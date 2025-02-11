---
title: Idea - automatic dependency updates
categories:
  - software
tags:
  - idea
date: 2023-04-01T00:00:00+00:00
lastmod: 2023-04-01T00:00:00+00:00
---

When working on a software project, more then likely you have some dependencies. When you add a dependency to your app, there is a cost going forward. You *should* keep those dependencies up-to-date to prevent some potential problems: 

* **Security** - Just like with your computer, an easy way to keep yourself safe from vulnerabilities is to keep software up-to-date. This also goes with your dependencies. 
* **Productivity** - We have probably all had that feeling when you have an exciting idea for some code that you want to write but before you are able to begin working on it, you have to spend an hour updating each dependency. It would be ideal if you could begin developing immediately with all dependencies already up-to-date. 

The goal of this project is that I can launch an app today and 6 months from now do a `git pull` and start working on it immediately. No need to update it's dependencies or upgrade tooling. 

# Requirements 

I work with a few different types of languages and build systems: iOS, Android, Node/Deno, go. I want to try and figure out a re-usable workflow for each of these different languages. But before then, I need to define some requirements that's the same for all. 

## Every commit has dependencies locked to a specific version. 

This shouldn't be a difficult requirement as many languages use lock files. As long as those languages are able to install and execute using the lock file versions, this can be taken care of. 

We want our software to be reliable. Each commit should reliably compile by everyone or fail by everyone. No, "it works on my machine" scenarios. If dependencies are locked to 1 specific version for everyone, we can feel confident we will not have issues. 

## Each dependency can define it's own rules for how the dependency can be updated. 

What if 1 dependency should never be automatically updated? What if 1 dependency should only have it's minor version updated? You should be able to have a default behavior for automatically updating dependencies, but also be able to define rules for each dependency independently. 

## Update dependencies to latest version by default 

Unless the developer defines rules for a dependency to prevent this, tooling should attempt to update to the latest version of the dependency. This includes an update to a new version that could introduce a breaking change and/or update to a newer major version. 

This might not sound ideal because, well, you may introduce a breaking change and your code will no longer compile. That sounds bad! However, *the goal of this project is to keep dependencies up-to-date and so that must include major version updates*. 

If a dependency update causes a breaking change, I want to get that project fixed and compiling again as easily as possible. I am not 100% sure yet on how to easily do this but I do have an idea. There are handy tools such as [git-xargs](https://github.com/gruntwork-io/git-xargs) that allow you to write a script that performs a change to X number of code bases such as creating a new file or modifying existing files and push those changes to the existing pull request. If there is 1 dependency that I use in 15 of my projects and all 15 no longer compile because of a breaking change introduced by this dependency, I should be able to create a script that performs the change against all 15 of the projects and get them all to compile again. On paper, this sounds promising. In practice, this might not work well but that's to be decided. I have not experienced this scenario many times yet. 

It could be considered a feature to experience dependencies that introduce many breaking changes. I might ask myself if I even need that dependency and I decide to remove it instead of update it? 

## Dependency updates can be manually reviewed by a human or not. 

Some projects might benefit from reviewing the changelog of dependencies before accepting the newest version. Some projects might not have a good test suite and need to be manually tested before accepting the updates. 

You should be able to specify when updates should be reviewed before being accepted. Projects that have a good test suite and use popular dependencies are projects that may benefit from automatic acceptance of updates. 

# Workflow I want to aim for 

* Once a week, a tool runs against my project and attempts to update it's dependencies to the latest release version (no beta, release candidate, developer previews). Major version updates are allowed and encouraged. 
* A pull request gets made if the tool finds dependencies that could be updated. 
  * If an existing pull request exists for that repository, the branches are simply updated to re-use the existing pull request. 
* Test suite runs automatically on pull request to check that merging this pull request doesn't break my software. 
* Optionally: a security scanner runs against pull request to perform a vulnerability scan to avoid merging in code with known vulnerabilities. 
* If I decide to allow it, as long as test suite passes, automatically merge the pull request with the dependencies updated inside. 
  * No need to make a new deployment of the software as we did not make a new feature or fix a bug for customers. Unless we are using a vulnerability tool and find the security of the software has been improved, we make a release but that release can be manually triggered by a developer. 
 
# Implementations 

How exciting?! Here is some implementations I have come up with for this idea of automatic dependency updates! 

* [**Deno**](https://github.com/levibostian/deno-update-dependencies-workflow) 
* [**Gradle/Android**](https://github.com/levibostian/Dropbox-KMP/blob/b7ea8ed23d4cebeb8557c505768017ecf57b1411/.github/workflows/update-dependencies.yml) 
* **Swift/iOS** - Not yet started 
* **CocoaPods/iOS** - Not yet started 
