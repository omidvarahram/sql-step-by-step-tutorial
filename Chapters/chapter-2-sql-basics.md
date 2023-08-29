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

    func parseAndValidateConfig(downloadedConfig: String, publicKey: String) -> Result<[String: Any], Error> {
        
        // Step 1: Parsing
        guard let data = downloadedConfig.data(using: .utf8) else {
            return .failure(ConfigError.stringToDataConversionFailed)
        }
        
        guard let jsonObject = try? JSONSerialization.jsonObject(with: data, options: []) as? [String: Any] else {
            return .failure(ConfigError.parsingFailed)
        }
        
        do {
            try validateConfig(jsonObject, publicKey: publicKey)
            return .success(jsonObject)
        } catch let error {
            return .failure(error)
        }
    }
    
    private func validateConfig(_ config: [String: Any], publicKey: String) throws {
        
        // Step 2: Check for mandatory fields
        guard let _ = config["appConfig"], let _ = config["signature"] else {
            throw ConfigError.mandatoryFieldsMissing
        }
        
        // Step 3: Validate types
        guard let _ = config["signature"] as? String, let _ = config["appConfig"] as? [String: Any] else {
            throw ConfigError.invalidFieldTypes
        }
        
        // Step 4: Verify signature
        let signature = config["signature"] as! String
        let isSignatureValid = AssetsValidator.verifySignature(signature: signature, publicKey: publicKey)
        if !isSignatureValid {
            throw ConfigError.signatureVerificationFailed
        }
    }
}

class AssetsValidator {
    static func verifySignature(signature: String, publicKey: String) -> Bool {
        // Your implementation for signature verification
        // Return true if valid, false otherwise.
    }
}
