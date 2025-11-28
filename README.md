# DBCA developer guidance

This is a resource for software developers in DBCA, to provide guidance on development tools and processes. Individuals should feel free to skip elements for which they already have a good level of competency. This advice is (generally) not prescriptive, but is meant to provide a baseline level of guidance and advice relating to a variety of topics relevant to software development within DBCA.

- [Software development](/developer-guidance/SoftwareDevelopment) - guidance for software developers within DBCA.
- [DevOps best practices](/developer-guidance/DevOps) - design and operational best practices, mainly related to containerised applications.
- [AKS storage](/developer-guidance/AKSStorage) - considerations related to storage options within the AKS environment.

Additional resource documentation pages include the following:

- [Git](/developer-guidance/Git) - basic instructions and workflow for using Git.
- [Docker](/developer-guidance/Docker) - instructions for the use of Docker during development.
- [Rancher](/developer-guidance/Rancher) - usage of Rancher to manage Kubernetes resources.
- [Auth2](/developer-guidance/Auth2) - DBCA's bespoke internet single-sign-on (SSO) solution.
- [Kustomize](/developer-guidance/Kustomize) - declarative Kubernetes resource definitions.

## Project layout

This project is intended to be built and deployed as a static site via GitHub Pages. Create new pages in GitHub-flavoured Markdown files in the project root and they will be automatically built and deployed at <https://dbca-wa.github.io/developer-guidance/>.

## Contributions

This resource is intended as a living collaborative document. All DBCA developers are encouraged to submit pull requests to this repository with additions and corrections.
