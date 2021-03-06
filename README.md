# yarn-workspaces-cheat-sheet
The best yarn workspaces commands and practices in a single file

## Reminder
- A workspace is just an `npm` package, aka a folder with a `package.json`. 
- With Yarn workspaces any of these local workspaces/packages can be used as regular npm package without having to publish it


## Helper commands
| **Command**                                                                                   | **Goal**                                              | **Api**                                       | **Examples**                                   
|-----------------------------------------------------------------------------------------------|-------------------------------------------------------|-----------------------------------------------|------------------------------------------------
| [yarn workspace](https://yarnpkg.com/en/docs/cli/workspace)                                   | Run a Yarn command in a specific workspace            | yarn workspace <workspace_name> <command>     | - yarn workspace package-a add react react-dom<br> - yarn workspace package-a build
| [yarn workspaces run](https://yarnpkg.com/en/docs/cli/workspaces#toc-yarn-workspaces-run)     | Run a Yarn command in all existing project workspaces | yarn workspaces run <command>                 | - yarn workspaces run add react react-dom<br> - yarn workspaces run build
| [yarn workspaces info](https://yarnpkg.com/en/docs/cli/workspaces#toc-yarn-workspaces-info)   | See which workspace uses which workspace              | yarn workspaces run <command>                 | - yarn workspaces run add react react-dom<br> - yarn workspaces run build


## Enable yarn workspaces
```diff
// Root package.json
{
   "name": "my-repo",
+   "private": true,                // Do not allow publishing of workspace on NPM
+   "workspaces": [
+      "someofolder/somesubfolder", // This specific folder is a workspace
+      "somefolder/*"               // Any folder under somefolder is a workspace
+   ],
   "scripts": {}
}
```

## Create a workspace/package
```bash
$ cd somefolder && mkdir my-package # "somefolder/my-package" matches "somefolder/*" in root package.json "workspaces"
$ cd my-package && npm init -y      # Generate package definition
```
```diff
// somefolder/my-package.json
{
+   "name": "my-repo",              // Name is how you reference a package in the workspaces, just as any regular npm package
+   "main": "dist/index.js"         // The entry file for your package, can be whatever file you want
}
```

## Import a package into another
### Manually
```diff
// somefolder/a/package.json
{
   dependencies: [
+      "package-b": "*"                  // "*" Matches any available version of package-b
   ]
}
```

### Via command line
```
$ yarn workspace package-a add package-b@* // "package-a" and "package-b" matche the package's package.json "name" key
```

## Checking dependencies between your workspaces
```bash
$ yarn workspaces info # Displays the workspaces dependeny tree of your project

// Logs
yarn workspaces vx.x.x
{ 
  "package-a": {
    "location": "packages/package-a",
    "workspaceDependencies": [
      "package-b" // 👈 package-a uses package-b workspace/package
    ],
    "mismatchedWorkspaceDependencies": []
  },
  "package-b": {
    "location": "packages/package-b",
    "workspaceDependencies": [], // 👈 package-b uses no workspace/package
    "mismatchedWorkspaceDependencies": []
  }, ... }
```

## Nohoist: Fixing dependency errors
Chances are one day you'll end up with an error of this kind: `Module not found` or `A different version of {package_name} was detected higher up in the tree`.

That means the sharing of {package_name} isn't working out.

Use [`nohoist`](https://yarnpkg.com/blog/2018/02/15/nohoist/) to prevent sharing of specific packages between workspaces.

```diff
# root package.json
{
   ...
   "workspaces": {
      "packages": [
         ...
      ],
+   "nohoist": [
+      "**/jest", // For any package, don't hoist jest
+      "**/jest*", // For any package, don't hoist any package starting with "jest" Ex: jest-then, jest-when, ...
+      "**/jest/**", // For any package, don't hoist any jest sub package Ex: jest/core
+      "my-package-a/jest", // For package "my-package-a", don't hoist jest
+   ]
   },
   ...
}
```

[More about glob patterns](http://www.globtester.com/)

## Useful links
- [Introducing yarn workspaces](https://yarnpkg.com/blog/2017/08/02/introducing-workspaces/)
- [Yarn workspaces documentation](https://yarnpkg.com/lang/en/docs/workspaces/)
- [Nohoist docs](https://yarnpkg.com/blog/2018/02/15/nohoist/)
- [Glob patterns tester](http://www.globtester.com/)

## Projects using yarn workspace
@todo
- https://github.com/isthatcentered/unicorn/ (disclaimer, this is mine)


