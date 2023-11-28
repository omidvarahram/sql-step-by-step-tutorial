# iOS Development Principles

This is a place for our team to record the often verbally agreed principles of development. In an organization, each project can be quite different, and this is our way to draw a line in the sand around some obvious and not-so-obvious principles.

## We Prefer the Following...

- Immutable over mutable to ensure thread safety and predictability.
- Small classes/methods over larger ones to maintain readability and manageability.
- Classes that have a [single responsibility](https://en.wikipedia.org/wiki/Single_responsibility_principle) over classes that do multiple things.
- Following an existing pattern in our project over introducing a new one.
- Designing better solutions over accepting solutions that are no longer appropriate.
- Simple code over fancy code to keep the codebase maintainable and straightforward.
- Refactoring common code over duplicating business logic.
- Methods and variables that describe themselves over comments to explain.
- Documented external interfaces.
- Leaving code in a better state than when you found it.
- No warnings in Xcode or when building on the commandline.

## Framework Rules

- Do not import the React Native module into any Swift code.
- Do not import any React Native headers into a public Objective-C header.

Doing either of these will automatically expose the React Native modules from the framework and will force every consuming app to link against React Native, which will defeat the purpose of this framework.

## Swift Specific Guidelines

```swift
// Example of using immutable over mutable
let constantArray = ["A", "B", "C"]
var mutableArray = ["A", "B", "C"]
mutableArray.append("D") // This is discouraged in favor of immutability

// Example of a small, single-responsibility Swift class
class UserViewModel {
    private let userId: String
    
    init(userId: String) {
        self.userId = userId
    }
    
    func fetchUser(completion: (User) -> Void) {
        // Fetch user data
    }
}
```

## Objective-C Specific Guidelines

```objective-c
// Example of a small, single-responsibility Objective-C class
@interface UserFetcher : NSObject

- (void)fetchUserWithId:(NSString *)userId completion:(void (^)(User *))completion;

@end

@implementation UserFetcher

- (void)fetchUserWithId:(NSString *)userId completion:(void (^)(User *))completion {
    // Fetch user data
}

@end
```

## React Native Specific Guidelines

- Ensure that React Native components are used within the React Native environment and do not bleed into the native codebase.

By following these principles, we aim to create iOS applications that are not just functional but also exemplary in terms of code quality and architecture.


# Android Development Principles

This document serves as a guideline for the Android development team. It records the principles that are often discussed verbally, providing a reference to uphold our standards of development. We recognize that every project is unique, but it is essential to maintain a consistent approach across our organization.

## We Prefer the Following Practices

- **Immutable over Mutable**: Choose immutable objects where possible to avoid side effects and make concurrency simpler.

- **Small Classes/Methods Over Larger Ones**: Aim for classes and methods that are concise and focused on a single task. This promotes readability and maintainability.

- **Classes with a Single Responsibility**: Adhere to the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) to ensure that classes are only tasked with one concern, making them easier to understand and test.

- **Following Existing Patterns**: Unless there is a compelling reason to deviate, existing project patterns should be followed to maintain codebase consistency.

- **Designing Better Solutions**: Always strive for innovation and improvement over settling for outdated solutions.

- **Simple Code Over Fancy Code**: Opt for clarity and simplicity in your code rather than complex and less understandable solutions.

- **Refactoring Common Code**: Avoid duplicating logic by refactoring common code, adhering to the DRY principle.

- **Self-Describing Methods and Variables**: Write code that is self-explanatory, minimizing the need for comments.

- **Documented External Interfaces**: Ensure that any public API is well-documented for ease of use and understanding.

- **Improving the Codebase**: Always leave the code in a better state than you found it, following the "boy scout rule".

- **Zero Warnings**: Maintain a codebase with no warnings in Android Studio and when building from the command line.

## Code Examples

### Java

```java
// Example of an immutable class in Java
public final class User {
    private final String name;
    private final int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

### Kotlin

```kotlin
// Example of a small, single-responsibility class in Kotlin
class UserRepository(private val dataSource: UserDataSource) {
    fun getUser(userId: String): User {
        return dataSource.fetchUser(userId)
    }
}

// Utilizing data classes for immutable data structures
data class User(val id: String, val name: String)
```

### React Native

```javascript
// Example of following existing patterns in a React Native component
// This component follows the pattern of container components being responsible for data fetching
class UserContainer extends React.Component {
    state = {
        user: null,
    };

    componentDidMount() {
        fetchUser(this.props.userId)
            .then(user => this.setState({ user }))
            .catch(error => console.error(error));
    }

    render() {
        return this.state.user ? <UserComponent user={this.state.user} /> : null;
    }
}
```

By following these principles and exemplifying them through our code in Java, Kotlin, and React Native, we aim to create Android applications that are not only functional but also exemplify high standards of software craftsmanship.
 
