# NUSGreyhats.github.io

This is where our website lives.

We use Hugo so that we don't need to mess with any HTML when we add more content.

### Team

To add your profile to the team page, head over to [team.yml](/data/team.yml) and add your entry as follows:

```yml
- name: <your name>
  major: <your enrolled major>
  roles:
    - <role 1>
    - <role 2>
  desc: <description of yourself>
  twitter: <twitter profile url> # delete this line if you don't have
  linkedin: <linkedin profile url> # delete this line if you don't have
  github: <github profile url> # delete this line if you don't have
  personal: <personal homepage url> # delete this line if you don't have
```

Once you're done, make a PR and wait for it to go live!

### SecWeds

Information of each SecWed session is stored in a markdown file under **content/posts/secwed/<semester>**. They look as follows:

```md
---
author: "NUS Greyhats"
title: "SecWed #<secwed id, e.g. 150921 if it was on Sep 15, 2021>"
talks:
    - title: <talk 1 title>
      speaker: <talk 1 speaker>
    - title: <talk 2 title>
      speaker: <talk 2 speaker>
date: <session date> # e.g. "2021-09-15"

# don't change these
tags: ["secweds"]
ShowBreadcrumbs: False
HideSummary: True
hiddenInHomeList: True
---

### Talk 1: 1900Hrs - 1945Hrs
**Talk Title: <talk 1 title>**

<talk description>

#### Pre-talk Preparations

<any pre-talk prep instructions>

#### Speaker

<speaker bio>

---

### Talk 2: 1945Hrs - 2030Hrs
**Talk Title: <talk 2 title>**

<talk description>

#### Speaker

<speaker bio>

```

### Resources

Links to any form of resources like workshops or writeups can be placed in [resources.yml](/data/resources.yml). Each set of resources should follow the sample structure as follows:

```yml
- name: Orbital Mission Control 🚀
  desc: We hold web security workshops for [Orbital](https://orbital.comp.nus.edu.sg/) students in NUS every year.
  workshops:
    - name: Orbital Mission Control 2021
    orgs: Wen Junhua
    link: https://www.youtube.com/watch?v=yUs9zKqGJDU&list=PLceyrQSWkM_eFkbY9DvmkMN9RJnarOp55&index=1

    - name: Orbital Mission Control 2019
    orgs: Ngan Ji Cheng, Chang Hui Zhen & Lim Yan Ting
    link: https://www.youtube.com/watch?v=VdTgwSIJZaA&list=PLceyrQSWkM_eFkbY9DvmkMN9RJnarOp55&index=2
```
