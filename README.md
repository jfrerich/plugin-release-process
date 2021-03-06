---
description: 'Mattermost, Inc.   Jason Frerich   January 2020'
---

# Plugin Release Process

[GitBook Entry](https://app.gitbook.com/@jason-frerich/s/plugin-release-process)  
[GitHub Page](https://github.com/jfrerich/plugin-release-process)

![horizontal line](.gitbook/assets/0.png)

* [OVERVIEW](./#overview)
* [GOALS](./#goals)
* [SCOPE](./#scope)
* [PLUGIN RELEASE FLOWS](./#plugin-release-flows)
  * [Considerations when bumping and releasing a plugin version](./#considerations-when-bumping-and-releasing-a-plugin-version)
  * [Bump current version of an existing plugin](./#bump-current-version-of-an-existing-plugin)
  * [Tag/cut a version of a plugin for release](./#tagcut-a-version-of-a-plugin-for-release)
  * [Bundle a plugin release version to a Mattermost server release](./#bundle-a-plugin-release-version-to-a-mattermost-server-release)
  * [Publish a plugin release version to the Plugin Marketplace](./#publish-a-plugin-release-version-to-the-plugin-marketplace)
  * [Plugin Intake](./#plugin-intake)
    * [Create PR for Initial Review](./#create-pr-for-initial-review)
    * [Plugin Review Checklist](./#plugin-review-checklist)
  * [Publish a new plugin to the Plugin Marketplace](./#publish-a-new-plugin-to-the-plugin-marketplace)
* [FUTURE ENHANCEMENTS](./#future-enhancements)
  * [Automation \(WIP\)](./#automation-wip)
* [SECURITY RELEASE / UPGRADE PROCESS](./#security-release--upgrade-process)
  * [Updating Security Alerts Through CLI](./#updating-security-alerts-through-cli)
  * [Updating Security Alerts Through GitHub](./#updating-security-alerts-through-github)

## OVERVIEW

The release process for modifying plugin versions and plugin version dependencies currently requires a developer to perform several non-automated tasks. Bumping, tagging, releasing, publishing, and bundling \(preloading\) versions are not necessarily complicated, but a formal set of steps is required and should be followed. This document describes those processes in detail and will be used to help standardize and automate these flows in the future.

## GOALS

1. Define the steps required to bump, tag, release, publish, and bundle plugin versions.
2. Through defining specific tasks during the process we will be able to define protocols and identify areas for automation improvements.

## SCOPE

This document covers the current steps required to perform the following tasks

1. Bump the current version of an existing plugin
2. Tag a version of a plugin for release
3. Bundle a plugin version to a Mattermost server release
4. Publish a bumped plugin version to the Plugin Marketplace
5. Publish a new plugin to the Plugin Marketplace
   1. Release to community.mattermost.com

The Future Enhancements section of this document describes additional suggestions for automating some of these tasks.

## PLUGIN RELEASE FLOWS

### Considerations when bumping and releasing a plugin version

\(**`TODO:`** Need permanent place to place the spreadsheet\)

* Compare commits from last bump / tagged release
* Documentation \(README.md\) changes aren’t necessarily vital
  * Documentation through bundled releases aren’t viewable through the app and users will be looking at the latest master commit in the github repo
* Keep spreadsheet up to date
* The PR for the version bump \(in the plugin repo\) does not mean that is the last commit to get tagged. This step only bumps the version. The tagging step actually determines the commit that is tagged with the release tag

### Bump current version of an existing plugin

\(**`TODO:`** First determine the next version number\)

* Feature or patch bumping determined by commits being added from previous release tag
* Look through existing Issues and PRs and make sure Milestone label added for items to be included with release
* Update in Google Spreadsheet

\(**`TODO:`** Use SemVer \`v.X.feature.patch\` to determine which to bump\)  
\(**`TODO:`** must have admin access\)

Changing the version of a plugin is no different than any other PR and requires 2 Developers and 1 QA Reviewer. Also add a PM review to verify the plugin change.

**Create a commit with new version**

* Verify No existing Security issues \(using [Updating Security Alerts Through CLI](./#updating-security-alerts-through-cli)\)
  * If security issues exist, submit PR and merge before bumping version
* `git pull` on the master branch in your local plugin repository to prepare for creating a branch
* `git checkout -b bump-version-vX.X.X` to create a branch to perform the necessary edits
* Edit `plugin.json` and bump the plugin’s `version` field in the json object. This file is located in the root directory of the plugin
*  Modify `release_notes_url` to match the new bumped version
  * Example: "release\_notes\_url": "[https://github.com/mattermost/mattermost-plugin-todo/releases/tag/v0.3.0](https://github.com/mattermost/mattermost-plugin-todo/releases/tag/v0.3.0)",
* `make apply` to update the plugin manifest files. The manifest files exist for server and webapp plugin directories. 
  * Possible manifest files are `server/manifest.go`, `webapp/src/manifest.ts` and `webapp/src/manifest.js`
* `git status` verify no untracked files will get added with following git -A 
* `git add -A` if the make target builds successfully to add the files for commiting
* `git commit -m “Bump version to 1.1.2”` to commit the new commit
* `git push --set-upstream origin bump-version-1.1.2` to push the commit to origin and prepare the PR

**Create a PR against the master branch of the plugin repository**

* **Use as automation template** 
  * [**https://github.com/mattermost/mattermost-plugin-custom-attributes/pull/21**](https://github.com/mattermost/mattermost-plugin-custom-attributes/pull/21)
* **PR Title**: `Bump version to X.X.X`
* **PR Summary**: `Bump version to X.X.X` \(use the same text for the PR summary\)
  * Add any further reasoning or description for version bump \(if necessary\)
* Add 2 Developers, 1 PM, and 1 QA for review
* Add `Dev Review`, `QA Review`, and `PM Review` labels
* Add Release Notes Proposal in the PR so others can verify before moving to tag/release [https://github.com/mattermost/mattermost-plugin-custom-attributes/pull/21](https://github.com/mattermost/mattermost-plugin-custom-attributes/pull/21)  


  ![](.gitbook/assets/image.png)

### Tag/cut a version of a plugin for release

After the PR for bumping the version of a plugin has been merged, you can now tag the version for release.

`Prerequesite:` In order to cut releases using matterbuild slash commands, you will need to be added to the following config.json

* [https://github.com/mattermost/platform-private/blob/master/matterbuild/config.json](https://github.com/mattermost/platform-private/blob/master/matterbuild/config.json)

Cut the release using the following as an example.  Note this is a slash command for use inside mattermost.

`/mb cutplugin --tag v1.2.0 --repo mattermost-plugin-todo`

To cut a plugin for a specific branch, you can specify the specific SHA commit

`/mb cutplugin --tag v3.0.1 --repo mattermost-plugin-jira --commitSHA 837456086c5c3e21f6056e5c4ad1979d8a42b24d`

CI runs can be viewed at [circleci.com/gh/mattermost](https://circleci.com/gh/mattermost)

If CI jobs complete successfully, a new release will automatically be produced and viewable under the `Releases` tab in the plugin repo

Matterbuild will respond with message upon success.  Now view the release link and update the commit messages.  This is a subjective task where determine if a commit is a feature of enhancement.  Edit the release messages and arrange accordingly.

The next steps are to add the plugin to the marketplace.  The instructions are included in the return message upon a successful `cutplugin` command.

* Matterbuild created  the release notes with the following command
* `git log --pretty=oneline --abbrev-commit --no-decorate --no-color $(git describe --tags --abbrev=0)..HEAD`

### Bundle a plugin release version to a Mattermost server release

\(**`TODO:`** Branch name - determine naming convention for PR branch name\)  
\(**`TODO:`** PR - determine naming convention for PR Title\)  
\(**`TODO:`** PR - determine naming convention for PR Summary\)  
\(**`TODO:`** PR - inside summary get list of from -&gt; to versions with automation\)

Plugins that are released with Mattermost are called bundled plugins. These plugins are included with the software and need only to be configured.

* `git pull` the latest master branch on mattermost-server
* Create a new branch so you can modify the plugin versions
  * `git checkout -b bundle-plugins-v5.20`
  * Use branch naming convention `bundle-plugins-vX.XX`
* Edit Makefile
  * Locate `# Plugins Packages` comment
  * Modify plugin release versions
* Create PR against master branch with following:
  * **Title:** Update bundled plugins for vX.XX
  * **Summary:** List of updated plugins
    * Ideally includes from version -&gt; to version for each plugin

### Publish a plugin release version to the Plugin Marketplace

The steps to have a plugin version added the marketplace are included with the success of an `/mb cutplugin` slash command.

![](.gitbook/assets/image%20%283%29.png)

If a new plugin is being added to the marketplace, you will need to add the repo name to the repositoryNames array in `cmd/generator/main.go`

## Plugin Intake

### Create PR for Initial Review

\(**`TODO`**: See if `hub` CLI can automate PR creation\)  
\(**`TODO`**: need to instruct new plugins to initialize a commit that can be easily compared for PR. options are starter-plugin commit or empty branch. Git compare dirs to see if first commit was starter plugin or included changes from author\)  
\(**`TODO`**: Review anti-pattern link [Do Not Issue Pull Requests From Your Master Branch - Learn, Converse, Share](https://blog.jasonmeridth.com/posts/do-not-issue-pull-requests-from-your-master-branch/)  
\(**`TODO`**: Cannot assign reviewers easily in forked branch in my personal repo. Try forking to mattermost  
\(**`TODO`**: Don’t have forking permissions to Mattermost Organization.

This is a brief overview of the steps required to create the initial PR for Plugin Intake Review.

* **Fork the repo so no possibility to mess up authors repo**
  * use GitHub \(possibly hub cli\)
* **Insert empty root commit into master \(probably over kill\)**
  * most plugins will start from starter-template
    * skip this section if using first commit from authors master branch
  * `git checkout --orphan emptyroot`
  * `git rm -rf .`
  * `git commit —allow-empty -m ‘empty root commit for PR’`
  * `git rebase —onto emptyroot —root master`
  * `git branch -d emptyroot`
* **Create branch: `initial-commit-for-PluginReview-compare`**
  * _This branch is for creating PR from `PluginReview` branch to view all changes to the repo from the `intial commit`_
  * `git checkout <commit-hash>`
    * choose commit to compare against latest master
    * could use empty root commit \(overkill\) or initial commit in repo \(likely from plugin-starter template\)
  * `git checkout -b initial-commit-for-PluginReview-compare`
    * create as new branch
  * `git push --set-upstream origin initial-commit-for-PluginReview-compare`
    * push to remote
* **Create branch: `PluginReview-master-dev-copy`**
  * _This branch is for doing the review.  A PR will be created against the initial branch to view all the changes from the plugin-starter-template_
  * `git checkout master`
  * `git checkout -b PluginReview-master-dev-copy`
  * `git push --set-upstream origin PluginReview-master-dev-copy`
* **GH create PR**
  * title: `Initial Plugin Review`
  * base: `initial-commit-branch-for-PR`
  * compare: `PluginReview-master-dev-copy`
* **Create Initial Plugin Review Issue in Repo**
  * Use the following as a template
  * Track checklist items here
  * Issue to be closed after plugin review is completed
  * [example](https://github.com/mattermost/mattermost-plugin-agenda/issues/5)
* **Review checklist items and start creating PRs against `master`**
* **Create PR `PluginReview-master-dev-copy` to `master` to view all proposed changes**

### Plugin Review Checklist

\(**`TODO`**: when automate, add checklist into description section\)  
\(**`TODO`**: How to automate `min_server_version` verification [link](https://community.mattermost.com/core/pl/1agmru9n67ffxkmz7aet51ptbw%29%29%20%20%20%28**`TODO`**:%20if%20plugin%20began%20with%20mattermost-plugin-starter-template,%20find%20way%20to%20sync%20with%20latest%20start-template%20files%20%28Makefile,%20.lintrc%20files,%20.etc%29%20\)

* README.md
  * Installation instructions provided, detailed, and accurate
  * Use cases are defined and documented
* Plugin Setup Files
  * plugin.json
    * `id` - try to make unique \(Ex. `calendar` likely to collide as \# plugins grow\)
    * `min_server_version` - verify works using specified version
  * server/plugin.go
* Installation
  * compare Makefile to latest mattermost-plugin-starter-template
  * `make` executes without errors
  * no lint errors
* Security
  * `npm audit` returns with no issues
* Best Practices
  * recognize anti-patterns
  * variable / function naming \(possibly automate check with go or write AST script\)
    * vars: fooBar
    * Public Methods: FooBar\(\)
    * Private Methods: fooBar\(\)
  * idiomatic Go
    * err handling

## Plugin Intake Process

\(**`TODO`**: create as a ticket in an common area.  don't use make solar-lottery the golder template\)

This is the process by which Mattermost takes ownership of a plugin and most the plugin under the Mattermost GitHub Organization.  The intake process is a list of checks that need to be completed.  The list is created as an issue in the plugin repo and updated as items are completed. Use \[this issue template\]\([https://github.com/jfrerich/plugin-release-process/issues/1](https://github.com/jfrerich/plugin-release-process/issues/1)\) for each intake.

The intake process in this document originated from this Jira ticket [https://mattermost.atlassian.net/browse/MM-21180](https://mattermost.atlassian.net/browse/MM-21180)

Plugin must first pass intake review process

## FUTURE ENHANCEMENTS

This section lists possible enhancements to the release flow. By gathering the details of the flow and breaking into specific tech areas, we can start create a design spec and start building automation for the entire plugin release flow.

There is an open Ticket to automate syncing plugins with \[mattermost-plugin-starter-template\]\([https://github.com/mattermost/mattermost-plugin-starter-template](https://github.com/mattermost/mattermost-plugin-starter-template)\)

* [https://mattermost.atlassian.net/browse/MM-21722](https://mattermost.atlassian.net/browse/MM-21722)

### Automation \(WIP\)

\[mattermost-plugin-sync\]\([https://github.com/jfrerich/mattermost-plugin-sync](https://github.com/jfrerich/mattermost-plugin-sync)\)

1. Bumping Plugin Version
2. Tagging version for release
3. Bundling plugins with mattermost
4. Publishing plugins to Marketplace
5. Filing Jira tickets to toolkit for publishing to marketplace
6. MM command to show bundled releases of current MM-server version
   1. Compare with latest tagged release of repos
   2. Include commit PR history for viewing changes since last tagged release

## SECURITY RELEASE / UPGRADE PROCESS

\(**`TODO`**: Automate checking all released plugins through CLI, cron, or GH webhook event\)  
\(**`TODO`**: User must be repo admin to see and resolve automated security issue\)  
\(**`TODO`**: Need method to hook to tell us when security issue is found\)  
\(**`TODO`**: PR for security updates should be discrete\)  
\(**`TODO`**: investigate `npm ls hoek`\)

Security alerts are displayed when viewing a GitHub repo and are resolved via the automated `dependabot` tool

### Updating npm Dependencies CLI

* `git checkout latest master`
* `git checkout -b bump-dependency-versions`
* `cd webapp/`
  * `npm-check -E -u`  to view the changes interactively

    `npm-check -E -y` to update without interactive
* `git add package-lock.json package-lock.json`
* `git commit -m "Update dependencies"`
* `git push --set-upstream origin bump-dependency-versions`
* Create PR 
  * TItle: `Update Dependencies` \(Will automatically get set\)
  * Summary: Update dependencies

### Updating Security Alerts Through CLI

* `git checkout latest master`
* `git checkout -b npm-audit-fix`
* `cd webapp/`
* `npm audit` - will return list of security issues
* `npm audit fix` - updates package-lock.json dependencies
* `git add package-lock.json`
* `git commit -m "Update dependencies"`
* `git push --set-upstream origin npm-audit-fix`
* Create PR 
  * TItle: `Update Dependencies` \(Will automatically get set\)
  * Summary: &lt;library\_name&gt; &lt;from\_ver&gt; -&gt; &lt;to\_ver&gt;

### Updating Security Alerts Through GitHub

Github displays security alerts when viewing a GitHub repo.

![Security Notification](.gitbook/assets/security-notification.png)

View all alerts by clicking on the `View security alerts` button.

![Security Alerts](.gitbook/assets/security-view-alerts.png)

Clicking on a specific security alert will open the details alert and provide a `Create automated security update` button. Clicking on the button have `dependabot` begin generating an automated security update.

![Security Alert Details](.gitbook/assets/security-serialze-javascript.png)

![Generating automated security update](.gitbook/assets/security-generating-automated-security.png)

