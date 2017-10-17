# ROS Quality Hub
ROS Quality Hub is an online community hub that will provide a focal point and resources to foster greater quality related activity and bring the community together to improve the quality and quality practices within ROS and RIS-Industrial communities.

# Patterns
In software engineering, a design pattern is a general repeatable solution to a commonly occurring problem in software design. It is a description or template for how to solve a problem that can be used in many different situations. We used this concept to document ROS quality issues and the proposed solutions for these issues.

The patterns are presented based on the three modes of ROS development 1) Core Development, 2) Driver and Reusable Packages Development, 3) Application Development. Each development requires different measures for quality assurance and face a different set of challenges.

## Core Development

### Pattern 1: Submit a pull request
#### Name
Submitting patches to core packages through maintainers
#### Context
An application developer discovers a bug in a core ROS package (e.g. roscpp), corrects it and tests the patch on his own computer. The fix should be merged into the main code base, but that should be done in a controlled manner. 
#### Problem
How to ensure quality of the proposed changes to the core – and of the software as a whole after integration of those changes – and at the same time encourage newcomers and members of the ROS community to participate in the development?
#### Forces
_**Striving for quality**_

Everybody and especially maintainers are interested in maintaining the quality of core packages, as the introduction of new bugs or regressions or reducing maintainability will affect all current and future users of ROS. (Perceived) usability and stability have a direct influence on the reputation of ROS – especially in industrial settings where quality of the core packages is often considered paramount.

_**Guarding development (policies)**_

ROS development is governed by a number of policies. One example is that development always targets the latest release, with patches being backported to older and long term stability (LTS) releases whenever this is considered desirable or required. Another example would be maintaining portability of code in order to remain platform independent as much as possible. Not every newcomer is aware of these policies.

_**Involve community members in development**_

As every Open-Source Software project, ROS lives through the enthusiasm of its community members. But this also requires that community members can participate in the development, learn about the design and code structure and can earn merits in order to take on more responsibility in future. Contributing to core packages is an important step in the onboarding of new community members.

_**Educate community members**_

To successfully contribute, newcomers need to learn how to write high quality software in-line with ROS’ best practices. These principles can best be taught based on actual development rather than only publishing them on the wiki and expecting newcomers to read, understand and apply them before writing and submitting their patch.

_**Maintaining efficiency**_

Blocking contributions from being merged into the main code base for too long can be detrimental, both to the engagement of the submitter, as well as to the chances that they will get merged (as a patch may have been written for an older version of the software, increasing the effort required to make it compatible with the current state). As such, Pull Request reviews and iterations should be efficient, with minor (cosmetic) issues not holding up the process.

