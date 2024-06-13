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
