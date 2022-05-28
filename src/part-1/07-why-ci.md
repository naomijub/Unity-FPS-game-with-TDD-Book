# Chapter 6: CI, why does it matter?

First of all, CI means Continuous Integration and it is a development practice where developers integrate code into a shared repository frequently. Each time someone integrates with the integration engine, automated builds, automated formatting, automated linting and/or automated tests may be executed. Another step that is sometimes used is Pull Request reviews and some continuous delivery practices. 

## Why should we be concerned about CI?

CI strategies don't really reduce bugs, but they definitely help detecting them quicker, as the code is frequently integrated so that build systems and test runners are always running. Whenever they fail, a bug might be present and some action needs to be taken. Having said that, we can point out a few benefits of CI strategies:

* Bug detection - besides the fact that tests and builds should be executed whenever the code is integrated and if anything fails it is quickly fixed before merging, detecting when other bugs occured or were introduced is way faster, so the mean time to resolution should be reduced.
* Version control - usually CIs are integrated with version control systems, which help us identify merge clonflicts and have a clear idea of the current code state.
* More test reliability - test reliability is increased as every test scenario is executed when weh commit any changes, making sure that each scenario that has a test is correctly tested for the current changes. 
* Faster release rate - as the code state is always being checked, the main branch is always ready to be deployed and used.
* Reduces critical defects - as less bugs go into production, there are less critical defects to be dealt with after commiting, which in a general sense also reduces backlog, as they are probably not going to be introduced. Less bugs also mean less development cost.
* Easy Maintenance and Updates - as the code state is nearly guaranteed to be correct in the main branch, fixes and updates are easier to implement.
* Documentation - many CIs have a documentation generation and release from the code integrated into the main branch. 

Only knowing the benefits doesn't make a CI happen, so we need to understand some of the basic practices associated with CI:

* Have all the code associated with a project or product in the same repository or in some integrated way. For example, monorepos for a product, each section of a product or a project in its own repository or core project in one repository and its libraries in another.
* Automated builds, whenever the code is integrated, execute your build system to check if everything is OK.
* Execute tests for every build - building can detect some specific or compilation errors, while testing can detect logic or functionality errors. 
* Keep it quick - avoid long CIs as they make it more dificult to frequently integrate.
* Test the code in an environment similar to production - in microservices we usually use container-like strategies to develop and deploy in the same environment. 
* Anyone can easily have access to the last stable build.
* Automated deploy - it is easy to release a test version or a beta version of the product whenever new features are included.

Besides knowing the basic practices it is important to know the methodology behind it. This is not the only methodology available, but it is one I find most versatile:

1. Engineers clone the repository into their workspaces.
2. When all changes are done,  commit them to the remote repository.
3. For committing into the main branch there are two most common practices. Feature branch, when all the code is committed into a new branch, a pull request is required and code review is done. Or, trunk based development, when all the team is in sync and agrees to some basic development practices and the commits are made directly into the main branches.
4. The CI server monitors when changes are made in the repository and checks for formatting and linting.
5. The CI server builds the game and executes its test runner.
6. The CI server communicates if the build was successful
7. In case of failure, the CI server communicates to the team. 
8. The team fixes the issues (build, testing, formatting or linting).
9. The CI server delivers builds for beta testing or testing in the staging environment. 
10. Restart the process for another feature or update. 

## Team Responsibilities 

For TDD and CI to work well there are a few possible team responsibilities that should be observed. Many teams develop their own rituals over these practices and responsibilities, by removing, changing and evolving them, which results in a self managed team, avoiding hierarchical and bureaucratic policies. A few of them are:

* Keep your code fork up to date.
* Check your code and the remote main branch frequently.
* Don't commit something that is broken in your machine.
* Don't commit untested changes.
* Don't add code to a build that is broken, unless it is to fix it.
* Don't abandon your code after the commit, wait for the CI to finish.
* Honestly review pull requests codes, also known as code review.

### Code Review

Code review is a common agile practice that aims to share knowledge of what is being built as well as receiving feedback on the code that was written and improvements requirements. Besides that, it is usually associated with feature branch practice which enables the CI to be executed more frequently and no code is merged if things break.

