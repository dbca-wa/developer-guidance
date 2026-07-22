# DBCA developer guidance

This is a resource for software developers in DBCA, to provide guidance on development tools and processes. Individuals should feel free to skip elements for which they already have a good level of competency. This advice is (generally) not prescriptive, but is meant to provide a baseline level of guidance and advice relating to a variety of topics relevant to software development within DBCA.

- [Software development]({{ site.baseurl }}/Software_Development) - guidance for software developers within DBCA.
- [DevOps best practices]({{ site.baseurl }}/DevOps) - design and operational best practices, mainly related to containerised applications.
- [AKS storage]({{ site.baseurl }}/AKS_Storage) - considerations related to storage options within the AKS environment.

Additional resource documentation pages include the following:

- [Git]({{ site.baseurl }}/Git) - basic instructions and workflow for using Git.
- [Docker]({{ site.baseurl }}/Docker) - instructions for the use of Docker during development.
- [Kubernetes]({{ site.baseurl }}/Kubernetes) - basic glossary of terms relating to Kubernetes, plus documentation references.
- [Rancher]({{ site.baseurl }}/Rancher) - usage of Rancher to manage Kubernetes resources.
- [Auth2]({{ site.baseurl }}/Auth2) - DBCA's bespoke internet single-sign-on (SSO) solution.
- [Kustomize]({{ site.baseurl }}/Kustomize) - declarative Kubernetes resource definitions.
- [React]({{ site.baseurl }}/React) - modern React development patterns and recommended tooling.
- [Containerisation learning path]({{ site.baseurl }}/Containerisation_Learning) - a syllabus for building knowledge from local Docker to production hosting in Kubernetes.
- [Python environment management]({{ site.baseurl }}/Python_environment_management) - recommendations relating to managing Python development environments.

## Project layout

This project is intended to be built and deployed as a static site via GitHub Pages. Create new pages in GitHub-flavoured Markdown files in the project root and they will be automatically built and deployed at <{{ site.url }}{{ site.baseurl }}>.

## Contributions

This resource is intended as a living collaborative document. All DBCA developers are encouraged to submit pull requests to this repository with additions, improvements and corrections.
