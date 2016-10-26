# OneConfig
## Overview
Realtime configuration of applications has become increasingly popular. A modifiable configuration system allows app developers to tweak the constraints of their application while the app is in production, eliminating the need for an update to change small things. A universal remote app config also allows programmers to abstract envionment variables in a centralized location separate from their backend. Services like Heroku provide a field to easily add and modify environment variables on the go. However, this creates issues when migrating services between hosting providers. 

In a concrete example, Who Quote was created in a hackathon, and soon after it was demoed, it went offline. The server code is still there, but if the app were in the appstore (given the current architecture), it would require an update to redirect the app to a new host. With OneConfig, an admin could simply change the value on a key-value store to redirect the app to a separate host.

## Example Admin Usage
`$ oneconfig login schrismartin`

`$ oneconfig --new "WhoQuote"`

`$ oneconfig --app "WhoQuote" --key "prod-host" --value "392:391:932:834"`

## Example iOS Implementation
### Singleton Class
```swift
class OneConfig {

	// Create private setters with public getters for environment variables
	private var _config: Dictionary<String, Any>
	internal var config: Dictonary<String, Any> {
		return _config
	}
	
	// Create singleton instance of OneConfig
	private let sharedInstance = OneConfig()
	
	public static func initialize(applicationId: String, clientId: String) {
		// 1. Register application with the server
		// 2. Create pubsub to watch for changes on the server, 
		//    modifying _config as necessary.
	}
}

final class WQOneConfig: OneConfig {
	override static func initialize(applicationId: ApplicationSettings, clientId: ApplicationSettings) {
		super.initialize(applicationId: applicationId.rawValue, clientId: clientId.rawValue)
	}
}
```
### App Delegate
```swift
enum ApplicationSettings: String {
	case myAppId = "2511e44acbaf411b8c254892d5b07d5a"
	case myClientId = "8662f240528a754647da26f66d2ca294"
}

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey : Any]? = nil) -> Bool {
        
        WQOneConfig.initialize(applicationId: .myAppId, clientId: .myClientId)
        
        return true
    }
    
    // Rest of AppDelegate...
    
}
```

### View Controller
```swift
class MyViewController {

	var configVariable: Any {
		return WQOneObject.sharedInstance.config["configVariable"]
	}
	
	// ...

}
```