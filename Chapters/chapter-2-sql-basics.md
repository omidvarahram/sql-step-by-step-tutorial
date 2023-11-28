In the `CONTRIBUTING.md` file of this repository, we aim to provide a comprehensive guide for both new members and external teams looking to contribute to our project. The intention of this document is to establish clear and consistent guidelines for contributions, ensuring that all collaborators are aligned with the project's goals, standards, and practices. This file serves as a vital reference, detailing the processes for submitting changes, reporting issues, and proposing new features. It also outlines our code of conduct, emphasizing respect, inclusivity, and collaboration. By following these guidelines, contributors can effectively and efficiently participate in the development of the project, fostering a positive and productive community environment.
## Branching Strategy, Pull Requests, and Forking

### Branching Strategy
Our project employs a specific branching strategy to streamline the development and release process. Contributors should create branches with a `feature-release` prefix, branching off from the `develop` branch. This is the primary branch for integrating new features or fixes. For detailed information on our branching strategy, please refer to our [Branching Strategy Documentation](#placeholder-link).

### Creating Feature and Fix Branches
- **Feature Branches**: When developing new features, create a branch with the prefix `feature` from the `feature-release` branch.
- **Fix Branches**: For bug fixes, use the prefix `fix` for branches, also branching from `feature-release`.

### Pull Requests (PRs)
1. **Creating PRs**: Once development on a `feature` or `fix` branch is complete, create a PR against the `feature-release` branch.
2. **Review and Approval**: Each PR requires a minimum of two approvals, including at least one from the code owners, to be merged into the `feature-release` branch.
3. **Merging to `feature-release`**: Merging a PR into the `feature-release` branch generates an alpha version of the application.
4. **PR from `feature-release` to `develop`**: After merging into `feature-release`, create another PR to merge the changes into the `develop` branch. Merging into `develop` creates a beta version of the application.
5. **Final PR to `master`**: To release a production version, create a PR from `develop` to `master`. This step signifies a new release of the application.

### Forking and Automated Pipeline Handling
External teams are encouraged to fork the repository for their development purposes. Our automated pipelines are designed to seamlessly identify PRs originating from forked repositories. These pipelines will automatically handle the process of raising issues and managing merges, given the proper approvals are in place. This integration ensures a smooth workflow and maintains the integrity and continuity of the project development, even with contributions from various external sources.

### Approval Process
All merging steps in this workflow require a minimum of two approvals, with at least one approval from a code owner, to ensure quality and adherence to project standards.

For more detailed instructions and guidelines, please refer to our comprehensive [Branching Strategy Documentation](#placeholder-link).

### Release Process Overview

The release process in our project is a structured and automated sequence, designed to ensure a smooth transition of code from development to production. This process involves the creation of alpha and beta versions, leading up to a final release. We adhere to semantic versioning principles, and the entire process is fully automated to maintain consistency and efficiency.

#### Alpha Version Creation
- **Branching for Alpha**: Developers create feature or fix branches with appropriate prefixes (`feature` or `fix`) from the `feature-release` branch.
- **Merging to `feature-release`**: Upon completion, these branches are merged into the `feature-release` branch through a pull request. This merge generates an alpha version of the application.
- **Automated Versioning**: Our automated systems automatically assign a semantic version number to this alpha release, reflecting the nature and extent of changes made.

#### Beta Version Creation
- **PR to `develop`**: After the alpha version is stable, a pull request is created to merge the `feature-release` branch into the `develop` branch.
- **Merging and Beta Version**: Merging into the `develop` branch results in the creation of a beta version. This version is meant for broader testing and final adjustments before the production release.
- **Automated Beta Versioning**: Similar to the alpha version, the beta version is automatically assigned a semantic version number, indicating its stage in the release process.

#### Final Release
- **PR from `develop` to `master`**: The final step is a pull request from the `develop` branch to the `master` branch. This PR signifies the readiness of the code for production.
- **Production Release**: Merging this PR into the `master` branch triggers the release of the new production version of the application.
- **Automated Semantic Versioning**: The release to the `master` branch is automatically assigned a final semantic version number, indicating its official release status.

#### Summary
Throughout this process, each step is carefully automated to align with semantic versioning. This automation ensures that version numbers accurately reflect the changes in each release, be it an alpha, beta, or final production release. The emphasis on automation not only streamlines the release process but also minimizes human error, ensuring a reliable and consistent release workflow.

Our iOS development process prioritizes practices that ensure clean, efficient, and maintainable code. These guidelines are crucial for developing high-quality, robust, and user-friendly applications.

#### Code Structure and Organization
- **Immutability Over Mutability**: Prefer immutable objects to mutable ones for their predictability and safety in concurrent environments.
- **Small Classes and Methods**: Opt for smaller, more focused classes and methods rather than larger, more complex ones. This approach enhances readability and maintainability.
- **Single Responsibility**: Each class should have a single responsibility and not be overloaded with multiple functionalities.
- **Follow Established Patterns**: Adhere to existing design patterns and coding conventions of the project. Introduce new patterns only when existing ones are insufficient or problematic.
- **Innovative Solutions**: While following established patterns, always be open to designing better solutions where applicable, enhancing the project's efficiency and effectiveness.

#### Clean Code Principles
- **Simplicity Over Complexity**: Prioritize simple code over more complex or "fancy" coding techniques. Simple code is usually more readable, maintainable, and less prone to errors.
- **Refactoring**: Regularly refactor the code to improve its structure, remove redundancies, and keep the codebase efficient and clean.
- **Documentation and Comments**: Document external interfaces thoroughly. Ensure that any public API or interface is well-documented to facilitate ease of use and understanding.

#### Performance Optimization
- **Memory Management**: Efficiently manage memory, particularly focusing on the proper use of Automatic Reference Counting (ARC) to avoid memory leaks and retain cycles.
- **Efficient Networking and UI Responsiveness**: Optimize network calls and maintain UI responsiveness. Perform intensive tasks in the background and update the UI on the main thread.

#### Testing and Quality Assurance
- **Unit and UI Testing**: Conduct comprehensive unit and UI tests. High test coverage helps in early identification and resolution of issues.
- **Continuous Integration (CI)**: Implement a CI system to automatically build and test the application with every commit, ensuring consistent code quality and functionality.

#### Security Best Practices
- **Data Protection and Error Handling**: Ensure robust data protection measures and error handling practices are in place to prevent crashes and safeguard against vulnerabilities.

By adhering to these best practices, our team aims to create iOS applications that are not just functional but also exemplify high standards of software craftsmanship.


### Testing

Effective testing is a cornerstone of our development process. It ensures that our code is reliable and maintainable. Below are the guidelines for testing within our project:

#### Test-Driven Development
- **Test While Developing**: Write tests concurrently while developing a class to ensure each new piece of code functions as expected.
- **Push Code with Tests**: Only push code that includes corresponding tests, demonstrating that the new functionality works and does not break existing features.
- **Question Pull Requests Without Tests**: Scrutinize and question any pull requests that do not include tests. Every change in the codebase should be verifiable through tests.

#### Designing Testable Code
- **Dependency Injection**: Use dependency injection over creating dependencies within the class to make classes easier to test.
- **Single Responsibility**: Develop classes that are easy to test by ensuring they have a single responsibility and do not mix concerns.

#### Maintaining a Healthy Build
- **Keep the Build Green**: Always maintain a passing build. If the build breaks, fixing it should be the team’s top priority.
- **Unit Test Non-View Classes**: Write unit tests for all public methods in classes that are not directly tied to the UI.
- **Integration Tests**: Integration test the public interface of each component or kit to ensure parts of the system work together as expected.
- **UI Tests**: Perform UI tests on the application to verify the user interface behaves correctly under various scenarios.

#### Test Characteristics
- **Focused Tests**: Write tests that are focused on small pieces of functionality and avoid testing multiple things at once.
- **Fast Tests**: Strive for tests that run quickly to support rapid development cycles.
- **Reliable Tests**: Ensure tests are consistent and do not have flaky behavior.
- **Full Test Suite**: Run the full test suite from the command line before creating pull requests to ensure all tests pass.

#### Continuous Testing
- **Real Device Testing**: Periodically confirm functionality on a real device, not just simulators or emulators.
- **Test Public Interfaces**: Test the public interface, including those defined in protocols that are implemented, to ensure they meet the contract they advertise.

#### Structuring Tests
- **Given/When/Then**: Use the Given/When/Then structure in your tests to designate the setup, method under test, and the expectations respectively.
- **Use Fixtures**: Utilize fixtures in your tests to factor out the creation of incidental model objects, which helps in reducing redundancy and focusing on the test subject.

By following these testing practices, we can ensure that our codebase remains robust, flexible, and error-free.


#ios

# iOS Development Principles

This document outlines the principles of iOS development within our organization. It aims to codify the often verbally agreed standards into a clear set of guidelines. As each project can be quite different, this document draws a line in the sand around some obvious and not-so-obvious principles that are crucial for the consistency and quality of our code.

## Framework Rules

- Do not import the React Native module into any Swift code.
- Do not import any React Native headers into a public Objective-C header.

Importing React Native in such a way will expose the modules from the framework and force consuming apps to link against React Native, which is against our architecture principles.

## Development Best Practices

We prefer the following:

- Immutable over mutable to ensure thread safety and predictability.
- Small classes/methods over larger ones to maintain readability and manageability.
- Classes with a single responsibility, as defined by the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), over classes that perform multiple tasks.
- Simple view controllers over view controllers with complex logic, view creation, API callbacks, etc.
- Organized code within the source files, Xcode project, and filesystem to maintain clarity and ease of navigation.
- Following existing patterns in our project over introducing new ones, unless the existing pattern is causing significant issues.
- Designing better solutions where applicable and adopting them over solutions that are no longer appropriate.
- Simple code over fancy code to keep the codebase maintainable and straightforward.
- Refactoring common code over duplicating business logic to promote DRY (Don't Repeat Yourself) principles.
- Methods and variables that self-describe over using comments to explain functionality.
- Documented external interfaces to ensure that any public API or interface is well-understood and easy to use.
- Leaving the code in a better state than when it was found, adhering to the "boy scout rule".
- No warnings in Xcode or when building on the command line to maintain a clean and warning-free build environment.

## Testing

- Use breakpoints to catch errors during development.
- Write tests while developing a class and push code that has a test.
- Question pull requests that do not include tests.

## Objective-C Specific Guidelines

- Follow Cocoa conventions as per Apple's guidelines.
- Use generics on collections like `NSArray<Foo *>` to specify contained types.
- Use named and meaningful constants in Apple’s Hungarian notation (e.g., `kPortfolioSectionHeight`) instead of magic/random numbers and strings.
- Annotate headers with nullability specifiers to indicate whether a method can return `nil`.
- Order methods and properties in a standard way for consistency.
- Avoid using `[SomeClass new]` as a rule since it hides errors when `init` is unavailable or deprecated. Use `[[SomeClass alloc] init]` always.
- Use `NSUInteger` instead of `NSInteger` where applicable, considering the range of possible values for the types you use.

## Swift Specific Guidelines

```swift
// Preferred way of defining a constant in Swift following Hungarian notation
let kMaximumNumberOfItems = 50

// Example of a small, single-responsibility class
class ItemFetcher {
    func fetchItems(completion: ([Item]) -> Void) {
        // Fetch logic here
    }
}

// Immutable example
let items: [Item] = fetchItems()React Native Specific GuidelinesUse React Native components in a way that they remain encapsulated within the React Native environment and do not leak into the native codebase.BuildingPrefix all ruby commands with bundle exec to ensure the use of the correct environment.By following these principles, we aim to create iOS applications that are not just functional but exemplary in terms of code quality and architecture.
