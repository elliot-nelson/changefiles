---
sidebar_position: 2
---

# Changelog

A _changelog_ is a structured file representing the record of released changes for a specific project. The changelog is checked into source control.

The ideal approach for changefile tooling is for each project to have two side-by-side changelogs: the _changelog_, in JSON format (`CHANGELOG.json`), which can be easily parsed and updated by tools, and the _human-readable changelog_, in Markdown format (`CHANGELOG.md`), which is generated from the structured changelog.

This separation of responsibility ensures that you can easily insert, remove, or reorder entries in the changelog with changefile tooling, and that later enhancements to the Markdown format can be easily applied (by re-generating it from your structured changelog).

## File format and naming

By convention, the changelog is named `CHANGELOG.json`.

Here's an example of a short changelog:

```json title="CHANGELOG.json"
{
  "project": "@acme/dynamite",
  "entries": [
    {
      "version": "1.0.1",
      "tag": "@acme/dynamite_v1.0.0",
      "date": "Tue, 12 Jan 2020 18:12:01 GMT",
      "changes": {
        "patch": [
          {
            "description": "Improved explosion radius"
          }
        ]
      }
    },
    {
      "version": "1.0.0",
      "tag": "@acme/dynamite_v1.0.0",
      "date": "Tue, 12 Jan 2020 18:12:01 GMT",
      "changes": {
        "none": [
          {
            "description": "Initial version"
          }
        ]
      }
    }
  ]
}
```

## Consuming changefiles

A common task for changefile tooling is to "consume" changefiles, which generally consists of 3 steps: processing and deleting pending changefiles, inserting new entries at the top of each changelog, and then bumping the project to a new version. Each change record in a consumed changefile becomes part of a new entry in the corresponding changelog.

For example, take this simple changefile with a single change record:

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

To consume this changefile, the tooling will follow these steps:

 - First, the `project` property is discarded (it is specified once at the top of the changelog).
 - Next, we take the array of `changes` and turn it into a map instead, by extracting the `type` property and making it the key (with the value of the new map being an array of all of the change records that had that type).
 - This new `changes` map is then bundled with some additional properties (the new `version`, an optional git `tag`, and a `date` timestamp).
 - Last, this newly created object is inserted at the front of the `entries` key in the changelog.

:::info

If the change record contains other properties (for example, the `custom` property bag), they are copied over into the changelog as well, becoming part of the permanent record for the project.

:::

## Generating markdown

Typically, each time it updates the changelog, your changefile tooling will also regenerate the human-readable changelog. Output format can vary, but normally it is a straightforward conversion of the JSON structure into markdown headers.

For example, the changelog above might generate something like this:

```markdown
# Changelog - @acme/dyamite

## 1.0.1
Fri, 10 Feb 2023 01:18:51 GMT

- _(patch)_ Improved explosion radius

## 1.0.0
Tue, 12 Jan 2020 18:12:01 GMT

 - Initial version
```
