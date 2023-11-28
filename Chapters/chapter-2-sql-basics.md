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
