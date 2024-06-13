To address the issue where the `postMessage` sends a message but the `dataPromise` is not updated immediately due to the delay in receiving the response, we need to ensure that `postMessageSecurely` waits for the promise to be resolved before returning the data. This can be achieved by returning the promise from `postMessageSecurely` and waiting for the event listener to update the data in the promise.

Here's the refactored version of the class:

```typescript
type DataPromise<DATA> = {
  resolve: (data: DATA) => void;
  reject: (reason?: any) => void;
};

export class AndroidSecureChannelEventBus<PAYLOAD, DATA> implements MobileSecureChannelEventBus<PAYLOAD, DATA> {
  private messageEventDataObjectKey = 'channelName';
  private messagePort: MessagePort;
  private dataPromises: Map<string, DataPromise<DATA>> = new Map();

  constructor() {
    if (typeof window !== "undefined") {
      window.addEventListener('message', this.eventHandler.bind(this), false);
    }
  }

  public postMessageSecurely(message: MobileSecureEventMessage<PAYLOAD>): Promise<DATA> {
    if (!this.messagePort) {
      return Promise.reject(new Error('AndroidSecureChannelEventBus: Unable to post message: Message port not initialised'));
    }

    return new Promise<DATA>((resolve, reject) => {
      // Store the resolve and reject functions in the map
      this.dataPromises.set(message.type, { resolve, reject });

      try {
        this.messagePort.postMessage(JSON.stringify(message));
      } catch (error) {
        this.dataPromises.delete(message.type);
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

  private eventHandler(messageEvent: MessageEvent) {
    const eventData = this.getEventData(messageEvent);
    if (eventData && Object.keys(eventData).includes(this.messageEventDataObjectKey)) {
      const port = messageEvent.ports[0];
      this.messagePort = port;
      console.info('AndroidSecureChannelEventBus: Message port initialised');
    }

    const data = this.getEventData(messageEvent);
    if (data && data.type) {
      const promise = this.dataPromises.get(data.type);
      if (promise) {
        promise.resolve(data);
        this.dataPromises.delete(data.type);
      }
    }
  }
}
```

### Explanation of the Changes:

1. **Promise Handling in `postMessageSecurely`**: In `postMessageSecurely`, the method returns a new promise that is resolved when the event handler receives the response.
2. **Event Handler Logic**: The `eventHandler` listens for messages and resolves the corresponding promise from the `dataPromises` map when a message with the expected type is received.
3. **Ensuring the Port Initialization**: The constructor ensures that the event listener is bound correctly and that the message port is initialized properly.

With these changes, `postMessageSecurely` will wait for the listener to receive the response and resolve the promise, ensuring that the correct data is returned. This approach ensures that the function waits for the response before proceeding, addressing the delay issue between `postMessage` and the listener's update to `dataPromise`.
