# What is this repo?

This repo is a Minimum Reproducible Example illustrating the issue with current (Jan 26 '22) `nodejs-lockfile-parser` when being used with NPM 7 workspaces.

# How to reproduce the problem?

Ensure your system is configured to use Node 16.x and NPM 8 (current active LTSes)

```
git clone https://github.com/louis-bompart/literate-bassoon
cd literate-bassoon
npm i
npx snyk-nodejs-lockfile-parser
```

This will output:
`{"dependencies":{},"hasDevDependencies":false,"name":"workspace","size":1,"version":"0.0.0","meta":{"lockfileVersion":2,"packageManager":"npm"}}`

Which, in short, mean that there's no dependency detected in this project. This is technically true, the NPM project `workspace` itself doesn't have any dependencies, as you can see in the [root package.json](./package.json).

Snyk does recommend to use the `--all-projects` with monorepos[^1], however this is not possible with workspaces, because there's no `package-lock.json` or `node_modules` in the sub-packages: everything is hoisted up at the root level.

This combined means that it's not currently possible to scan NPM workspace project.

# Workarounds

I did not find any bulletproof workaround. However, if devDep scanning is not required, you can get a 'full scan' if you had all the subpackages as dependency of the root package.
However, the dev dependencies of the subpackages won't be scanned, because the subpackages are then considered 'external dependency' and only their 'dependencies' and 'peers' are considered.

[^1:] https://support.snyk.io/hc/en-us/articles/360000910577-Does-the-Snyk-CLI-support-monorepos-or-multiple-manifest-files-