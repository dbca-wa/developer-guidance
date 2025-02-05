# DBCA OIM developer guidance

This is a resource for OIM developers new to DBCA, to get to a basic standard of understanding of our development tools and processes. Individuals should feel free to skip elements for which they already have a good level of competency. This page is not prescriptive, but it represents a sensible order of operations for a new hire to go through.

Additional resource documentation pages include the following:

- [DevOps best practices](/developer-guidance/DevOps)
- [Docker](/developer-guidance/Docker)
- [Auth2](/developer-guidance/Auth2)

## Web development stack

To summarise the recommended technology stack for new web development projects:

- [Python](https://www.python.org/) - programming/scripting language
- [Django](https://www.djangoproject.com/) - Web application framework
- [Git](https://git-scm.com/) - project code source control
- [Docker](https://www.docker.com/) - software containerisation

## Python

[Python](https://www.python.org/doc/essays/blurb/) is a general purpose programming language which is dynamically typed, interpreted, and known for its easy readability. General principles to be aware of when undertaking development of applications using Python:

- Python 2.x/3.x - New projects should _always_ be developed against a recent version of Python 3. Notwithstanding the similarities, Python 2 is no longer maintained for the purposes of features or security.
- Correct tool for the job - developers should be mindful of using Python where its strengths are greatest (rapid prototyping, versatility, portability, maintainability) versus other tools that may have advantages (SQL for database queries, command-line tools for file/text handling, JavaScript for DOM manipulation, etc.)

For installation, most \*nix operating systems come with a version of Python installed which will probably be sufficient for learning. For usage instructions, see [this page](https://docs.python.org/3/using/index.html). For installation on Windows, see [this page](https://docs.python.org/3/using/windows.html).

A suggested syllabus for learning Python for the purposes of web application development is as follows:

- Install Python 3.x on your PC.
- If required, take a [crash course in the Python syntax](https://www.freecodecamp.org/news/learning-python-from-zero-to-hero-120ea540b567).
- For a more thorough overview of Python syntax, read the [Dive Into Python ebook](https://diveintopython3.net/), chapters 0, 1, 2, 3, 4, 7, 11 & 14 (the whole book is valuable, but these chapters will get you basically productive).

### Python environment management

Once you start working on more than one project, managing separate and isolated Python environments for each one becomes a requirement. This topic is slightly "extra credit" (it's most important to get in and start learning the syntax), but it's worth learning sooner rather than later. [This is a good primer](https://realpython.com/python-virtual-environments-a-primer/) on Python virtual environments and why you need them.

There are many different Python environment management tools on the market (pip, pip-tools, Poetry, Pipenv, etc.) and the choice always remain with individual developer, but the current recommended tool is [uv](https://docs.astral.sh/uv/). It's (very) fast, easy to use, and is standards-compliant. First steps are as follows:

- Install [uv](https://docs.astral.sh/uv/getting-started/installation/) on your development PC.
- [Install a specific version of Python using uv](https://docs.astral.sh/uv/guides/install-python/).
- [Create an isolated Python environment](https://docs.astral.sh/uv/pip/environments/) and begin experimenting with it.

## Django

[Django](https://www.djangoproject.com/) is our current tool of choice for non-trivial web applications requiring a significant amount of business logic, direct manipulation of databases, and a large amount of user interaction. It is a "full stack" web framework that includes all of the components required to develop a complex web application for internal or external customers. It also boasts some of the best online documentation to be found.

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

## Software development environment

OIM application developers are encouraged to undertake development locally in an environment that is as close as possible to the final deployed application environment in order to reduce the incidence of "works on my machine" problems.

### Development tools

- [Git](https://git-scm.com/) - for version control. Refer to the "Source control" section below.
- [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install) - for developing more easily on windows.
- [Visual Studio Code](https://code.visualstudio.com/) - Microsoft's development IDE is freely available, and is a good choice for many developers.

### Linux

For the best development experience, OIM recommends developers install a variant of Linux on their workstation, either outright or in a dual-boot arrangement with Windows. We've found modern Ubuntu to have a good user experience plus up-to-date packages, but the choice of distribution can be entirely up to user preference.

Some good additional resources from the internet are as follows:

- [Linux Tutorial for Beginners](https://ryanstutorials.net/linuxtutorial/)
- [Linux Command Reference](https://perpetualpc.net/srtd_commands_rev.html)

### Windows

Microsoft has made it surprisingly easy to run a Linux environment under Windows 10 and newer. [Windows Subsystem for Linux](https://docs.microsoft.com/en-gb/windows/wsl/install) is a good alternative to a full VM, and is a lot more pleasant for Python development than using the Win32 ported equivalent of the tools. There are a few cases where support is less than optimal (e.g. some complex filesystem interactions fail, meaning you can't run PostgreSQL natively), but for basic development it provides a good alternative or transitional step to a full UNIX environment.

A decent guide for installing Docker inside the Ubuntu WSL is [here](https://dev.to/bartr/install-docker-on-windows-subsystem-for-linux-v2-ubuntu-5dl7) (includes instructions for installing the Azure CLI and dotnet core).

Failing that, there's the option of using an emulator like VirtualBox to run a local Linux VM for development purposes but this can consume a fair amount of your computer's resources.

### Mac OS X

Apple includes a lot of UNIXy tools in the standard OS X environment, but isn't very prompt in keeping the software up to date. You may have some luck using [Homebrew](https://brew.sh/), which is styled to be like a Linux package manager. See <https://hackercodex.com/guide/mac-development-configuration/>

### Software development best practices

The following ebook is a well-regarded and very readable collection of advice related to software development: [Andrew Hunt, David Thomas - The Pragmatic Programmer - your journey to mastery](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/)

## Git

[Git](https://git-scm.com/) is a free and open source version control system. Learning to use Git is non-trivial and can be intimidating. However, it is the most widely-used VCS in the world therefore learning to use it will be a good investment of time for any developer.

All public DBCA source code repositories are located at the [department's GitHub organisation](https://github.com/dbca-wa). Usage of the department's GitHub organisation is subject to the following principles:

- Developers are not required to use their DBCA email address as the primary for their GitHub account, but they are required to list their real name on their account profile.
- Developers require MFA to be enabled on their GitHub accounts require MFA to be enabled before being added to the DBCA organisation.
- Repository access permissions are managed via group memberships, which are managed by OIM administrators.
- Developers are responsible for not committing sensitive information and data into hosted code repositories.

GitHub provides [this list of introductory resources](https://help.github.com/articles/git-and-github-learning-resources/) for getting started with Git.

Here is a very basic workflow of a Git repository and a developer's fork of it:

1. Create a fork of a repository (repo) on Github.
2. Clone the forked repo to your local machine (your fork on Github becomes the "origin").
3. Make changes to your local repository and commit the changes.
4. Push your commits to your remote repository (the fork) on Github.
5. Open a pull request (PR) from your fork to the original repo (propose changes).
6. The project owner reviews and merged your PR into the original repo.

Obviously things become progressively more complicated as we add things such as branching, multiple developers collaborating to update a repo, etc. The specific workflow in use should be settled on in each team; consistency of usage within a team is more important than the workflow chosen. A good, basic workflow is the the Github Flow: <https://docs.github.com/en/get-started/quickstart/github-flow>

A helpful graphic demonstrating the Git commands to move a repository state between locations is as follows:

![image](https://user-images.githubusercontent.com/121014/152270734-d3a75462-7c8a-4fe8-815b-1ed3de2bb373.png)

Below are some additional resources to assist with learning Git:

- [Git tutorials](https://www.atlassian.com/git/tutorials) - a good set of tutorials about Git functions by Atlassian.
- [Git cheatsheets](https://training.github.com/) - reference sheets covering essential Git commands.
- [Git Immersion](http://gitimmersion.com/) - a guided tour through using Git via a series of lessons.
- [Learn Git branching](https://learngitbranching.js.org/) - learn the workflow of branch and merging at the CLI, in your browser.
- [Awesome Git](https://github.com/dictcp/awesome-git) - a curated list of resources about Git.

### Working on a repository

The high-level expectations for source code management are explained in the [ASD guidelines for Software Development](https://www.cyber.gov.au/resources-business-and-government/essential-cyber-security/ism/cyber-security-guidelines/guidelines-software-development). For most multi-contributor projects we use [GitHub pull requests](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project#making-a-pull) as the workflow for contributions/code review.

We recommend the following steps for getting started with a code repository:

- In the GitHub UI, [fork the main repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) to your own account.
- Add your fork of the repository as the origin

```bash
git clone https://github.com/your_username/project_name.git
cd project_name
```

- Add the original version of the repository as an "upstream" source:

```bash
git remote add upstream https://github.com/dbca-wa/project_name.git
```

Do all of your work on the fork, make a bunch of commits, push frequently, etc. Once a feature is done, use the GitHub UI to create a pull request from your fork to the upstream repository, so that it may be code reviewed. Developers might wish to work on specific features or fixes in a branch and then merge that branch to the main branch as needed, however the specific workflow can be defined by the project owner.

## Docker

All modern web applications in the department are built as Docker images and deployed using [Kubernetes](https://kubernetes.io/). This is a whole technical speciality on its own, so we suggest that new developers not already familiar with Docker ease into it over time (concentrate initially on Python, Django and getting set up with collaboration tools).

For the enthusiastic, there are some resources to start learning about [Docker](/Docker.md) below.

- [Introduction to Kubernetes on Azure](https://docs.microsoft.com/en-us/learn/paths/intro-to-kubernetes-on-azure/) - Learning path from Microsoft. Pretty thorough introduction to Docker, containers and AKS in general.
- [Awesome Docker](https://github.com/veggiemonk/awesome-docker) - a curated list of resources related to Docker.

## Security awareness

- [1Password](https://1password.com/) - a service for managing and sharing organisational secrets.
- Keep sensitive information out of project repositories - don't commit user credentials, passwords, access keys or database dumps to the repository. Get in the habit of creating a `.gitignore` file and adding relevant file patterns to it in order to reduce the chance of this happening.
- ISAP - the department runs an Information Security Awareness Program training course that is compulsory. Even if the information seems basic, try to internalise the content and cultivate a security mindset.
- In the case of credentials or keys, the recommended approach is to make your settings/config code read these pieces of information in via **environment variables**.