#### Solution
Community members as Submitters submit a change through a Maintainer. The Maintainer should guard the quality of both the contributions and the result of merging the contribution with mainline by making use of their understanding of ROS and of the automated quality assurance tooling.
#### Stakeholders
A **Maintainer** is either part of the core ROS development team, or a well reputed community member who has taken on the responsibility for a (number of) core packages. The Maintainer ‘owns’ the respective repository on a ROS Github organisation. His interest and task is to make sure that changes to this repository adhere to the coding standards, that the development guidelines and policies are followed and that introduction of changes by the community does not diverge from the overall design of the software components affected (see also [Maintenance Guide](http://wiki.ros.org/MaintenanceGuide) on the ROS wiki).

A second interest is to involve a growing number of community members in the development and to educate newcomers about ROS development guidelines and policies, so that they eventually will be able to not only develop better patches but also might be able to take on more responsibilities.

A **Submitter** is every other community member who contributes for instance a bug fix to a core package. These could be developers using ROS to develop applications and encounter a problem or could also be developers of drivers or maintainers of other core packages. The submitter creates a Pull Request containing the patch and – ideally – a unit and / or regression test for the new or affected functionality.

#### Tools involved
**Github** is set up to require approved code reviews of Pull Requests by maintainers before such PRs can be merged into mainline.

The **ROS Buildfarm** runs **Continuous Integration** tests on all PRs submitted against core repositories, and Github is configured on core repositories to require successful test runs before PRs can be merged.

Maintainers can execute components locally to do **runtime or acceptance testing** when appropriate (for instance because an automated integration test is not available or possible due to hardware requirements).
#### Example 
A user of the roscpp client library identifies a bug in queue management (wrong order of operations) that results in messages being lost and submits a PR to remedy this after finding the cause and testing it locally on his own machine.
The maintainers of roscpp now need to merge this patch, after making sure it is safe to do so.
#### Example resolved
The pull request is reviewed, updated and finally merged by two maintainers of roscpp, which are referred to as MA and MB respectively. The chain of events in the review, polishing and final merge of the PR (and related PRs) is as follows:
* user identified problem in a core package (ros_comm/actionlib)
* he diagnoses it, writes a patch and tests that locally
* he then submits a Pull Request against the indigo-devel branch (second-to-last LTS)
* MA identifies some small omissions in the submitted patch and proposes some fixes
* MA creates a new PR based on that of the submitter, as the original PR did not target the correct branch (policy: PRs should target latest ROS release). Submitter’s PR is closed in favour of that one. The new PR also includes a regression test contributed by MA.
* MB reviews the replacement PR by MA (policy: many-eyes) and waits for the Continuous Integration server to complete the tests.
* MB merges the replacement PR into the latest development branch.
At this point maintainer B decides to backport the merged fix to other development branches for older ROS releases. He will do that at a later time together with other fixes for which backporting is desirable.
#### Links
List of core modules 
The list of packages which are considered part of the core are documented in [REP-142](http://www.ros.org/reps/rep-0142.html), sections ROS Core and ROS Base.
#### Best practices
* For developing ROS libraries and core components see the [ROS Developer's Guide](http://www.ros.org/DevloperGuid)
* For maintaining and releasing ROS libraries and core components see the [ROS Maintenance Guide](http://wiki.ros.org/MaintenanceGuid). 
#### Consequences
There must be at least one maintainer per core package (but preferably more). 
Maintainers need to have access to the necessary tools, both locally and remote (CI output of ROS Buildfarm).
It becomes possible to add more Q&A tooling to the buildfarm to more easily enforce Q&A process / best practices (make it less subjective).
#### Known Uses
Many open-source systems have assigned the responsibility for the quality of core modules to so called maintainers, among them the Linux project: https://github.com/torvalds/linux/blob/master/MAINTAINERS
#### Related patterns
* Continuous Integration Testing
* Test automation.

### Pattern 2: Accept a pull request
#### Name
Accepting a Pull Request 

#### Context
A user has identified an issue in a package, diagnosed it, wrote a fix and now submits a pull request. The maintainers of the package want to merge the contribution, but they need to make sure that that happens in a controlled and predictable manner.

#### Problem
Merging in of any (external) contribution presents a risk: specific bugs may be fixed or functionality enhanced, but at the same time new bugs may be introduced or repositories may diverge architecturally.
Following predefined procedures can help, as they reduce the chances of making mistakes during the review process, but which aspects of pull requests need to be checked, and how can maintainers cooperate during the review?

#### Forces
_**Controlled development**_

Well-defined processes for contribution will increase the probability of avoiding messing up the main code base. Which for example otherwise could result in components that are difficult to understand and use. This could be prevented by checking that any used code conventions and styles are followed.

_**Striving for quality**_

Everybody and especially maintainers are interested in maintaining the quality of core packages, as the introduction of new bugs or regressions or reducing maintainability will affect all current and future users of ROS. (Perceived) usability and stability have a direct influence on the reputation of ROS – especially in industrial settings where quality of the core packages is often considered paramount. 

_**Guarding development (policies)**_

ROS development is governed by a number of policies. One example is that development always targets the latest release, with patches being backported to older and long term stability (LTS) releases whenever this is considered desirable or required. Another example would be maintaining portability of code in order to remain platform independent as much as possible. Not every newcomer is aware of these policies.

#### Solution
The following is an example workflow for reviewing pull requests.

General, high-level checks

1. Make sure the PR does not introduce regressions:
   1. If a CI infrastructure is available: check its status
   1. Otherwise run tests manually / locally: this could include human-in-the-loop tests if necessary (fi when testing requires significant human-machine interfacing)
1. Check proposed changes for adherence to conventions:
   1. ROS REPs: as far as applicable
   1. code style: automated if available (clang-format) or manually
   1. naming conventions (packages, nodes, topics, services, actions, coordinate frames, etc)
   1. Repository/package specific conventions
   1. Design/architectural: would acceptance of the changes cause the overall design of the package to significantly diverge from its current structure (both static and dynamic)?

If the pull request is a bug fix:
1.	Check that either a new test is included or that an existing one is extended or adapted that proves that the issue is fixed

If the pull request introduces new functionality:
1.	Check that a test is included that covers the new functionality

Detailed checks
If the pull request is a bug fix:
1.	Do the proposed changes actually fix the reported issue?
1.	Is the fix generic enough, or does it only work for the submitter?
1.	Does the fix not conflict with other uses of the code (ie: those that might not be immediately apparent to the submitter)?
1.	Is there a less invasive, more efficient, easier or more maintainable solution that would be an equivalent fix?

If the pull request introduces new functionality:
1.	 Is the proposed functionality actually a new feature?
1.	 Does the proposed functionality do what is claimed?
1.	 Is the new feature generic enough, or does it only address a use-case of the contributor?
1.	 Is there a less invasive, more efficient, easier or more maintainable implementation that would result in the same enhancement?

Accepting the contribution
Pull requests should only be accepted and merged if the above checks have all been completed and the review has been performed by at least two maintainers.

#### Links
[Industrial Pull Requuest Review](http://wiki.ros.org/Industrial/Tutorials/IndustrialPullRequestReview)

[Maintenance Guide](http://wiki.ros.org/MaintenanceGuide)

[Moveit Pull Request](http://moveit.ros.org/documentation/contributing/pullrequests)

#### Consequences
Policies and standards for reviewing pull requests must be made available to maintainers.

Maintainers must be aware of the policies regarding pull request review.

All pull requests will have been reviewed by at least two maintainers.

The repository must have been setup to run CI on pull requests.

Contributions must be accompanied by sufficient rationale as to why they are to be included (and as to why the change introduced).

#### Known Uses
All pull requests against ROS repositories maintained by the OSRF use a similar - albeit implicit - review policy.

The ROS-Industrial community also has a similar policy for reviewing contributions.

Another example is the maintainers policy of the MoveIt community.

#### Related patterns
* Standards and Patterns (Driver Developer)
* Submit a patch (Component Developer)
* Submitting patches to core packages through maintainers
* CI with public infrastructure (App. Developer)

### Pattern 3: Continuous integration with the public infrastructure
#### Name
CI with public infrastructure (App. Developer)

#### Context
Running tests should be automated, and should preferably be done after each change to the code. In addition, (proposed) changes should be checked to make sure they don't introduce new bugs or break already existing functionality. Such regression testing should also be automated and should ideally also be run after introduction of changes.

Finally, development of ROS applications is often done in a collaborative or federated way, introducing the need to share test results and metrics between collaborators in an efficient way.

#### Problem
Setting up Continuous Integration for a code repository can be daunting, as not only does the CI service itself need to be configured correctly, but the repositories to be tested will need to be properly set up as well. Such configuration is in addition to the tests that need to also be present.

And in order for results to be viewable by all collaborators, a shared infrastructure will be needed, which further increases setup and maintenance overhead.

#### Forces
_**Producing Quality Code**_
Developing quality code is a process that can be facilitated by the use of the appropriate tools. CI is one of such tools, and a cornerstone in quality assurance procedures, providing automated ways of ensuring reproducibility, regressions tests, deployability and so on.

_**Striving for quality**_
Everybody and especially maintainers are interested in maintaining the quality of (core) packages, as the introduction of new bugs or regressions or reducing maintainability will affect all current and future users of ROS. (Perceived) usability and stability have a direct influence on the reputation of software – especially in industrial settings where quality of packages is often considered paramount. 

_**Increasing Trust**_
The use of Continuous Integration to continuously test and analyse software components and guard them against regressions increases trust in those components: a high coverage (and growing) test suite with a long history of succeeding tests are a good indication of the quality of the software.

Testing also increases trust and confidence of developers, as such continuous testing functions as a 'safety net' or 'canary' that warns developers whenever changes to be introduced would cause regressions and / or unintended side-effects.

#### Solution
Re-using a publicly available Continuous Integration infrastructure significantly reduces the effort required to introduce unit and regression testing into a project. Hosting code on publicly accessible repositories further reduces the administrative burden, leaving just the configuration of the repositories to be tested.

Within the ROS and ROS-Industrial communities several ready-to-use CI setups are provided. An overview is given on the [Continuous Integration](http://wiki.ros.org/CIs) page on the ROS wiki.

_**ROS Buildfarm**_

The ROS buildfarm can be configured to run CI for user repositories. This includes both CI for every change committed to a repository (called Development Tests) as well as CI for specific Pull Requests (Pull Request testing, only GitHub supported right now).

Configuring development tests for a repository is documented in [REP-143](http://docs.ros.org/independent/api/rep/html/rep-0143.html#distribution-file). In order to add pull request testing, refer to the [buildfarm/Pull request testing](http://wiki.ros.org/buildfarm/Pull request testing) page on the ROS wiki.

_**Travis CI**_

In all cases, the Travis CI service needs to be enabled for the repositories that should be tested. The [Travis Documentation](https://docs.travis-ci.com/user/getting-started) shows how this can be done. After this, testing setup can either be done manually, or by using the industrial_ci package provided by the ROS-Industrial project.

_**Vanilla Travis**_

Again, consult the [Travis Documentation](https://docs.travis-ci.com/user/getting-started) for how to create the CI configuration. This includes the tools used, software that needs to be present in order to build the software and run the test, combinations of OS and versions of the software to test.

In order to test ROS and ROS-Industrial packages, additional configuration is required, including adding the ROS package repositories, installing ROS, creating a workspace, configuring the workspace, resolving and installing all dependencies of the packages under test, building the workspace and finally running tests and gathering the results.

There is no official documentation in ROS on how to do this, but [felixduvallet/ros-travis-integration](https://github.com/felixduvallet/ros-travis-integration) is one example repository that shows how this may be done.

_**With industrial_ci**_

To exploit the fact that many ROS packages are tested in similar ways, and thus the fact that the configuration of the repositories that host them is also similar, the ROS-Industrial project has made the [industrial_ci](https://github.com/ros-industrial/industrial_ci) package available. In cases where the template configuration that it provides can be reused, this greatly simplifies Travis setup for a particular repository.

The [Quick Start](https://github.com/ros-industrial/industrial_ci#quick-start) documentation lists the necessary steps. More complicated setups (such as those needing special build or test dependencies) are covered in the [detailed documentation](https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst).

#### Links
[REP-143](http://docs.ros.org/independent/api/rep/html/rep-0143.html)

[REP-141](http://www.ros.org/reps/rep-0141.html)

[Indexing Your ROS Repository for Documentation Generation](http://http://wiki.ros.org/rosdistro/Tutorials/Indexing%20Your%20ROS%20Repository%20for%20Documentation%20Generation)

[ROS wiki: CIs](http://wiki.ros.org/CIs)

[ROS wiki: buildfarm/Pull request testing](http://http://wiki.ros.org/buildfarm/Pull%20request%20testing)

[ROS wiki: regression_tests/Development Tests](http://wiki.ros.org/regression_tests#Development_Tests)

#### Consequences
Contributors will be notified of test results as soon as the CI service has completed running the tests.

Developers will need to keep test suites up-to-date and meaningful: the CI service only automates running the tests, gathering the results and reporting on them. It does not create any tests itself. As tests are also code, this means increased maintenance for maintainers.

Maintainers and developers will need to guard against a false sense of security: a 'green badge' from the CI service does not necessarily mean that all is ok. Low coverage from test suites may leave defects undiscovered.

Everyone, not just the developer or maintainer, has access to build and test results for registered public repositories.

#### Related patterns
* Continuous Integration with private repositories
* Integrate tests in catkin
* Pre-release Testing
* Regression Testing
* Submitting patches to core packages through maintainers

### Pattern 4: Regression test (Unit test)
#### Name
Regression Testing
#### Context
A developer fixes a bug in a core ROS package (e.g. roscpp), corrects it and tests the patch on his own computer. The developer wants the bug not to be reintroduced later.  The pattern is beneficial for component and application developers as well, but it is mostly used by core developers (core components are easier to test, and it is more important to test them). Any ROS project (including application and component process) that decide to use continuous integration, should seriously consider also using regression testing.

#### Problem
The problem is that often bugs are (re-)introduced by developers not knowing the context sufficiently well, and bugs keep returning the project (they are known as regression bugs), because the rationale for a certain decision in code is forgotten.  The problem is to prevent future developers from reintroducing the bug.

#### Forces
_**Avoiding Reintroduction of Problems**_

Using tests for past bugs influences quality in the long term. It is a well accepted practice in many open-source projects.   Writing regressions tests, you contribute to long term quality of the ROS ecosystem.  You enable maintenance of code, and better automated discovery of inconsistencies.

_**Documenting Design and Requirements**_

Regression tests document your coding decisions, and communicate to other developers automatically about their violation.  Thus tests become documentation for your code.

_**Enable Others to Contribute to ROS**_

It is very difficult for new external developers to contribute to your components.  When they make changes to code, they are often doing it in the blind, driven by a lot of guesswork.  By providing a harness of regression tests, you help them in the task.  They get immediate feedback for their changes.  It becomes easier to contribute to a project.

_**Amplifying Value of Continuous Integration**_

Regression tests, along with normal scenario-based requirements tests contribute to overall body of automated tests for your component.  This increases effectiveness of the build system and of continuous integration (CI).  Your component is better tested against evolution of other APIs that it depends on (CI servers will tell you better and more precisely what problems develop in your code).

_**Development Cost**_

Regression testing comes at a cost: you need to develop a test, which sometimes may be difficult or costly.  Sometimes it might also be nontrivial, as the test should be automatic.

_**Maintenace Cost**_

Regression tests need to be maintained. When the design of the component changes, a lot of tests become invalidated (for instance no longer compile, or throw runtime exceptions related to the API design).  These tests fail not only because the redesign re-introduced bugs but also because they need to be updated to the new design.   Occasionally, with bigger redesigns, old regression tests should be dropped.

#### Solution
In order to avoid a bug to be reintroduced, you should write a test that fails if the bug is reintroduced.  Often this test is best to be written before the bug is fixed.  Once you understood the bug, write a small unit tests that exhibits the problem (fails).  Then see this failure go away, once the bug is fixed.  

It is best to write this test at the lowest possible level, where the problem is exhibited.  If the problem is local in a library function, it is beneficial to write the test at the API level of this library.  If the problem involves communication on a ros-topic, it is probably best written at the ROS node level.  The reason for this is two-fold. First, lower-level tests are more efficient, involving less ROS infrastructure, and thus more efficient to execute.  Fast execution of tests is beneficial both offline (on your machine) and in continuous integration.  Second, lower-level tests localize the problem better, so when they fail, it is easier for new developers to diagnose what is going on.

The ROS project provides several standard processes and solutions for regression testing. It also (at least formally) requires unit tests for code review.  Regression tests are suitable unit tests for bug fixes.

#### Stakeholders
A **Submitter** is the programmer willing to submit a bug fix to a ROS repository of a package.

A **Maintainer** is the programmer taking care of the package.

A **Code Reviewer** is another ROS developer who together with the maintainer will be reviewing the code (very often, for simpler packages, or for less formalized projects this is the same person as the maintainer).

#### Tools involved
For testing Python code at library level (at the Python API level) ROS projects should use Python’s unittest framework. See http://pythontesting.net/framework/unittest/unittest-introduction/. For testing C++ code at the library level (at the C++ API level) Google test framework gtest should be used. See https://github.com/google/googletest. For testing at the ROS node level, involving ROS as a communication middleware, rostest  is used together with unittest or gtest. See http://wiki.ros.org/rostest. This applies both to single node tests, and tests that require integrating several nodes (technically known as integration tests, not unit tests).

It is key that the tests are not only automatic, but are integrated in the project scripts, so that they are run by the build and test infrastructure, whenever the project is being tested. To run the tests, you will need catkin/roslaunch integration (see the pattern Integrate tests in Catkin and http://wiki.ros.org/rostest/Writing ).  This may involve introducing a build dependency on rostest in package.xml and including a launcher for the test in the test file.

Test nodes should be introduced using the <test> tag into launch files.  The rostest tool interprets these tags to start the nodes responsible for running node-level tests. (See http://wiki.ros.org/roslaunch/XML/test )
Regarding the submission please refer to the pattern Submit a patch where git infrastructure for submission is discussed.

#### Example
[Fix race condition that lead to miss first message](https://github.com/ros/ros_comm/pull/1054) #1054. This particular example involves a Python unittest (without rostest).

Example resolved:

* Submitter identified problem in a core package (ros_comm/actionlib)
* Wrote a patch and tested locally using the new regression test.  
* In this particular case, another developer submitted a regression test exhibiting the problem
* Submitted Pull Request against indigo-devel (second-to-last LTS). Importantly the pull request included both the test and the submission, so the both the fixing patch and the test are used to explain the problem (in the example this is a race due to wrong locking).
* maintainer #1 identifies small issues with submitted patch, proposes fixes. In particular he requests that the regression test is incorporated into the patch.
* Maintainer #2 reviews the new PR by maintainer #1 and waits for the Continuous Integration server to complete the testrun.
* Eventually (on another branch, see https://github.com/ros/ros_comm/pull/1058 ) the fix for the problem is merged together with the test.  The test remains active for future modifications of locks in this package.

Sometimes regression tests will involve large amounts of data (for instance ROS bags storing data from the failing execution). It is possible to make tests conditional, so that they are not called if this data is not available.  This allows the other developers on your package to avoid running expensive tests, if they don’t wish it.

#### Links
http://wiki.ros.org/UnitTesting 
http://wiki.ros.org/unittest 
http://wiki.ros.org/gtest 

#### Consequences
There must be at least one maintainer per core package (but preferably more). 

Maintainers need to have access to the necessary tools, both locally and remote (CI output of ROS Buildfarm).

It becomes possible to add more Q&A tooling to the buildfarm to more easily enforce Q&A process / best practices (make it less subjective).

Regression testing benefits community developers as well (not only maintainers, and not only core package developers).

#### Known Uses
Many open-source systems are using regression testing, and regression testing has been popularized a long time ago.  A good example of an open-source project with regression tests is WebKit: https://webkit.org/regression-testing/, a web-browser engine used by many applications, including by Apple’s Safari browser. Another project is KDE, a popular desktop manager for Unix-like systems:
https://community.kde.org/Guidelines_and_HOWTOs/UnitTests. Both projects use CMake as their build manager, which is a similarity they share with ROS.

#### Related patterns
* Continuous Integration Testing
* Submit a patch
* Integrate tests in catkin

### Pattern 5: Integrating tests in the build (catkin)
#### Name
Integrate tests in catkin

#### Context
A robot application is not only developed but the software is subject to change either as part of the development process or as part of later maintenance and evolution. Thus, crucial behavior of the software can get affected and in worst case fail or yield wrong outputs. 

#### Problem
In many cases this error will not arise right away and at the time that a developer or an user comes across this bug it will be difficult to track down its source. Even if the developer defined certain tests for the software, it is likely that they are not executed on a regular basis.

#### Example
A team develops for example a library for path planning of a robot. One of the developer wants to clean up the code and decides to use matrixes calculations instead of simple equations. However, he mismatches two entries of a matrix, or forgets a minus at some position. Afterwards, he pushes his changes to the common repository of the library and moves on to his next task. 

#### Forces
_**Strive for quality**_

In order to courage other parties to work with the provided software, it should meet preparations to sustain a flawless code.

_**Fail fast**_

Development becomes much more expensive if bugs are not detected at an early stage.

#### Solution
The whole application needs to be tested regularly to catch these regressions. Thus, one has to define explicit tests for the crucial behaviour of the program. Common methods for this purpose are unit tests and rostests (an extension to roslaunch, which enables testing across multiple nodes).

In order to run tests for a package globally they can be defined in the build system of ROS named catkin. Afterwards, the catkin tools offer to run all tests of the ROS workspace either at once or individually. This makes it more convenient to run tests on a regularly basis. Furthermore, they can be integrated into Continuous Integration to run the tests for example for every push to the Github repository.

#### Preparations
The how to documentation of catkin describes the steps of integrating tests extensively. First of all, it recommends to configuration all steps related to testing conditionally. In this way larger data files, that might for example be required for Replay testing, do not need to be downloaded if the user does not intend to do testing.

The document further covers the integration of the following types of tests: 
* Configuring gtest for C++
* Configuring Python nose tests
* Configuring rostest

#### Execution
One can either run all test at once with the command 

$ catkin_make run_tests 

or an individual test (here of type gtest) for the package example package by 

$ catkin_make run_tests_examplepackage_gtest_exampletest 

Some further information can be found in [1, Running unit tests].

#### Stakeholders
* The developers of the package
* The community members

#### Tools involved
* atkin
* gtest (cpp)
* nose (Python)
* rostest 

#### Example resolved
In the mentioned scenario of the library for path planning, it is useful to have a test that verifies that the mechanics of the kinematic functions remain flawless. This can be achieved by defining a test that inputs an arbitrary (but reachable) cartesian goal position into the inverse kinematic to calculate the required robot’s joint states and subsequently, input this values to the forward kinematic to again get a pose in cartesian space. Finally, one can compare the original and the calculated pose to see if the functions are working correctly. The test will fail, if the difference is higher than a defined threshold, which is accounting numerical errors. If this test is include in catkin and the Continuous Integration system, it can be executed every time a developer pushes his patches to the library.

#### Links
[Configuring and running unit tests](http://docs.ros.org/kinetic/api/catkin/html/howto/format2/index.html)

[Conceptual overview of catkin](http://wiki.ros.org/catkin/conceptual_overview)

[Rostest](http://wiki.ros.org/rostest)

#### Consequences
There is an initial effort to develop a suitable set of test cases for the application and as best practice a Continuous Integration is required to run the test on a regular basis.

#### Related patterns
* Continuous Integration with the public infrastructure
* Continuous Integration with private repositories
* Regression Testing (unit tests)
* Replay testing

## Driver Development
### Pattern 1: Submit a pull request


### Pattern 2: Accept a pull request


### Pattern 3: Continuous integration with the public infrastructure
### Pattern 4: Regression test (Unit test)
### Pattern 5: Integrating tests in the build (catkin)
### Pattern 6: Release a reusable module
#### Name
Release a reusable module (e.g. a driver)
#### Context
As part of the development, a model of general interest has been developed, e.g. a driver for a specific camera, or a new path finder. The development team wants to make this module available for others in an easy manner. The community wants to have this software available, but also wants to assure that it is tested for the specified ROS distribution and optional provides documentation.
#### Problem
If a package is provided in the form of source code to interested end-users, the installation can reveal difficulties, since the user has to take care of installing required dependencies by himself. Then again, it takes quite some effort to create an own independent Debian package. Such a package could be used by interested community members, however, people would have a hard time to search for it and they might be insecure of the quality of the software. One last point is that there are different Debian-based distribution as well as different computer architectures and thus, one has to build several Debian packages.
#### Forces
_**Strive for quality**_

In order to encourage other parties to work with the provided software, it should meet preparations to ensure a proper compilation and installation. 

_**Community reputation**_

It is a feather in the hat to have authored and maintain an “official” ROS package, which is hosted in a central place.

_**Community benefit**_

The ROS community is an open-source community. The idea is to benefit from each others’ development and to share results.

#### Solution
Releasing the reusable component to the ROS building repository makes it available to the public. Official ROS packages have an automatically created wiki page, which shows basic package information, the link to the source code and the status of continuous integration. In addition, one can increase the usability by filling the page with further documentation and tutorials. 

Another very important benefit is that this process makes the software available as an pre-compiled Debian package for APT (Advanced Package Tool). This makes the managing of the package on Linux distributions very easy for an end-user, by providing functionalities as search, install, update and remove. The ROS build farm will automatically build binaries for different Debian-based distribution as well as different computer architectures. As an example the Ubuntu Xenial packages will be build for amd64, i386 and armhf. 

Moreover, if any build-dependency of the software is updated, the build farm will automatically rebuild and check all of the package’s binaries.

In order to assure the quality of officially released packages, the process requires a certain procedure which is described in the following. Be aware that you have to release the package for every ROS distribution individually.

_**0) Pre-release repository**_

Best practice is to perform Pre-release testing to assure that dependencies are declared and installation commands are set-up in the correct way.

_**1) Create a release repository**_

One has to create a new Github repository, which should be named the same as the package but denoted as release. For now it stays empty for, but later the software’s source code will be copied into this location via the bloom tool. 

Within this repository one can optional grant additional developer writing rights and thus to release patches.

_**2) Configure the package's release track boom**_

The package can now be released to the ROS build farm with bloom. Once you run the build automation tool, it will guide you through all necessary steps. It can be installed by the APT package python-bloom and started by the following command.

$ bloom-release --rosdistro <ros_distro> --track <ros_distro> repository_name --edit
Explanation
* <ros_distro>: This is the name of the ROS distribution, e.g. kinetic.
* repository_name: This is the name of the new ROS package
* --edit: This is important for a first time release to create a new track

_What is a track?_
“bloom is designed to allow the release of the same package for different ROS distributions and versions in the same release repository. To facilitate this, bloom uses release "tracks" to maintain configurations for different release processes.” 

Steps to perform with bloom

1. The tool will inform that the specified repository_name is not yet listed in the distribution file and ask for the link to the Github release repository.
1. Specify a desired repository name.
1. Insert the link to the package’s development/upstream (not release) repository, which contains the source file of the software.
1. In the next step select the provided repository type. Available options are git, svn, hg or hosted tar.
   1. Version can be left as default
   1. Release Tag can be left as default
1. Specify the right branch of the development/upstream repository.
1. Specify the ROS Distribution
   1. Rest can be left as default
   1. Provide your Github credentials

See [ROS: Releasing a package](http://wiki.ros.org/ROS/ReleasingAPackage) a more detailed overview and [Bloom: First time release](http://wiki.ros.org/bloom/Tutorials/FirstTimeReleasefor) for even more details on bloom.

Subsequently, bloom will prepare your release repository and create a pull request for the distribution.yaml of the selected ROS distribution.

_**3) Wait for ROS build farm**_

“Once your pull request has been submitted then you will have to wait for one of the ROS developers to merge your request (this usually happens fairly quickly). Then after 24-48 hours your package should be built by the build farm and released into the building repository. Packages built are periodically synchronized over to the shadow-fixed and public repositories, so it might take some time (weeks) before your package has made it into the public ROS debian repositories.” 

#### Stakeholders
* The author of the package to be released
* The community members

#### Tools involved
* Github
* Bloom
* Ros build farm

#### Links
[Bloom: First time release](http://wiki.ros.org/bloom/Tutorials/FirstTimeReleasefor)

[ROS: Releasing a package](http://wiki.ros.org/ROS/ReleasingAPackage)

[How to build and install target with catkin](http://docs.ros.org/kinetic/api/catkin/html/howto/format2/)

#### Consequences
* At least one developer must commit to be the maintainer of the released package.  
* A member or the ROS team must review the pull requests for the distribution. yaml and the ROS build farm must periodically build packages from this yaml lists.

#### Related Patterns
* Continuous Integration with the public infrastructure
* Pre-release testing

### Pattern 7: Pre-Release Testing
#### Name
Pre-release Testing
#### Context
A developer wants to contribute a piece of code (a patch) to a ROS component that is tested using continuous integration (the build farm).  

#### Problem
The build farm will install the component in a clean environment, on standard distribution components, attempt to build it and run the tests.  This environment is typically quite different from the one that the developer used to code the component, that tends to include a lot of local customizations for convenience, legacy, for interaction with his other ROS projects, etc.  For this reason, the installation, the build, or the tests on the build farm will often fail.  To make it worse, it can take several hours before the developer will receive the information from the build farm. The information might come when he is already working on a different issue, and it requires an expensive context switch to fix the component and re-submit to the build farm (or another CI infrastructure).  The problem is thus to shorten the modify-test-debug loop, but involving the setup that is as close as possible to the continuous integration conditions.

Another problem is being able to to run the tests, before the package release is made.  A package release, that causes tests to fail on the build farm is very problematic, because usually many other dependent packages will be affected, and their maintainers might be notified.  So failures of released packages in the build farm runs should be avoided. 

Pre-release tests make sense to be run, even if there is no automated tests in the component.  The setup will still check for compilation issues (errors find by compilers) and installation issues.  These traditionally are a large fraction of issues in ROS components, so just building and installing the component on the build farm (and then also in a pre-release testing environment) can detect many problems.

#### Forces
_**Development Speed / Release Speed**_

Pre-release testing allows to speed up testing, fixing, and releasing components, as one does not have to wait for the external build farm to report on test results. 

#### Complexity
Running pre-release tests is fairly complex, requires using docker components. However the process has been streamlined with external tools, and is still more efficient than relying on external CI servers.

#### Solution
The solution is to run pre-release tests, which are realized using the docker component technology and a set of scripts that mimic possibly closely the conditions of the external build farm, in a local docker environment.  Typically they will be able to detect the same problems as the build farm.

Unlike the build farm tests, pre-release tests should be run manually. ROS streamlines running the pre-release testing process. You need to

1. Install the prerequisites (mostly docker, the build farm scripts,  and catkin). See http://wiki.ros.org/regression_tests#How_do_I_setup_my_system_to_run_a_prerelease.3F 
1. Use http://prerelease.ros.org/ to synthesize an invocation command for the pre-release testing of your component. This interface allows you to configure the set of components that should be available on the test system (you can also use non-standard release components, which is often needed in testing bleeding edge development).
1. The synthesized command will be several lines long.  You just need to run it.

#### Stakeholders
A _**Submitter**_ is the programmer releasing the component.

#### Tools involved
Docker (for creating a virtual test environment), along with build farm scripts, and catkin usually installed (http://wiki.ros.org/regression_tests#Prerelease_Tests).  Of course, all other build and test dependencies will also be used.

The online tool http://prerelease.ros.org helps you by synthesizing the run command for the configuration for your build.

#### Links
http://wiki.ros.org/regression_tests#Prerelease_Tests
http://prerelease.ros.org 

#### Consequences
Your component needs to be packaged as a proper ROS package (with all the meta-data).

A 64-bit machine is required to run docker.

You can avoid/reduce build farm errors after release.

#### Related Patterns
* Continuous Integration Testing
* Integrate tests in catkin

### Pattern 8: Model-in-the-loop (MIL) Testing with Specialized Robot Simulators
#### Name
Model-in-the-loop (MIL) Testing with Specialized Robot Simulators

#### Context
Developers wants to perform tests and verifications of how different kinds of components operate together with specialized robot simulators. The components could for example contain control algorithms or network communication. And the specialized robot simulators could for example come from ABB, Fanuc, Kuka etc.

#### Problem
How to test and debug robot applications and drivers before running the software on a real robot that could both result in damage of the robot and damage of the environment including potentially human casualty. 

#### Forces
_**Rapid development**_

MIL testing allows for performing tests relatively quickly. It is not necessary to have access to real equipment, which might be limited in number and availability. This can then for example be used to verify that component still work as intended after new updates as well as testing current components against new simulator versions.

#### Quality
Depending on the test cases one should decide on the expected behaviors before performing the tests. E.g. what should happen if there is communication failure (if such parts are simulated), what should happened if only partial user input is used or is the simulated robot following the specified path or trajectory. This will allow the developers to detect possible issues and achieve components with higher quality.

#### Familiarization
MIL testing can help beginners to get used to working with robot software without danger, before potentially starting to work with real robots.

#### Solution
If you have access to a simulator of the targeted robot, you can use it to test the software on a simulation model of the robot (Model-in-the-Loop testing). To be able to make the best assessments of the results, you need to familiarise yourself with the usage of the robot simulator system that is being used for the tests.

Then the process for running the MIL tests could be:
1. Set up the different test cases.
   1. The focus of MIL tests is the (mal-)functioning of the software rather than the hardware. Test cases should contain both success scenarios and anticipatable error scenarios. The reaction of the software to changes in the environment the robot is operating in might be a subject of tests as well. 
   1. Define suitable success criteria and how to measure them. What logs would you need in a malfunction situation to analyse the error? 
   1. Prepare (program) the simulation setup including what data to log. Besides the model of the robot the test scenarios can contain specific arrangements of the environment.
1. Run the tests. 
   1. Deploy the software to the simulated robot and run the test scenarios.
1. valuate the results. 
   1. If the simulator has a visual interface, a first observation would be whether it shows the expected behavior. However, logs and the measurements for the success criteria should be analysed as well. 
1. Report/fix any detected issues.
   1. The error report needs to contain the information about the setup of the test, the measurements of the success criteria, and the log files.
1. Rerun the tests if needed.

#### Consequences
By performing MIL testing continuously then it should be possible to achieve higher quality components.

You need a simulator. The simulator has to allow to define, save and run test scenarios including variation of the environment the robot is operating in.

The simulator used in the testing should have been validated before trusting the results. (If the simulator is from the robot manufacturer this should not be an issue.)

If the success criteria can be evaluated algorithmically, MIL test can be part of a regression test set up.

Limits: simulation is always ideal, does not always capture all the physical aspects (both of the robot and of the environment.
#### Related Patterns
* Regression Testing

### Pattern 9: Best Practices
#### Name
Best practices

#### Context
Several contexts can be envisioned here:
* An application developer wants to develop components that could be used in other applications. The reuse could be done by him, his team or eventually external collaborator.
* A team of Developers want to agree on programming policy to ease their collaborative work.
* A Developer desires to produce code of quality by applying agreed best practices.

#### Problem
* How to develop a ROS code so that its reuse in other contexts is facilitated ?
* How to make sure that other developers can easily embed such development in other applications, but also contribute to its maintenance and evolution along time in a fluent way?

#### Forces
_**Strive for quality**_

By taking into consideration defined programming policies, guide styles and programming patterns, developers are taking on board the experiences collected by the Community along time for producing high quality code, in terms of readability, re-usability or extensibility among others.

Note that the community terms refers here not only to the ROS Developers, but also to the community conducting research on software quality.

_**Increase the efficiency in developing new applications**_

Even though the conception of applications through a collection of nodes is already a step towards the design of reusable code, a particular care has to be given to the communication interface for maximizing the reuse potentiality.

In that line, a well-described node (including documentation), which interface focuses on the core functionalities provided, and designed to be less dependent to the current application it is being developed for, is more likely to be reused in other contexts.

_**Facilitate the collaboration and community contribution**_

By following and / or establishing coding and architecture policies, developers can reduce the subjectivity of their implementation, and ease the readability of their development.

Such readability is necessary for facilitating the improvement and extension of the code by other Developers, for the benefit of all.

Finally, and in line with the previous point, the quality of the node interface (in term of documentation and interaction means) is also crucial for maximizing its reuse by the community.

#### Solution
_**Look for best practices**_

Generally speaking, a key in developing code intended to be understood and reused by other Developers or Integrator is to reduce as much as possible the subjectivity in the implementation choices.

Nevertheless all Developers do not have the same background and experience in software programing, robot programing or ROS. And even if they would have, there would still be a need for agreeing common practices for handling any problem.

This is why it is a good practice (if not mandatory) to look for best practices during the development of any component or application.

Unfortunately, there is not a unique place hosting all the good practices, and it still required to dive into several sources to find the relevant information we are looking for.

The main sources of best practices are the following:
* [ROS Wiki](http://ros.org/wiki)
* [ROS Answers](http://answers.ros.org/questions/)
* [ROS Discourse](https://discourse.ros.org/)
* [ROS-users mailing list](http://lists.ros.org/pipermail/ros-users/)
* ROSCon programs
* Team or developer best practices

The [ROS Wiki](http://ros.org/wiki) is a tremendous source of information, including for finding best practices. Yet, the page [ROS Best Practices](http://wiki.ros.org/BestPractices) provides useful pointers on best practices in ROS, as a set of “statements of how best to achieve common tasks with ROS”.

It mentions the existence of the [ROS Enhancement Proposal(REP)](http://www.ros.org/reps/rep-0000.html) which can be used to register best practices. It lists existing best practices, and also mentions the open points that would deserve a best practice description.

The wiki section on [ROS Use Patterns and Best Practices](http://wiki.ros.org/ROS/Patterns) also gathers best practices organized through abstract design patterns that are Conventions, Workspaces, Modularity, Communication, Parametrization, Logging and Robot Modeling.

The wiki section [ROS developer´s guide](http://wiki.ros.org/DevelopersGuide) is a good starting point for getting used to the common practices for developing components to be shared with the community.

Among other links, we can mention the pointers towards ROS Programming styles (for [C++](http://wiki.ros.org/CppStyleGuide), [python](http://wiki.ros.org/PyStyleGuide) and [javascript](http://wiki.ros.org/JavaScriptStyleGuide)), and the description of the [Quality Assurance Process](http://wiki.ros.org/QAProcess) to be followed by a package to be included into the ROS eco-system.

Considering that the people are likely to have already considered a question we have, it is relevant having a look at the [ROS Answers](http://answers.ros.org/questions/) section of ROS. In particular, the tag best_practices can be looked for for getting answers related to best practices. See [here](http://answers.ros.org/questions/scope:all/sort:activity-desc/tags:best_practices/page:1/) the list of related questions.

Note that [ROS Discourse](https://discourse.ros.org/) is intended to be the successor of [ROS Answers](http://answers.ros.org/questions/), and progressively this engine will replace the former one.

Finally, bet practices can be searched as well through the information provided by individuals or Developer teams.

Several presentations done at ROSCON ([2017](https://roscon.ros.org/2017/), [2016](http://roscon.ros.org/2016/) , [2015](http://roscon.ros.org/2015/)) provide good insights on best practices that could be used. Some developers groups give access to the best practices they gathered and follow, like:
* The [Autonomous Systems Lab](https://github.com/ethz-asl/ros_best_practices/wiki) of ETH Zurich
* The [Artificial Intelligence and Robotics Laboratory](http://airwiki.ws.dei.polimi.it/index.php/ROS_HOWTO) from Milano, which provides also a generic ROS node template that can help to define new nodes.

_**Consider Automation tools**_

Several high-level tools are existing for automating the creation of nodes and packages.

The advantage of such automation tools is that it reduces the code production by using hidden code templates or skeleton.

If the proposed templates fits the Developer needs, then the Developer can focus on the core added value of a component, and let the automation tool prepare the rest of the architecture.

_**ROSLab**_

[ROSLab](http://precise.github.io/ROSLab/) is a High-level Programming Language for Robotic Applications.

The code is maintained by [the Precise Lab](https://github.com/PRECISE), but has not been updated since two years.

Examples of use are available at [Nicola Bezzo´s website](http://www.seas.upenn.edu/~nicbezzo/ROSLab.html).

ROSLab is a high-level programming language based on blocks and links dragged on a java workspace which generates the skeleton code for robotic applications involving different types of robots.

Components can be connected though their communication interface.

Once generated, the Developer can implement the core code, having all the package and node skeleton automatically created.
The code generation is done using the [ROSGen](https://rtg.cis.upenn.edu/HACMS/report.pdf) component, which is implemented in Coq.

ROSLab seem to be an interesting solution for roboticist Developers with limited knowledge in programming language and ROS, and want to quickly develop applications.

_**ROSMOD**_

[ROSMOD](http://www.isis.vanderbilt.edu/node/4744) is a work from the [Vanderbilt University](http://www.isis.vanderbilt.edu/node/4744).

As it can be seen on the [github account](https://github.com/rosmod), the development is still active, and an online demo is available [here](https://rosmod.rcps.isis.vanderbilt.edu/?project=ROSMOD%2BSamples&branch=master&node=%2Fv&visualizer=RootViz&layout=NewDefaultLayout).

A extensive documentation is also accessible on [github](http://rosmod.github.io/webgme-rosmod/docs/users.html).

ROSMOD is a Robot Operating System Model-driven development tool suite, providing graphical tools for rapid prototyping and deploying large-scale applications.

It follows a component-based approach structure, and is said to be a refinement of the ROS component model.

The ROSMODE tool-suite is intended to reduce the amount of time and effort they spend installing, configuring, and maintaining applications.

Nevertheless, it requires agreeing with the proposed model of component, that is slightly different from the traditional ROS one, which may be acknowledged only by advanced Developers.

_**Bride**_

[Bride](http://wiki.ros.org/bride) is on the main outcomes of the European project Brics.

Bride stands for BRICS Integrated Development Environment.

It allows for a definition of component interface and behavior using an abstract representation. enabling automatic model validation and code generation, where appropriate. It also provides a clear separation in between framework-specific (like the component interface) and the framework-independent code (like the core component computations).

Bride is integrated as an Eclipse plugin, in which the Developer can graphically design nodes together with its communication interface with the ROS world.

The development is following the spirit of Component-Based Software Engineering, targeting quality, technical and functional reusability (see that [paper](http://www.best-of-robotics.org/pages/publications/UniBergamo_Component_Robotics_RA-Magazine_2009.pdf)).

Considering that a software component is defined to be a unit of composition with contractually specified interfaces and explicit context dependencies only, brics stresses in its implementation the clear distinction in between the interface and the implementation of the component functionalities.

From the definition of the interface, trough the Eclipse plugin, or directly through a xml description, brics prepares the ROS node structure, defines the communication tools, and prepares in a distinct file the skeleton of the code to be filled by the user.

The concepts followed by brics seem to be of major importance for developing stable components with clear interfaces.
Unfortunately, the developments have been stopped two years ago and are sticked to ROS indigo.

Furthermore, the life pattern implemented in the generated ROS core module can not be adapted to Developer needs, and would required good skills in java and eclipse plugin for being adapted to specific needs.

_**Rosnode 2.0 node life-cycle**_

In addition to the relevance of defining well the interface a node provides to the user, it is also crucial making clear what is the life-cycle of the node once launched.

Even though part of it can be deduced from the interface definition, critical aspects may not be easily inferred from such description, such as when the node computation starts, can we and should we stop and resume the node activity during the application, …

A particular care is placed on such aspect in the design of ROS2, essentially in the concept of [managed nodes](http://design.ros2.org/articles/node_lifecycle.html).
The inheritance mechanism is used to associate to nodes a common life-cycle, with pre-defined interaction mechanisms. This way the monitoring of the deployment, initialization, pausing and resuming of any node launched is made easier since all managed nodes follow the same policy.

Managed nodes execute following a known state machine which state indicates whether the node is unconfigured (just instanciated), inactive (configured but not running), active (performing its computation) or finalized (before destruction).

The implementation of a managed nodes requires implementing the different transitions from one state to another.

The advantage of such model is that, if well-spread in all nodes, the monitoring of an application is eased since all nodes follow a common methodology and can thus be triggered or consulted on their status in a common way.

The use of a component is thus less dependent on the implementation proposed by the Developer.

This definitely ease the collaboration in the community as well as the reuse of components in other applications.

Even though ROS2.0 is not yet deployed, such mechanism and philosophy is a good practice to consider when developing nodes, even in the current ROS structure.

#### Related Patterns
* Submit a patch
* Regression testing (unit tests)
* Code review
* Accepting a pull request
* Standards and patterns

## Application Development
### Pattern 3: Continuous integration with the public infrastructure
### Pattern 4: Regression test (Unit test)
### Pattern 5: Integrating tests in the build (catkin)
### Pattern 9: Best Practices
### Pattern 10: Continuous Integration with private repositories
#### Name
Continuous Integration with private repositories
#### Context
An application developer wants to use a continuous integration service as part of his/her development process in order to parallel to the development check that the application can be integrated and deployed and allow for regression tests. The CI shall be run in a separate server, and run automatically builds and tests defined in the code.
#### Problem
There are several options available to use CI, as Travis or the ROS buildfarm. However Travis does not support privately hosted repositories (only GitHub at the moment), and setting up and running a private installation of buildfarm is overcomplicated.
#### Forces
_**Producing quality code**_

Developing quality code is a process that can be facilitated by the use of the appropriate tools. CI is one of such tools, and a cornerstone in quality assurance procedures, providing automated ways of ensuring reproducibility, regressions tests, deployability and so on.

_**Make ROS business friendly**_

The use of ROS and ROS-I should be extended beyond the academic community, into industrial applications and industrial actors using and contributing to it. These actors usually have stricter IP sharing rules, and by giving them the proper tools such as CI, we facilitate their involvement into the ROS community.

_**Facilitate the collaboration and contribution to the community**_

Code contributed to the ROS repository is expected to comply with certain rules regarding its quality. By the use of CI as part of the development process, compliance with such requirements is facilitated, which fosters further contributions.

#### Solution
We provide instructions here on how to set up a system/process based on the use of Jenkins + industrial_ci + GitLab. The rationale to choose such a combination, instead of other available alternatives such as buildfarm, Travis and others is: 
* support for private repositories and in-house installation 
* based on open-source tools 
* easy installation and setup time 
* low threshold for usage, easy integration of new components into the process 
* extensible 
* GitLab is a well-known repository manager; however the instructions can be easily adapted and applied to other infrastructures

_**Components of the solution**_

_Jenkins_

Jenkins is an open-source automation server written in Java, that runs in servlet containers such as Apache Tomcat. It is mainly targeting facilitating the automation of continuous integration aspects: it offers integration with many version control tools, such as CVS, Subversion, Git, or Mercurial (can be extended to others through the use of plugins), and offers different ways of triggering builds, such as commits in the control version system or scheduling with a cron-like mechanism.

It also provides web-based reporting capabilities for the results of the builds (interface with the user is mainly web-based).

Jenkins supports scalability through a "master/slave" mode, where the workload of building projects are delegated to multiple "slave" nodes, allowing a single Jenkins installation to host a large number of projects, or to provide different environments needed for builds/tests.

Jenkins' functionality can also be extended by the addition of plugins.

_industrial_ci_

industrial_ci is a set of bash scripts that can be used to check that a ROS package builds and installs without issues. If unit or system tests are defined, it can also run them. In order to ensure reproducibility, the builds are run in empty Docker containers, in which the dependencies specified are installed.

#### Stakeholders
Installer / SysAdmin

Link to Jenkins and industrial_ci: Quick manual; Section For installer / administration

ROS application developer

Link to Jenkins and industrial_ci: Quick manual; Section For developers

#### Links
* industrial_ci: [ROS wiki](http://wiki.ros.org/industrial_ci); [GitHub repository](https://github.com/ros-industrial/industrial_ci)
* [industrial_ci documentation](https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst)
* [Jenkins](https://jenkins.io/)
* [Jenkins plugins](https://plugins.jenkins.io/)
* [rosdep](http://wiki.ros.org/ROS/Tutorials/rosdep)
* [REP 126](http://www.ros.org/reps/rep-0126.html)
* [rosinstall file format](http://docs.ros.org/independent/api/rosinstall/html/rosinstall_file_format.html)
* The ROS build farm - what it can do for me [pdf](http://roscon.ros.org/2016/presentations/ROSCon2016 Build Farm.pdf); [video](https://vimeo.com/187705230)
* buildfarm [web](http://wiki.ros.org/buildfarm) / [ros_buildfarm2](https://github.com/ros2/ros_buildfarm_config)
* [ROS and CI](http://wiki.ros.org/CIs)

#### Related patterns
* Integrating tests in the build
* MIL testing
* Best Practices
* Regression Testing
* Pre-release testing

### Pattern 11: Replay testing
#### Name
#### Context
#### Problem
#### Forces
#### Solution
#### Stakeholders
#### Links
#### Related patterns

### Pattern 12: Model-in-the-Loop Testing with Specialized Simulator
#### Name
#### Context
#### Problem
#### Forces
#### Solution
#### Stakeholders
#### Links
#### Related patterns

### Pattern 13: Model-in-the-Loop Testing with Gazebo
#### Name
#### Context
#### Problem
#### Forces
#### Solution
#### Stakeholders
#### Links
#### Related patterns

# Bugs Hunt Report

