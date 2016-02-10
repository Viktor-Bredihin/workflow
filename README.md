versioning with git on a intern server (gitlab)
========================

### Install

* https://github.com/gitlabhq/gitlabhq/blob/master/doc/install/installation.md


access model for every developer to restrict access for some branches etc.
========================

### Using gitlab we can easy protect our branches

* https://github.com/gitlabhq/gitlabhq/blob/master/doc/workflow/protected_branches.md


using Git-Flow for Branching
========================

(I'm not the creator of this process, I just read this article http://nvie.com/posts/a-successful-git-branching-model about two years ago, and start using it)

### Two main branches

* master - production server is always synchronized with master branch. 

* develop - the main branch for development. Contains last changes for the next release.

### Supporting branches

* Feature branches - for a new features

* Release branches - for a new release. On a stage when develop branch is ready or almost ready we are creating release branch, which can exist some time before go in live. Before release branch is live we can make minor changes

* Hotfix branches - branches for hot fixes (unplanned releases)

### Feature branches

* May branch off from develop

* Must merge back into develop

* Branch naming convention anything except master, develop, release-*, or hotfix-*

* typically exist in developer repos only

### Release branches

* May branch off from develop

* Must merge back into develop and master

* Branch naming convention release-*

### Hotfix branches

* May branch off from master

* Must merge back into develop and master

* Branch naming convention hotfix-*


how to handle hotfixes?
========================

* Create branch based on master

* create needed changes

* merge hotfix branch to master

* create new version tag

* merge master to develop branch

* remove hotfix branch


release only a part of all new features to the live project
========================

* on some state of develop branch we must create release branch, and merge this branch to master when ready (recommended way)

* if for some reasons we can't use recommended way, we should create a release branch based on master and then merge needed features to this branch (unrecommended way)


rollback functionality if something is broken in live app to an earlier verison
========================

* Since for every release we have a different tag, named as project version (eg 1.2.2)
```
git revert tag (previous version)
```

working with at least 3 areas (developer private area, staging area, integration area, live area etc.)
========================

* Developers work on bugs and features in separate branches on local environment (dev)

* Once features are implemented, they are merged into the develop branch and deployed to the Staging environment for quality assurance and testing.

* On the release date, the development branch is merged into production and then deployed to the Production environment.


how to integrate extern developers with only partial access to the project
========================

* create user permissions needed for this developer using gitlab


Team members
========================

### Lead developer

* development

* unit/functional testing

* distribute tasks between team members

* code review

* choice of technical tools

* deployment

### Front-end developer

* front-end development

* unit/functional testing

### Back-end developer

* back-end development

* unit/functional testing

### QA

* testing

### admin

* Install software / Create a backup and recover policy / Update system / Setup security policies for users


CI server
========================

* http://doc.gitlab.com/ce/ci/quick_start/README.html


Permissions
========================

* Only Lead developer has permissions to push into develop and master branches

* Only Lead developer has permissions to remove a branches

* Only Lead developer has permissions to administrate CI server


Git hooks
========================

* deployment on production server on push to the master branch

* deployment on staging server on push to the develop branch


Process
========================

### First time

* Admin - Rent a server

* Lead developer with help of admin - install gitlab (described in 'versioning with git on a intern server' section)

* Lead developer - create main branches (master, develop)

* Lead developer - create branch access model using gitlab (described in security section)

* Lead developer with help of admin - set up staging server

* Lead developer set up git hooks (described in git hooks section)

* Lead developer - setup CI server (described in 'CI Server' section)

* Lead developer - set up user permissions for CI server (described in security section)


### Before sprint

* Product owner creates wishlist

* Lead developer creates tasks based on wishlist

* Lead developer tasks between team members

* team members provide an estimation for every task

* Lead developer create sprint

### During sprint

* team member working on his task in a separated branch

* when branch is ready team member creates merge request on Lead developer

* Lead developer review the task, merge it to develop branch, deploy to the staging and assign qa

* after qa confirmed that task is finished, task can be closed and task-branch should be removed

### Before release

* create release branch based on develop branch

### Release

* merge release branch to master
```
git merge --no-ff release-1.2
```

* create tag
```
git tag -a 1.2
```

* merge with develop
```
git checkout develop
git merge --no-ff release-1.2
```

* delete release branch
```
git branch -d release-1.2
```
