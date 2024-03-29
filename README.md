# Introduction

This repository/project contains the conceptual documentation for xPike.
API Documentation is generated in each of the individual xPike components and merged with this documentation
in the xpike.github.io build to produce the complete documentation site.

This project, and the docs for each component, are build using [docfx](https://dotnet.github.io/docfx/).

## Structure

Conteptual documentation for each component is found under:

```
docfx_project/api/{module}/articles
```

For example, conceptual documentation for the XPike.IoC.* set of components is found under:

```
docfx_project/api/ioc/articles
```

General developer guides are found under:

```
docfx_project/articles
```

## Building

The build for the documentation is against the xpike.github.io repository. It pulls together all the api docs and
conceptual docs to generate the complete site.

If a module and api documentation is added, that build needs to be modifed accordingly.
