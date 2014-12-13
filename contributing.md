Contributing Guidelines
=======================

This document details the process you should follow when contributing code to any Nice project.

Contributing to a Nice project
------------------------------

The master branch contains active development. It should be considered unstable. Versions are tagged in each project's repository.


### Step by step

#### 1. Fork this repository.

Fork the repository on GitHub.

#### 2. Clone the repository and checkout the master branch.

Change `nice-framework` below to whichever project you are working on: nice-twig, nice-security, etc.

``` bash
git clone git@github.com/path/to/your/fork nice-framework
cd nice-framework
git checkout master
```

#### 3. Create a new branch.

``` bash
git checkout -b new-and-awesome
```

#### 4. Implement the feature, publish your feature branch to your forked repository.

``` bash
git add .
git commit -m "Made some changes"
git push origin new-and-awesome
```

#### 5. Create a pull request from your feature branch to the master branch of this project.

From your repository on the GitHub interface, click the pull request button. Select your feature branch and ensure
the master branch of the Nice project you're contributing to is selected.

### Additional Info

All Nice projects follow [Semantic Versioning](http://semver.org).


Contributing to Nice documentation
----------------------------------

Coming soon.
