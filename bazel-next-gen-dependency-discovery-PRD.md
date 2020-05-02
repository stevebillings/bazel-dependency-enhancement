# Next Generation Dependency Discovery in Bazel - PRD

Status: Draft

Last updated: 2 May 2020

Authors: billings@synopsys.com

Reviewers: aiuto@google.com (lead)

Discussion Thread: https://groups.google.com/forum/#!topic/bazel-discuss/SdXuu9nDTHA 

Approvals

| Name         | Role                              | Date           |
| ------------ | ----------------------------------------------------- | -------------- |
|              |                                   |                |



Changelog

| Editor       | Comments                                              | Date           |
| ------------ | ----------------------------------------------------- | -------------- |
| billings     | Initial draft                                         | 17 Apr 2020    |
| billings     | Extended to macros                                    | 2 May 2020     |


## Objective

Provide a single mechanism that reveals the external package dependency details for a build target in any bazel project.

## Background

- Some organizations must provide evidence that their software product is free of serious security vulnerabilities before it is released. In order to do that, they (or much more likely, the tools they use) first need to generate a list of external package dependencies with sufficient details to unambiguously identify those external packages.
- Currently, there is no single way to do this that works across all repository rule types.
- This makes it difficult to develop tools for the current repository rule set, and impossible to develop tools that will continue to work as new repository rule types are developed.

## Goals

Provide a mechanism that reveals the external package dependency details for a given build target that will work regardless of the repository rule types used to specify those dependencies.

## Non-Goals

## Glossary of terms

## Functional requirements

### Work on any bazel project

**FR: Work on all existing projects.** The mechanism must work without modification for dependencies specified using all existing repository rule types and macros. No understanding of the project&#39;s bazel code (including the repository rule types and macros it uses) is required to use the mechanism.

**FR: Work on all future projects.** The mechanism must work without modification for dependencies specified using repository rule types and macros that will be developed in the future.

**FR: Work on a project as-is.** The mechanism must work on a project as-is, without any requirement to modify project files.

### Provide external package dependency details

**FR: External package dependency details.** The mechanism should provide list of external dependencies, along with origin details (a set of attributes and their values) for each dependency. The origin details must unambiguously identify the external packages. The set of attributes returned for each dependency will vary, depending on the repository rule type. For example (just two examples of the many different cases):

- For a Java project with a Maven dependency specified via a maven\_install rule or jvm\_maven\_import\_external macro, the data returned must (a) indicate that the dependency is a Maven dependency, and (b) provide the group:artifact:version (either as separate values, or combined in a string from which group, artifact, and version can be reliably parsed).
- For a Golang project with a dependency specified using a git\_repository rule, the data returned must (a) indicate that the dependency is a git repository dependency, and (b) provide the remote (URL), repository name, and commit ID.

**FR: Output.** An output file containing the list of dependencies similar to .lock files from other package managers would be ideal. This could be useful to users while making the dependencies easily discoverable by tools.

### Provide accurate data

**FR: Handle selects and build options.** The mechanism must accept an (optional) set of build attribute values (https://docs.bazel.build/versions/master/configurable-attributes.html) and produce a dependency list factoring in those build attribute values and the project&#39;s select() statements.

## Non-functional requirements

### Ease of use

**FR: Invoked from a bazel command.** It must be possible to invoke the mechanism from a bazel command. A bazel cquery command would be ideal.

