---
title: Add a command to Ruboty to search GitHub issues/pull requests
published: true
date: '2022-10-21T11:31:04.000Z'
tags: 'Ruby,Ruboty,GitHub'
canonical_url: 'https://qiita.com/mziyut/items/d9546a166da9a48919b2'
description: Introduction   Qiita Inc. is using Ruboty as a Slack bot as...
id: 1226190
---

## Introduction

Qiita Inc. is using Ruboty as a Slack bot as Qiitan.

https://qiita.com/tyamagu/items/9aa1bfd98d7ae69e614f

Qiitan (Ruboty) needs to search GitHub issues/pull requests, so I quickly added `search issues` command to "ruboty-qiita-github".

Please refer to Qiitan when you add new commands to Qiitan (Ruboty) in the future.

## Add a command to search GitHub issues/pull requests.

### Check the GitHub API

First, check the Interface to search GitHub issues/pull requests

GitHub REST API can be found in the GitHub Docs

https://docs.github.com/en/rest

The API we need is Search issues and pull requests.
It's hard to list all the parameters and query parameters you'll need, so here are the docs
[Search issues and pull requests | Search - GitHub Docs](https://docs.github.com/en/rest/search#search-issues-and-pull-requests)

Also, for query parameters `q`, I recommend you to refer to the following docs :thumbsup:
[Filtering and searching issues and pull requests - GitHub Docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/filtering-and-searching-issues-and-pull-requests)

The APIs listed in the GitHub REST API can be easily checked with the GitHub CLI, so let's check them out

In this case, we'll retrieve one issue/pull request from `usrt:mziyut` (@mziyut's repository), `is:open` (issue/pull request is open), and `is:public` (public repository). Searching for an issue/pull request in `is:public` (public repository) in `is:open` (issue/pull request is open)

```zsh
% gh api -H "Accept: application/vnd.github+json" '/search/issues?q=user:mziyut+is:public+is:open&per_page=1'
```

The execution result will be as follows

