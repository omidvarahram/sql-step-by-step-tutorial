Certainly! Here is a complete example of unit tests for the provided functions using Jest:

```javascript
import { setCookie, readCookie, storeInCache, retrieveFromCache } from './cookies';

describe('cookies.js', () => {
  // Setup for mocking the cookies
  const mockCookie = {};
  beforeEach(() => {
    Object.defineProperty(document, 'cookie', {
      writable: true,
      value: {
        get: jest.fn().mockImplementation(() => Object.entries(mockCookie).map(e => e.join('=')).join('; ')),
        set: jest.fn().mockImplementation((cookie) => {
          const [name, value] = cookie.split('=');
          mockCookie[name] = value.split(';')[0];
        })
      }
    });
  });

  afterEach(() => {
    document.cookie = jest.fn(); // Clear the mock after each test
    jest.restoreAllMocks();
  });

  describe('setCookie', () => {
    it('should set a cookie', () => {
      setCookie('test', '123');
      expect(document.cookie).toContain('test=123');
    });
  });

  describe('readCookie', () => {
    it('should return a cookie value if it exists', () => {
      mockCookie['user'] = 'John Doe';
      const cookieValue = readCookie('user');
      expect(cookieValue).toEqual('John Doe');
    });

    it('should return an empty string if the cookie does not exist', () => {
      const cookieValue = readCookie('non_existent_cookie');
      expect(cookieValue).toEqual('');
    });
  });

  // Mocking the CacheStorage API
  const cacheStorageMock = {
    open: jest.fn().mockResolvedValue({
      put: jest.fn().mockResolvedValue(undefined),
      match: jest.fn().mockResolvedValue({
        text: jest.fn().mockResolvedValue('cached_data')
      })
    })
  };

  global.caches = cacheStorageMock;

  describe('storeInCache', () => {
    it('should store data in cache and return true', async () => {
      const result = await storeInCache('testKey', 'testData');
      expect(cacheStorageMock.open).toHaveBeenCalledWith('cache-storage');
      expect(result).toBeTruthy();
    });

    it('should handle the error and return false when an exception occurs', async () => {
      cacheStorageMock.open.mockRejectedValueOnce(new Error('Failed to open cache'));
      const result = await storeInCache('testKey', 'testData');
      expect(result).toBeFalsy();
    });
  });

  describe('retrieveFromCache', () => {
    it('should retrieve data from cache if the key exists', async () => {
      const data = await retrieveFromCache('testKey');
      expect(cacheStorageMock.open).toHaveBeenCalledWith('cache-storage');
      expect(data).toEqual('cached_data');
    });

    it('should return an empty string if the key does not exist', async () => {
      cacheStorageMock.open.mockResolvedValue({
        match: jest.fn().mockResolvedValue(null)
      });
      const data = await retrieveFromCache('nonExistentKey');
      expect(data).toEqual('');
    });
  });
});
```

In these tests, `document.cookie` and the `caches` global are mocked to allow us to test the behavior of the `setCookie`, `readCookie`, `storeInCache`, and `retrieveFromCache` functions.

The tests for `setCookie` and `readCookie` manipulate the `mockCookie` object which simulates the browser's cookie storage. The `storeInCache` and `retrieveFromCache` tests rely on the `cacheStorageMock` object to simulate the Cache Storage API.

These tests should give you a fairly comprehensive coverage for the provided functions, though depending on your exact environment and the implementation details of these functions, you might need to adjust the mocks and assertions to match.
