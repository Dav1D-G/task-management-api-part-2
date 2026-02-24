# Q&A ? Jenkins Part 2

## 1) Why should builds not run on the Jenkins controller?

- The controller is responsible for orchestration and stability.
- Running builds there can degrade performance and security.
- It reduces scalability and increases the blast radius of failures.

## 2) What advantages do Docker-based build environments provide?

- Consistent, reproducible build environments
- Isolation between builds
- Easy cleanup and fast provisioning

## 3) What would happen if multiple builds used the same global environment?

- Builds can interfere with each other
- Dependency conflicts and flaky results
- Harder debugging and non-deterministic outcomes

## 4) Why is credential isolation important in CI systems?

- Prevents secret leaks in logs or code
- Limits access only to jobs that need the secret
- Supports auditing and least-privilege policies

## 5) How does introducing agents improve scalability?

- Jobs can run in parallel across multiple machines
- Workloads are distributed from the controller
- Teams can scale build capacity horizontally

