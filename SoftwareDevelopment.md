# Software development

DBCA software developers are encouraged to undertake development locally in an environment that is as close as possible to the final deployed application environment in order to reduce the incidence of "works on my machine" problems.

The department's standard operating environment (SOE) is currently Microsoft Windows 11, so the majority of DBCA developers are assumed to be carrying out development within this operating system and the following advice is written with this in mind. The one "essential" development tool is **[Git](https://git-scm.com/)**, which is our designated version control software solution.
Refer to the [Git](/developer-guidance/Git) page for more information about installing and using Git. Development tools are generally expected to be installed via Company Portal.

## Development IDE

The software development IDE is a matter of preference for the individual developer. Microsoft's development IDE [Visual Studio Code](https://code.visualstudio.com/) is a good choice and is available for install on Company Portal. It is free to use, well documented, well-supported and highly configurable. It also plays extremely well with WSL (see below) via an extension.

## Web development stack

To summarise the OIM-recommended technology "stack" for new web development projects:

- [Python](https://www.python.org/) - programming/scripting language
- [Django](https://www.djangoproject.com/) - Web application framework
- PostgreSQL - relational database
- [Git](https://git-scm.com/) - project code source control
- [Docker](https://www.docker.com/) - software containerisation

This selection of technologies is not prescriptive, but represents a proven, reliable and well-supported set of options that are used extensively for existing systems.

## Python

[Python](https://www.python.org/doc/essays/blurb/) is a general purpose programming language which is dynamically typed, interpreted, and known for its easy readability. General principles to be aware of when undertaking development of applications using Python:

- Python 3 - New projects should _always_ be developed against a recent version of Python 3. Notwithstanding the similarities, Python 2 is no longer maintained for the purposes of features or security.
- Correct tool for the job - developers should be mindful of using Python where its strengths are greatest (rapid prototyping, versatility, portability, maintainability) versus other tools that may have advantages (SQL for database queries, command-line tools for file/text handling, JavaScript for DOM manipulation, etc.)

For installation, most \*nix operating systems come with a version of Python installed which will probably be sufficient for learning. For usage instructions, see [this page](https://docs.python.org/3/using/index.html). For installation on Windows, see [this page](https://docs.python.org/3/using/windows.html).

A suggested syllabus for learning Python for the purposes of web application development is as follows:

- Install Python 3.x on your PC.
- If required, take a [crash course in the Python syntax](https://www.freecodecamp.org/news/learning-python-from-zero-to-hero-120ea540b567).
- For a more thorough overview of Python syntax, read the [Dive Into Python ebook](https://diveintopython3.net/), chapters 0, 1, 2, 3, 4, 7, 11 & 14 (the whole book is valuable, but these chapters will get you basically productive).

### Python environment management

Once you start working on more than one project, managing separate and isolated Python environments for each one becomes a requirement. [This is a good primer](https://realpython.com/python-virtual-environments-a-primer/) on Python virtual environments and why you need them.

There are many different Python environment management tools on the market (pip, pip-tools, Poetry, Pipenv, etc.) and the choice always remain with individual developer, but the current recommended tool is [uv](https://docs.astral.sh/uv/). It's fast, easy to use, and is Python-standards-compliant. First steps are as follows:

- Install [uv](https://docs.astral.sh/uv/getting-started/installation/) on your development PC.
- [Install a specific version of Python using uv](https://docs.astral.sh/uv/guides/install-python/).
- [Create an isolated Python environment](https://docs.astral.sh/uv/pip/environments/) and begin experimenting with it.

## Django

[Django](https://www.djangoproject.com/) is our recommended framework of choice for non-trivial web applications requiring a significant amount of business logic, direct manipulation of databases, and a large amount of user interaction. It is a "full stack" web framework that includes all of the components required to develop a complex web application for internal or external customers. It also boasts some of the best online documentation to be found.

A learning syllabus for Django is as follows:

- Once you have a good understanding of Python syntax, complete the ENTIRE Django tutorial (really, the whole thing): <https://docs.djangoproject.com/en/dev/>
- As an alternative/supplement to the Django docs tutorial, Mozilla has an excellent tutorial series for Django: <https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django>

Examples of DBCA-built web applications using Django:

- [Planning Referral System](https://prs.dbca.wa.gov.au) - code repository at <https://github.com/dbca-wa/prs>
- [IBMS](https://ibms.dbca.wa.gov.au) - code repository at <https://github.com/dbca-wa/ibms>

To minimise development effort, we typically take the following approach when building a new system:

1. Define a good data model with constraints: [Django models](https://docs.djangoproject.com/en/dev/ref/models/fields/).
1. Add validation for [model fields](https://docs.djangoproject.com/en/dev/ref/models/instances/#validating-objects).
1. Use the built-in [Django admin](https://docs.djangoproject.com/en/dev/ref/contrib/admin/) for testing and validation of the data model.

## Git

The usage of Git for version control is covered in detail on this page: [Git](/developer-guidance/Git)

The department makes use of the [dbca-wa GitHub organisation](https://github.com/dbca-wa) for code management. DBCA developers should create a GitHub account and request OIM for an invitation to the organisation.

## Docker

The department's preferred method of serving bespoke web services is as containerised [Docker](https://www.docker.com/) images and deployed using [Kubernetes](https://kubernetes.io/docs/home/). Therefore, is it useful for developers to install and run Docker locally. A sensible approach is to install and run Docker inside a WSL development environment. A guide for installing Docker inside the Ubuntu WSL is available [here](https://dev.to/bartr/install-docker-on-windows-subsystem-for-linux-v2-ubuntu-5dl7) (includes instructions for installing the Azure CLI and dotnet core).

We maintain a document that may serve as an introduction for developers to start learning about Docker here: [Docker](/Docker.md).

Note that production containerised systems are served in a managed Azure Kubernetes (AKS) cluster. Additional external resources for learning about Kubernetes are listed below:

- [Kubernetes Crash Course](https://canine.gitbook.io/canine.sh/technical-details/kubernetes-crash-course/introduction) - an extremely approachable and clear introduction to Kubernetes concepts.
- [Introduction to Kubernetes on Azure](https://docs.microsoft.com/en-us/learn/paths/intro-to-kubernetes-on-azure/) - learning path from Microsoft. Pretty thorough introduction to Docker, containers and AKS in general.
- [Awesome Docker](https://github.com/veggiemonk/awesome-docker) - a curated list of resources related to Docker.

## Developing in Linux - Windows Subsystem for Linux (WSL)

Many of the department's bespoke systems are built and deployed against a Linux environment, therefore it is highly recommended for developers to carry out development locally within a similar environment.

Microsoft has made it surprisingly easy to run a Linux environment under Windows 10 and newer. [Windows Subsystem for Linux](https://docs.microsoft.com/en-gb/windows/wsl/about) is a modern alternative to a virtual machine as it runs natively on the local hardware, meaning that performance is rarely an issue. Installed WSL environments also run directly on the local filesystem, so all local files can be read and copied as required. You can happily run Virtual Studio Code in Windows with a project in a WSL environment for development, via the optional WSL extension.

For a good experience, OIM recommends that developers utilise [WSL](https://learn.microsoft.com/en-gb/windows/wsl/install) to install and run a recent (LTS) version of Ubuntu Server. This OS is free, has excellent technical support, up-to-date packages, and (importantly) has a vast corpus of documentation available. Some good resources for developers new to working in a Linux command-line environment are as follows:

- [Linux Tutorial for Beginners](https://ryanstutorials.net/linuxtutorial/)
- [Linux Command Reference](https://perpetualpc.net/srtd_commands_rev.html)

Aside from WSL, there's the option of using an emulator like VirtualBox to run a local Linux VM for development purposes. This is no longer a recommended approach.

## Software development best practices

The following ebook is a well-regarded and very readable collection of advice related to software development: [Andrew Hunt, David Thomas - The Pragmatic Programmer - your journey to mastery](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/)

## Information security

Information security is an important topic and relevant to all software developers. The department runs an Information Security Awareness Program (ISAP) training course that is compulsory for all staff. Even if the information seems basic, try to internalise the content and cultivate a security mindset.

Other infosec resources/advice:

- [1Password](https://1password.com/) - a service for managing and sharing organisational secrets within OIM.
- Keep sensitive information out of project repositories - don't commit user credentials, passwords, access keys or database dumps to the repository. Get in the habit of creating a `.gitignore` file and adding relevant file patterns to it in order to reduce the chance of this happening.
- In the case of credentials or keys, the recommended approach is to make your settings/config code read these pieces of information in via **environment variables**.
