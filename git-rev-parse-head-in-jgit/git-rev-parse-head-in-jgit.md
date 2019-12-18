# git rev-parse HEAD alternative command in JGit

JGit does not provide extractly the same interface for what you run in comand line `git rev-parse HEAD`. Below snippets are what you need:

```
// git rev-parse HEAD
git.getRepository().exactRef("refs/heads/master").getObjectId().name()

// git rev-parse --short HEAD
git.getRepository().exactRef("refs/heads/master").getObjectId().abbreviate(7).name()
```