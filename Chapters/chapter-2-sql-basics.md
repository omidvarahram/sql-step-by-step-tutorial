Sure, you can add another level to the mapping by using a nested `Map`. This way, you can map the promises based on both `appId` and `message.type`. Here's how you can modify your class to include this additional level:

### Modified Class

```typescript
type DataPromise<DATA> = {
  resolve: (data: DATA) => void;
  reject: (reason?: any) => void;
};

export class AndroidSecureChannelEventBus<PAYLOAD, DATA> implements MobileSecureChannelEventBus<PAYLOAD, DATA> {
  private messageEventDataObjectKey = 'channelName';
  private messagePort: MessagePort;
  private dataPromises: Map<string, Map<string, DataPromise<DATA>>> = new Map();

  constructor() {
    // Ensure the event listener is added to the window object
    if (typeof window !== "undefined") {
      window.addEventListener('message', this.eventHandler.bind(this), false);
    }
  }

  public postMessageSecurely(message: MobileSecureEventMessage<PAYLOAD>, appId: string): Promise<DATA> {
    // Check if the message port is initialized
    if (!this.messagePort) {
      return Promise.reject(new Error('AndroidSecureChannelEventBus: Unable to post message: Message port not initialised'));
    }

    return new Promise<DATA>((resolve, reject) => {
      // Ensure there is a map for the appId
      if (!this.dataPromises.has(appId)) {
        this.dataPromises.set(appId, new Map<string, DataPromise<DATA>>());
      }

      // Store the resolve and reject functions in the nested map
      const appPromises = this.dataPromises.get(appId);
      appPromises.set(message.type, { resolve, reject });

      try {
        // Post the message
        this.messagePort.postMessage(JSON.stringify(message));
      } catch (error) {
        // If there is an error, remove the promise and reject
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
    // Check if the event contains the required key to initialize the port
    if (eventData && Object.keys(eventData).includes(this.messageEventDataObjectKey)) {
      const port = messageEvent.ports[0];
      this.messagePort = port;
      console.info('AndroidSecureChannelEventBus: Message port initialised');
    }

    // Extract the data from the event
    const data = this.getEventData(messageEvent);
    if (data && data.appId && data.type) {
      // Retrieve the nested map for the appId
      const appPromises = this.dataPromises.get(data.appId);
      if (appPromises) {
        // Retrieve the promise corresponding to the message type
        const promise = appPromises.get(data.type);
        if (promise) {
          // Resolve the promise with the received data and remove it from the map
          promise.resolve(data);
          appPromises.delete(data.type);
        }
      }
    }
  }
}
```

### Explanation:

1. **Nested Map for Promises**: The `dataPromises` map now contains another `Map` for each `appId`. This nested map stores promises based on `message.type`.
2. **`postMessageSecurely` Method**: This method now accepts an additional `appId` parameter. It ensures that there is a map for the given `appId` and then stores the promise using `message.type`.
3. **`eventHandler` Method**: This method now checks for both `data.appId` and `data.type` when resolving promises. It retrieves the nested map for the `appId` and then the promise for the `message.type`.

By using this nested map approach, you can map promises based on both `appId` and `message.type`, ensuring more precise handling of asynchronous responses. If you have further requirements or need more details, feel free to ask!
