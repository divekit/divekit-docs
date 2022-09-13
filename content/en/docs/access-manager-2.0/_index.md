---
title: "Access Manager 2.0"
linkTitle: "Access Manager 2.0"
weight: 2
description: >
  Rewritten (in python) and improved version of the original access manager. Used to change the access rights of repository members in a GitLab group based on a whitelist.
---

## Setup & Run
1. Install Python 3 or higher
2. Install python-GitLab using `pip install python-gitlab`
3. Check the file `config.py` to configure the tool
4. run AccesManager.py using `python AccessManager`

## Configuration
| Option | Purpose |
|--------|---------|
| GIT_URL | URL of your GitLab Server |
| AUTH_TOKEN | Your personal GitLab access token |
| GROUP_ID | Id of the GitLab Group you want to modify |
| ACCESS_LEVEL | Access level you want to provide. 1 for Maintainer, 0 for Guest |
| STUDENTS | List of users to modify. Users not in this List will be ignored. |