```json
{
  "total_count": 31,
  "incomplete_results": false,
  "items": [
    {
      "url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/issues/7",
      "repository_url": "https://api.github.com/repos/mziyut/honkit-plugin-prism",
      "labels_url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/issues/7/labels{/name}",
      "comments_url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/issues/7/comments",
      "events_url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/issues/7/events",
      "html_url": "https://github.com/mziyut/honkit-plugin-prism/pull/7",
      "id": 1410719907,
      "node_id": "PR_kwDOIJ_6M85A4onc",
      "number": 7,
      "title": "Bump @types/node from 18.8.3 to 18.11.0",
      "user": {
        "login": "dependabot[bot]",
        "id": 49699333,
        "node_id": "MDM6Qm90NDk2OTkzMzM=",
        "avatar_url": "https://avatars.githubusercontent.com/in/29110?v=4",
        "gravatar_id": "",
        "url": "https://api.github.com/users/dependabot%5Bbot%5D",
        "html_url": "https://github.com/apps/dependabot",
        "followers_url": "https://api.github.com/users/dependabot%5Bbot%5D/followers",
        "following_url": "https://api.github.com/users/dependabot%5Bbot%5D/following{/other_user}",
        "gists_url": "https://api.github.com/users/dependabot%5Bbot%5D/gists{/gist_id}",
        "starred_url": "https://api.github.com/users/dependabot%5Bbot%5D/starred{/owner}{/repo}",
        "subscriptions_url": "https://api.github.com/users/dependabot%5Bbot%5D/subscriptions",
        "organizations_url": "https://api.github.com/users/dependabot%5Bbot%5D/orgs",
        "repos_url": "https://api.github.com/users/dependabot%5Bbot%5D/repos",
        "events_url": "https://api.github.com/users/dependabot%5Bbot%5D/events{/privacy}",
        "received_events_url": "https://api.github.com/users/dependabot%5Bbot%5D/received_events",
        "type": "Bot",
        "site_admin": false
      },
      "labels": [
        {
          "id": 4678247549,
          "node_id": "LA_kwDOIJ_6M88AAAABFthkfQ",
          "url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/labels/dependencies",
          "name": "dependencies",
          "color": "0366d6",
          "default": false,
          "description": "Pull requests that update a dependency file"
        },
        {
          "id": 4678247557,
          "node_id": "LA_kwDOIJ_6M88AAAABFthkhQ",
          "url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/labels/javascript",
          "name": "javascript",
          "color": "168700",
          "default": false,
          "description": "Pull requests that update Javascript code"
        }
      ],
      "state": "open",
      "locked": false,
      "assignee": null,
      "assignees": [],
      "milestone": null,
      "comments": 0,
      "created_at": "2022-10-17T01:17:11Z",
      "updated_at": "2022-10-17T01:17:13Z",
      "closed_at": null,
      "author_association": "NONE",
      "active_lock_reason": null,
      "draft": false,
      "pull_request": {
        "url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/pulls/7",
        "html_url": "https://github.com/mziyut/honkit-plugin-prism/pull/7",
        "diff_url": "https://github.com/mziyut/honkit-plugin-prism/pull/7.diff",
        "patch_url": "https://github.com/mziyut/honkit-plugin-prism/pull/7.patch",
        "merged_at": null
      },
      "body": "Bumps [@types/node](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/HEAD/types/node) from 18.8.3 to 18.11.0.\n<details>\n<summary>Commits</summary>\n<ul>\n<li>See full diff in <a href=\"https://github.com/DefinitelyTyped/DefinitelyTyped/commits/HEAD/types/node\">compare view</a></li>\n</ul>\n</details>\n<br />\n\n\n[![Dependabot compatibility score](https://dependabot-badges.githubapp.com/badges/compatibility_score?dependency-name=@types/node&package-manager=npm_and_yarn&previous-version=18.8.3&new-version=18.11.0)](https://docs.github.com/en/github/managing-security-vulnerabilities/about-dependabot-security-updates#about-compatibility-scores)\n\nDependabot will resolve any conflicts with this PR as long as you don't alter it yourself. You can also trigger a rebase manually by commenting `@dependabot rebase`.\n\n[//]: # (dependabot-automerge-start)\n[//]: # (dependabot-automerge-end)\n\n---\n\n<details>\n<summary>Dependabot commands and options</summary>\n<br />\n\nYou can trigger Dependabot actions by commenting on this PR:\n- `@dependabot rebase` will rebase this PR\n- `@dependabot recreate` will recreate this PR, overwriting any edits that have been made to it\n- `@dependabot merge` will merge this PR after your CI passes on it\n- `@dependabot squash and merge` will squash and merge this PR after your CI passes on it\n- `@dependabot cancel merge` will cancel a previously requested merge and block automerging\n- `@dependabot reopen` will reopen this PR if it is closed\n- `@dependabot close` will close this PR and stop Dependabot recreating it. You can achieve the same result by closing it manually\n- `@dependabot ignore this major version` will close this PR and stop Dependabot creating any more for this major version (unless you reopen the PR or upgrade to it yourself)\n- `@dependabot ignore this minor version` will close this PR and stop Dependabot creating any more for this minor version (unless you reopen the PR or upgrade to it yourself)\n- `@dependabot ignore this dependency` will close this PR and stop Dependabot creating any more for this dependency (unless you reopen the PR or upgrade to it yourself)\n\n\n</details>",
      "reactions": {
        "url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/issues/7/reactions",
        "total_count": 0,
        "+1": 0,
        "-1": 0,
        "laugh": 0,
        "hooray": 0,
        "confused": 0,
        "heart": 0,
        "rocket": 0,
        "eyes": 0
      },
      "timeline_url": "https://api.github.com/repos/mziyut/honkit-plugin-prism/issues/7/timeline",
      "performed_via_github_app": null,
      "state_reason": null,
      "score": 1.0
    }
  ]
}
```

This time, once you have defined the API required to search for issues/pull requests and understand the items to be returned, let's move on to the next step

### Check the Gem `octkit`.

In `ruboty-qiita-github`, we use `octokit/octokit.rb` to retrieve and manipulate data via GitHub API.

https://github.com/octokit/octokit.rb

