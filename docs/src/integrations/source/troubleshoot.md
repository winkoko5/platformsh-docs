---
title: Resolve access issues with source integrations
sidebarTitle: Resolve access
description: Learn how to troubleshoot access rights for integrated repositories.
toc: false
---

If you [add a user](/administration/users.md#add-a-user-to-a-project) to a Platform.sh project,
but you haven’t added them to the remote repository on GitHub, GitLab, or Bitbucket,
they can't clone the project locally.

That user might try to clone the repository using the CLI with the following command:

```bash
platform get {{< variable "PROJECT_ID" >}}
```

In this case, the user gets an error similar to the following:

```txt
Failed to connect to the Git repository: git@github.com:organization/repository.git
Please make sure you have the correct access rights and the repository exists.
```

## Solution: Check external repository access rights

To enable the user to clone the repository,
grant them the correct access rights for the integrated repository.
