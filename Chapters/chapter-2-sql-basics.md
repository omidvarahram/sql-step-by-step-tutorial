Yes, you can achieve similar functionality using generator functions (yield) in conjunction with promises. This approach allows you to create a sequence of asynchronous operations that can be paused and resumed, making it easier to manage complex asynchronous workflows.

Here's how you can modify your `AndroidSecureChannelEventBus` class to use generator functions with yield:

### Updated Code

```typescript
type DataPromise<DATA> = {
  resolve: (data: DATA) => void;
  reject: (reason?: any) => void;
};

export class AndroidSecureChannelEventBus<PAYLOAD, DATA> implements MobileSecureChannelEventBus<PAYLOAD, DATA> {
  private messageEventDataObjectKey = 'channelName';
  private messagePort: MessagePort;
  private dataPromises: Map<string, Map<string, DataPromise<DATA>>> = new Map();
  private isPortReady: boolean = false;
  private portReadyResolvers: (() => void)[] = [];

  constructor() {
    if (typeof window !== "undefined") {
      window.addEventListener('message', this.eventHandler.bind(this), false);
    }
  }

  public async asyncPostMessageCallback(message: MobileSecureEventMessage<PAYLOAD>, appId: string, timeoutMs: number = 5000): Promise<DATA> {
    return new Promise<DATA>((resolve, reject) => {
      const gen = this.postMessageSequence(message, appId, timeoutMs);

      const step = (result?: any) => {
        const { value, done } = gen.next(result);
        if (done) {
          if (value instanceof Error) {
            reject(value);
          } else {
            resolve(value);
          }
        } else if (value instanceof Promise) {
          value.then(step).catch(err => step(Promise.reject(err)));
        }
      };

      step();
    });
  }

  private *postMessageSequence(message: MobileSecureEventMessage<PAYLOAD>, appId: string, timeoutMs: number): Generator<Promise<any>, DATA | Error, any> {
    try {
      yield this.waitForPortReady(timeoutMs);
      const data = yield this.postMessageSecurely(message, appId);
      return data;
    } catch (error) {
      return new Error(error.message);
    }
  }

  private waitForPortReady(timeoutMs: number): Promise<void> {
    if (this.isPortReady) {
      return Promise.resolve();
    }

    return new Promise<void>((resolve, reject) => {
      const timeoutId = setTimeout(() => {
        reject(new Error('Port not ready within the timeout period'));
      }, timeoutMs);

      this.portReadyResolvers.push(() => {
        clearTimeout(timeoutId);
        resolve();
      });
    });
  }

  private postMessageSecurely(message: MobileSecureEventMessage<PAYLOAD>, appId: string): Promise<DATA> {
    if (!this.messagePort) {
      return Promise.reject(new Error('AndroidSecureChannelEventBus: Unable to post message: Message port not initialised'));
    }

    return new Promise<DATA>((resolve, reject) => {
      if (!this.dataPromises.has(appId)) {
        this.dataPromises.set(appId, new Map<string, DataPromise<DATA>>());
      }

      const appPromises = this.dataPromises.get(appId);
      appPromises.set(message.type, {
        resolve: data => resolve(data),
        reject: error => reject(error)
      });

      try {
        this.messagePort.postMessage(JSON.stringify(message));
      } catch (error) {
        appPromises.delete(message.type);
        reject(new Error('AndroidSecureChannelEventBus: Unable to post message: Message port failed'));
      }
    });
  }

  private getEventData(messageEvent: MessageEvent): any {
    if (messageEvent.data && typeof messageEvent.data === 'string') {
      try {
        return JSON.parse(messageEvent.data);
      } catch (e) {
        console.warn('AndroidAuthEventBus: Unexpected message received');
      }
    }
    return null;
  }

  private eventHandler(messageEvent: MessageEvent): void {
    const eventData = this.getEventData(messageEvent);
    if (eventData && Object.keys(eventData).includes(this.messageEventDataObjectKey)) {
      const port = messageEvent.ports[0];
      this.messagePort = port;
      this.isPortReady = true;
      this.portReadyResolvers.forEach(resolve => resolve());
      this.portReadyResolvers = [];
      console.info('AndroidSecureChannelEventBus: Message port initialised');
    }

    const data = this.getEventData(messageEvent);
    if (data && data.appId && data.type) {
      const appPromises = this.dataPromises.get(data.appId);
      if (appPromises) {
        const promise = appPromises.get(data.type);
        if (promise) {
          promise.resolve(data);
          appPromises.delete(data.type);
        }
      }
    }
  }
}
```

### Explanation:

1. **Generator Function**: `postMessageSequence` is a generator function that sequences the asynchronous operations. It uses `yield` to wait for promises to resolve.
2. **waitForPortReady Method**: This method returns a promise that resolves when the port is ready or rejects if it times out.
3. **Port Ready Resolvers**: Maintains a list of resolvers that are called when the port becomes ready. This allows multiple calls to `waitForPortReady` to be resolved simultaneously.
4. **Async Callback**: `asyncPostMessageCallback` wraps the generator function in a promise, stepping through each yield value and handling promises accordingly.
5. **Event Handler**: Updates `isPortReady` and resolves any waiting promises when the port is ready.

### Usage

Here’s an example of how you can consume the `asyncPostMessageCallback` method:

```typescript
const eventBus = new AndroidSecureChannelEventBus();

const message = {
  type: 'REQUEST_INIT_CONFIG',
  payload: { appId: 'exampleAppId' }
};

eventBus.asyncPostMessageCallback(message, 'exampleAppId')
  .then(data => {
    console.log('Received data:', data);
    // Initialize the app with the received data
  })
  .catch(err => {
    console.error('Error:', err);
    // Handle the error (e.g., show a message to the user)
  });
```

This setup uses generator functions with `yield` to manage asynchronous flow and state changes, providing an alternative to RxJS observables.
