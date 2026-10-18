# agent-skills

## Install this repository

Requires Node.js and npm (`npx`).

Install **all skills** from [thealtoclef/agent-skills](https://github.com/thealtoclef/agent-skills)
globally in the canonical user skills directory only:

```sh
npx skills add thealtoclef/agent-skills \
  --global \
  --agent universal \
  --yes \
  --skill '*'
```

Skills are installed user-wide in `~/.agents/skills` without links to other
agent directories.

## External skills

Source: [mattpocock/skills](https://github.com/mattpocock/skills).
These skills are maintained upstream, not copied into this repository.

### Skills we use

- `codebase-design`
- `domain-modeling`
- `grill-me`
- `grill-with-docs`
- `grilling`
- `improve-codebase-architecture`
- `pr`
- `research`
- `tdd`
- `wait-what`
- `writing-for-agents`

### Install

Install only the selected skills globally in the canonical user skills
directory:

```sh
npx skills add mattpocock/skills \
  --global \
  --agent universal \
  --yes \
  --skill \
    codebase-design \
    domain-modeling \
    grill-me \
    grill-with-docs \
    grilling \
    improve-codebase-architecture \
    pr \
    research \
    tdd \
    wait-what \
    writing-for-agents
```