```ruby
      # Search issues
      #
      # @param query [String] Search term and qualifiers
      # @param options [Hash] Sort and pagination options
      # @option options [String] :sort Sort field
      # @option options [String] :order Sort order (asc or desc)
      # @option options [Integer] :page Page of paginated results
      # @option options [Integer] :per_page Number of items per page
      # @return [Sawyer::Resource] Search results object
      # @see https://developer.github.com/v3/search/#search-issues-and-pull-requests
      def search_issues(query, options = {})
        search 'search/issues', query, options
      end
```

https://github.com/octokit/octokit.rb/blob/e915332a3ab7aa2de3442b0a7fedd6b6ecb3b928/lib/octokit/client/search.rb#L37-L49

We have confirmed that we can hit the endpoint we just checked with the GitHub REST API.

### Add a command to `ruboty-qiita-github`.

After confirming the required GitHub API, define a command in `ruboty-qiita-github`.
Modify and create the following files to define commands

- Create a new file
  - Create a new file `lib/ruboty/github/actions/search_issues.rb`
- Change
  - Change `lib/ruboty/handlers/github.rb` New
  - Create new `lib/ruboty/github.rb` Change `lib/ruboty/handlers/github.rb` New

I will explain the roles and other details using actual code as an example.

#### lib/ruboty/github/actions/search_issues.rb

First, let's define the process for `search issues` Create a new file `lib/ruboty/github/actions/search_issues.rb`.

The process flow is as follows...

- Check if GitHub Token is registered
  - If not, return an error
- Get data from search_issues API via octkit (client in code)
- Format the results and return them

```ruby
# frozen_string_literal: true

module Ruboty
  module Github
    module Actions
      class SearchIssues < Base
        def call
          if has_access_token?
            search
          else
            require_access_token
          end
        end

        private

        def search
          results = search_issues.items.each_with_object(["Searched: '#{query}'"]) do |item, object|
            repository_url = item[:repository_url].delete_prefix('https://api.github.com/repos/')

            object << "[#{repository_url}##{item[:number]}] #{item[:title]} (#{item[:user][:login]})\n#{item[:html_url]}"
          end

          message.reply(results.join("\n"))
        rescue Octokit::Unauthorized
          message.reply('Failed in authentication (401)')
        rescue Octokit::NotFound
          message.reply('Could not find that repository')
        rescue StandardError => e
          message.reply("Failed by #{e.class}")
        end

        def query
          message[:query]
        end

        def search_issues
          client.search_issues(query)
        end
      end
    end
  end
end
```

https://github.com/increments/ruboty-qiita-github/blob/7df067035fd2f735359980b1265e613029334feb/lib/ruboty/github/actions/search_issues.rb#L1-L43

Once you have defined the process, let's move on to the next step

#### lib/ruboty/github.rb

Require `lib/ruboty/github/actions/search_issues.rb` that you just created.

```ruby
require 'ruboty/github/actions/search_issues'
```

https://github.com/increments/ruboty-qiita-github/blob/7df067035fd2f735359980b1265e613029334feb/lib/ruboty/github.rb#L14

#### lib/ruboty/handlers/github.rb

`lib/ruboty/handlers/github.rb` is responsible for dispatching commands received by Ruboty to the defined process.
The following two processes are added this time

```ruby
      on(
        /search issues "(?<query>.+)"/,  # Describe the conditions of the command to be added
        name: "search_issues",           # Describe the name of the method to be dispatched.
        description: "Search an issue",  # Describe command description
      )
```

```ruby
      def search_issues(message)
        Ruboty::Github::Actions::SearchIssues.new(message).call
      end
```

Now, when you send `search issues "user:foo"` to Ruboty, it will execute the specified process.

#### Others

If necessary, you can also write tests for the commands you have added :muscle:

##### spec/ruboty/handlers/github_spec.rb

https://github.com/increments/ruboty-qiita-github/blob/a3fb3554d9e7bcf5b4e8cfdb7070ae7f92120f88/spec/ruboty/handlers/github_spec.rb#L92-L124

## Finally.

This time, I added a simple command to Ruboty plugin `ruboty-qiita-github`.
It is easy to add commands, so why don't you try to develop Ruboty plugin?

## Reference

https://github.com/increments/ruboty-qiita-github/pull/7

https://github.com/increments/ruboty-qiita-github
