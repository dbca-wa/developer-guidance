# Git

[Git](https://git-scm.com/) is a free and open source version control system. Learning to use Git is non-trivial and can be intimidating. However, it is the most widely-used VCS in the world therefore learning to use it will be a good investment of time for any developer.

All public DBCA source code repositories are located at the [department's GitHub organisation](https://github.com/dbca-wa). Usage of the department's GitHub organisation is subject to the following principles:

- Developers are not required to use their DBCA email address as the primary for their GitHub account, but they are required to list their real name on their account profile.
- Developers require MFA to be enabled on their GitHub accounts before being added to the DBCA organisation.
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
- [Beej's Guide to Git](https://beej.us/guide/bggit/html/split/index.html) - if you wanted to read a thorough, clear, engaging book-length guide to all aspects of using Git, this is the one.

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
