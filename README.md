# A case of versioning software with CI/CD in Git

## Problem Statement
There is a commit **`A`** (`- initial commit` in this example) in a branch that we release from (`master` in this example).
In the state commit **`A`** there is cirtait state of code and the version is `1.0.0-SNAPSHOT`.
We assume there was release candidate built and deployed, let's say to `DEV` by CI/CD triggered by the commit **`A`**.

After cirtain period of time (needed to check the RC and to decide to ship it), the `shipit` manual CI/CD step is triggered. 
This process needs to:
1) set version to the release version (`1.0.0` in this example) - commit **`S1`** (`- setting release version` in this example)
2) tag the source base with a release tag (`v1.0.0` in this example)
3) bump and set version to snapshot (`1.1.0-SNAPSHOT` in this example) - commit **`S2`** (`- setting next snapshot version` in this example)
4) incorporate these changes into `master`

Aditional complication that we want to care about, is that between commit **`A`** and `shipit` step there was another 
commit that happenned on `master` branch, commit **`B`** that modified the code (`- updated code` in this example).

We have to make sure that the state of the `master` *after* `shipit` step finishes, satisfies the following:
- the tag `v1.0.0` marks the state of the code as of **`A`** and version `1.0.0`
- `master` at commit **`S1`** has the code as of **`A`** (**not** updated code from **`B`**) and version `1.0.0`
- `master` at commit **`S2`** has the code as of **`A`** (**not** updated code from **`B`**) and version `1.1.0-SNAPSHOT`
- `master` at commit **`B`** includes the updated code and next snapshot version `1.1.0-SNAPSHOT`

## Approaches
- `rebase` master onto `shipit`
- - see the result in the `mater-rebased`
- - looks exactly what we need
- - drawbacks - rebasing `master` is wrong
- `merge` `shipit` into `master`
- - see the result in the `mater-merged`
- - looks exactly what we need
- - drawbacks - extra merge commit, no other nopticed
