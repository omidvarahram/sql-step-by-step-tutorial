
Certainly! Here's a version that uses a custom `enum` for errors:

```swift
import Foundation

class AssetsLoading {

    // Error enum
    enum ConfigError: Error {
        case stringToDataConversionFailed
        case parsingFailed
        case mandatoryFieldsMissing
        case invalidFieldTypes
        case signatureVerificationFailed
        
        var localizedDescription: String {
            switch self {
            case .stringToDataConversionFailed:
                return "Failed to convert string to data."
            case .parsingFailed:
                return "Parsing failed."
            case .mandatoryFieldsMissing:
                return "Mandatory fields are missing."
            case .invalidFieldTypes:
                return "Invalid types for fields."
            case .signatureVerificationFailed:
                return "Signature verification failed."
            }
        }
    }

    func parseAndValidateConfig(downloadedConfig: String, publicKey: String) throws -> [String: Any] {
        
        // Step 1: Parsing
        guard let data = downloadedConfig.data(using: .utf8) else {
            throw ConfigError.stringToDataConversionFailed
        }
        
        guard let jsonObject = try? JSONSerialization.jsonObject(with: data, options: []) as? [String: Any] else {
            throw ConfigError.parsingFailed
        }
        
        // Step 2: Check for mandatory fields
        guard let _ = jsonObject["appConfig"], let _ = jsonObject["signature"] else {
            throw ConfigError.mandatoryFieldsMissing
        }
        
        // Step 3: Validate types
        guard let _ = jsonObject["signature"] as? String, let _ = jsonObject["appConfig"] as? [String: Any] else {
            throw ConfigError.invalidFieldTypes
        }
        
        // Step 4: Verify signature
        let signature = jsonObject["signature"] as! String
        let isSignatureValid = AssetsValidator.verifySignature(signature: signature, publicKey: publicKey)
        if !isSignatureValid {
            throw ConfigError.signatureVerificationFailed
        }
        
        return jsonObject
    }
}

class AssetsValidator {
    static func verifySignature(signature: String, publicKey: String) -> Bool {
        // Your implementation for signature verification
        // Return true if valid, false otherwise.
    }
}
```

The error-handling structure now uses an `enum`, making it cleaner and more modular.
