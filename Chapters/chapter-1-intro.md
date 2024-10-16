```
describe('AndroidSecureEventBus integration flow', () => {
  let eventBus: AndroidSecureEventBus<any, any>;
  let mockMessagePort: any;

  beforeEach(() => {
    // Mock the global window.addEventListener
    jest.spyOn(window, 'addEventListener').mockImplementation((event, callback) => {
      if (event === 'message') {
        global.messageHandler = callback; // Store the callback to manually trigger later
      }
    });

    // Mock the messagePort with a mock onmessage handler
    mockMessagePort = {
      postMessage: jest.fn(),
      onmessage: jest.fn(),
    };

    // Create a new instance of AndroidSecureEventBus
    eventBus = new AndroidSecureEventBus();
  });

  test('should set messagePort on the first message event and add data to dataPromises on the second message', () => {
    // Step 1: Simulate the first message event that sets the messagePort
    const firstMessageEvent = {
      data: { webViewContainerSecureMessagePort: true },
      ports: [mockMessagePort], // Pass the mockMessagePort
    } as MessageEvent;

    // Trigger the first message event using the stored global handler
    global.messageHandler(firstMessageEvent);

    // Check that the messagePort was set
    expect(eventBus.messagePort).toEqual(mockMessagePort);

    // Step 2: Now, simulate the second message that would trigger messagePort.onmessage
    const secondMessageData = JSON.stringify({ appId: 'test-app', type: 'test-type' });
    const secondMessageEvent = {
      data: secondMessageData,
    } as MessageEvent;

    // Trigger the onmessage manually
    mockMessagePort.onmessage(secondMessageEvent);

    // Check if the data was added to dataPromises correctly
    const expectedKey = 'test-app::test-type';
    expect(eventBus['dataPromises'].get(expectedKey)).toEqual(JSON.parse(secondMessageData));
  });

  test('should not set messagePort if messageEventDataObjectKey is not present', () => {
    // Step 1: Simulate the first message event without the required key
    const invalidMessageEvent = {
      data: { someOtherKey: true }, // Missing webViewContainerSecureMessagePort
      ports: [mockMessagePort], // Pass the mockMessagePort
    } as MessageEvent;

    // Trigger the first message event using the stored global handler
    global.messageHandler(invalidMessageEvent);

    // Check that the messagePort was NOT set
    expect(eventBus.messagePort).toBeNull();
  });

  test('should log warning on invalid JSON in messagePort.onmessage', () => {
    // Step 1: Simulate the first message event to set messagePort
    const firstMessageEvent = {
      data: { webViewContainerSecureMessagePort: true },
      ports: [mockMessagePort], // Pass the mockMessagePort
    } as MessageEvent;

    // Trigger the first message event
    global.messageHandler(firstMessageEvent);

    // Step 2: Now simulate an invalid JSON string in the onmessage event
    const invalidMessageEvent = {
      data: 'invalid-json-string',
    } as MessageEvent;

    const warnSpy = jest.spyOn(console, 'warn').mockImplementation();

    // Trigger the onmessage manually with invalid data
    mockMessagePort.onmessage(invalidMessageEvent);

    // Verify that the warning is logged
    expect(warnSpy).toHaveBeenCalledWith(
      '[SecureEventBus][AndroidAuthEventBus]: Unexpected message received'
    );
  });
});
```
