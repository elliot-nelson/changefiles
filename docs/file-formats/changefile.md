---
sidebar_position: 1
---

# Changefile

A _changefile_ is a small, structured file that represents one or more unreleased changes. This file is checked into source control along with the code changes it describes, and it is "consumed" (deleted) when those changes are released.

## File format and naming

Typically, a changefile is stored as a simple JSON file, in a dedicated folder of your repo (such as `.changes`). Changefiles should be written once, and then consumed once -- appending new changes to an existing changefile is frowned upon because it's a potential source of git conflicts (although editing an existing changefile can and does happen -- for example, to fix a typo in the change description).

```json title=".changes/@acme/dynamite/feature-310_2021-01-31-09-10.json"
{
  "changes": [
    {
      "project": "@acme/dynamite",
      "type": "patch",
      "description": "Increase explosion radius by 10%"
    }
  ]
}
```

You'll notice the file path contains elements of the project name, the originating branch name, and a timestamp. These path elements ensure that as people check in changes, there are no conflicts in the changefile folder. Changefiles are not intended to be created by hand, but rather by _changefile tooling_ you'll add to your repo. This tooling will create changefiles for you when you run it, or perhaps by automatically prompting you when you push your changes.

## Change record properties

The changefile contains a single property, `changes`, which is an array of _change records_. Each change record represents a specific change for a specific project in your repo, which will eventually appear in the project's changelog.

Property | Required | Description
---      | ---      | ---
project  | yes      | The project that was changed. This should be an identifier that uniquely identifies a project in your repo, such as the package name of a TypeScript project, the project name of a Swift project, or the project path of an Android project.
type     | yes      | The semver type for the change: `major`, `minor`, `patch`, `hotfix`, or `none`. A change record with type `none` will eventually be consumed, but will be ignored when releasing changes.<br/><br/>Some tools may also support other more specific "types", in addition to the ones suggested above.
description | yes   | A description of the change. This description should be written for the user of your project (unlike your commit message, which is written for the other developers that work on your project).<br/><br/>For changes of type `none`, leave this string blank.
custom   | optional | An object containing string keys and string values, containing additional custom fields attached to the change record. Tools can use this space to specify data like author email addresses, ticket ids, or any other metadata that might be useful to store along with change records.

## Storing multiple changes

The changefile format is intentionally flexible, and can store changes across multiple projects.

For example, this changefile for a fictional polyglot monorepo contains a batch of related changes to a Java project, a TypeScript project, and a Swift project:

```json title=".changes/feature-310_2021-01-31-09-10.json"
{
  "changes": [
    {
      "project": "com.acme.boom:boom-server",
      "type": "minor",
      "description": "Client ids are now returned in failure responses"
    },
    {
      "project": "@acme/boom-client-js",
      "type": "patch",
      "description": "Fixed a typo in response logs"
    },
    {
      "project": "BoomClientKit",
      "type": "patch",
      "description": "Fixed a typo in response logs"
    }
  ]
}
```

:::caution

Although this flexibility can be useful, it's recommended to store _one changefile per changed project_ for a given set of projects.

The example changefile above is only useful if your tooling allows you to consume and publish this changefile atomically, which would require releasing changes for multiple languages with one tool (an unusual approach). The only alternative would be to consume _part_ of the changefile, which is frowned upon because it's a potential source of git conflicts.

:::

## Alternate formats

Although JSON is the recommended file format for changefiles, other files that can be trivially translated to and from JSON work equally well. For some languages, if all of your existing configuration files are in YAML or TOML, and you have changefile tooling that supports those formats, you can avoid adding a new file format in the repo.

### YAML

YAML is an obvious alternative to JSON, and works well for changefiles. Example:

```yaml title=".changes/@acme/dynamite/feature-310_2021-01-31-09-10.yaml"
changes:
  - project: "@acme/dynamite"
    type: "patch"
    description: "Increase explosion radius by 10%"
  - project: "@acme/dynamite"
    type: "minor"
    description: "Added new explosion type"
```

### TOML

TOML is not an ideal file format for changefiles, as it has no easy way to describe arrays of objects. Here's one approach to representing changefiles with TOML (but it will require custom support from your changefile tooling):

```toml title=".changes/@acme/dynamite/feature-310_2021-01-31-09-10.toml"
[change1]
project = "@acme/dynamite"
type = "patch"
description = "Increase explosion radius by 10%"

[change2]
project = "@acme/dynamite"
type = "minor"
description = "Added new explosion type"
```

### Markdown

An alternate approach to storing changefiles is to store each changefile as a markdown file, using frontmatter to represent the changefile properties (with the body of the markdown file representing the description). This approach restricts your changefile format to one change record per file, but does make it easier to provide large, detailed change descriptions.

Example:

```markdown title=".changes/@acme/dynamite/feature-310_2021-01-31-09-10.md"
---
project: "@acme/dynamite"
type: "patch"
---

Increase explosion radius by 10%
```
