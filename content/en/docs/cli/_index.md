---
title: "cli"
linkTitle: "cli"
weight: 2
description: >
  CLI to simplify and orchestrate the usage of multiple divekit tools.
---

{{% pageinfo %}}
The documentation is not yet written. Feel free to add it yourself ;)
{{% /pageinfo %}}

## Getting started

### Prerequisites
- `npm`
- `python`
- `pip`

### usage
```bash
npm i @divekit/cli -g # install

mkdir st-2042 # (optional) create empty project directory
cd st-2042

divekit init # initializes basic configuration
```

### file structure
```
st-2042
├── .divekit
|   ├── config
|   |    ├── automated-repo-setup
|   |    └── ...
|   └── tools
|        ├── access-manager
|        └── ...
└── origin-repo
```



#### Possible upcoming features
```bash
divekit config # top-level config edit 
divekit setup # create student repos based on the configuration
```

