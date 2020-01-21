---
description: >-
  Document manual steps for plugin version bumps, tagging, bundling, publishing
  to marketplace and transition to automation.
---

# Demo Presentation

## List of steps \(happy path details\)

1. Bump the current version of an existing plugin
2. Tag / Cut a version of a plugin for release
3. Bundle a plugin version to a Mattermost server release
4. Publish a bumped plugin version to the Plugin Marketplace
   1. create Jira ticket for Toolkit
5. Publish a new plugin to the Plugin Marketplace
   1. Release to community.mattermost.com

## Steps Automated Now

 `Matterbuild` - \[docs\]\([https://github.com/mattermost/mattermost-developer-documentation/pull/439/files](https://github.com/mattermost/mattermost-developer-documentation/pull/439/files)\)

1. Tag / Cut a release `/mb cutPlugin --tag vX.Y.Z --repo mattermost/mattermost-plugin-<PLUGIN>`

## Next Steps

* Improve `Matterbuild` to bump versions \(`/mb bumpPlugin`\)
* Start cutting releases from Mattermost \(`/mb cutPlugin`\)
* Create Marketplace PR from Mattermost \(`/mb marketPlugin`\)
* Create bundle plugin list PR from Mattermost \(`/mb bundlePlugin`\)

## Workflow Integration Plan

* action from GH -&gt; do this
* 
