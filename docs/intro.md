---
sidebar_position: 1
---

# Introduction

_Changefiles_ are a way to track changes to projects in a repository, which are then used to bump versions, update changelogs, and release new versions of those projects (for example, publishing them to an NPM or Maven registry).

:::info

This website is not documentation for a specific toolset, but rather a reference specification for changefiles and some associated files, such as structured changelogs and publishing reports. If you're looking for tools that implement some or all of this specification, see the [Tools](./tools) section.

:::

## How does it work?

 - Developers working in your repository make changes to various projects.
 - Using a command-line tool (perhaps by choice, or perhaps auto-prompted at push time), they enter a _change description_ and _bump type_ (think major, minor, and patch, like semver). This creates small, structured _changefiles_ that are checked in along with the corresponding code changes.
 - As changes merge to [trunk](https://trunkbaseddevelopment.com/), a collection of these changefiles builds up in a designated folder.
 - When it's time to publish packages externally, these changefiles are _consumed_: changefiles are deleted, their bump types and change descriptions used to update changelogs and calculate new versions of affected projects. These projects are then published (for example, to an NPM registry), and a commit is pushed to the head of the trunk branch with the changes.

## Why use changefiles?

Using changefiles to track changes in a repository has many benefits.

### Uniquely suited to monorepos

Developers working in a monorepo often touch multiple projects in one "unit of change" (for many companies, the defacto unit of change is a Pull Request). Shorthand that works in small projects, like putting a bump type in a commit message or pull request title, doesn't work when the pull request might be touching 80 projects at once -- or two projects, but with different bump types for each.

### Reclaim your commit messages

The original purpose of commit messages was to inform your teammates what changes you were making. In many companies this purpose has been completely lost because the commit message has been replaced by a soup of bump types, ticket ids, and consumer-facing change messages.

Changefiles allow you to separate the _change description_, a user-facing message that will eventually appear in the changelog, from the _commit message_, a description of the code changes you are making for your fellow developers.

### Change descriptions are first-class citizens

Moving change descriptions out of commit messages, and into your checked-in content, has some other benefits as well. As your coworkers review your pull request, they can see both your changes and that eventual changelog message, and can offer wording suggestions as part of their feedback.

In fact, developers can even adjust a change description for a change after it has been committed but before it has been released (something that they would be loathe to do if it meant rebasing your trunk branch).

### Coherent changes in each commit

A repository built on changefiles has an interesting property, which is that changes are _coherent_: if you checkout any commit from trunk, the behavior of the code in that commit is completely described by the _project changelogs_ plus the _pending changefiles_. You don't need to provide a previous tag or do a diff between two release branches, because the cloned repository itself contains all of the information you need.

This property of changefiles is a boon for tool authors, especially if you choose to store metadata like JIRA tickets in your changefiles instead of in your commit messages.

### Blah
