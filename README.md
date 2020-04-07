### :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction:  :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction:  :construction: :construction:

<h3 align=center> WIP </h1>

### :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction:  :construction: :construction: :construction: :construction: :construction: :construction: :construction: :construction:  :construction: :construction:

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


# An ugly way to NCC an Next.js@9 that works 😅

<br>
<br>

> .
>  
> Because sometimes you want to compile with `@zeit/ncc` your  sweet [Next.js SSR server](https://github.com/zeit/next.js/tree/v9.0.5#custom-server-and-routing). In my case, I needed to do it so I can isolate the dependencies of the frontend server in an local docker image… [But Why ? ] Yeah… I know 😏
> 
> .

<br>
<br>
<br>
<br>


## The Intro.

<br>
<br>

**What can go wrong if you try to `ncc build` your Next.js custom server.js file ?**

<br>

Let's do it together with [the latest 9.3.4 Next.js release](https://github.com/zeit/next.js/tree/v9.0.5) (the 06/04/2020). We gonna use [the in house cutom-server example](https://github.com/zeit/next.js/tree/v9.0.5/examples/custom-server) for that.

```sh
$ yarn create next-app --example custom-server custom-server-app
yarn create v1.22.4
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Installed "create-next-app@9.3.4" with binaries:
      - create-next-app
Creating a new Next.js app in /tmp/custom-server-app.
Downloading files for example custom-server. This might take a moment.
Installing packages. This might take a couple of minutes.

[...]

Success! Created custom-server-app at /tmp/custom-server-app

[...]
```

We can verify that the example works by checking that `http://localhost:3000/a` contains the `<div>a</div>`.


```sh
$ yarn build
# [...]
$ npx start-server-and-test :3000 'curl -s http://localhost:3000/a | grep -q "<div>a</div>"'
starting server using command "npm run start"
and when url "[ 'http://localhost:3000' ]" is responding
running tests using command "curl -s http://localhost:3000/a | grep -q "<div>a</div>""
> custom-server@1.0.0 start /tmp/custom-server-app
> NODE_ENV=production node server.js
> Ready on http://localhost:3000
```

Great !

Now, we want to *Docker-ize* the build artifact of the sources. We create a single build file thanks to `ncc`.
Let's install the latest `@zeit/ncc` (version 0.22.1 today 06/04/2020)

```sh
$ yarn add -D @zeit/ncc
```

use it in our `package.json`

```js
{
  "build": "next build && ncc build server.js",
  "start": "NODE_ENV=production node ./dist/index.js"
}
```

build it 

```sh
$ yarn build           
yarn run v1.22.4
warning package.json: No license field
$ next build && ncc build server.js
Creating an optimized production build  

Compiled successfully.

Automatically optimizing pages  

Page                                                           Size     First Load JS
┌ ○ /                                                          1.75 kB        59.7 kB
├ ○ /404                                                       3.15 kB        61.1 kB
├ ○ /a                                                         259 B          58.2 kB
└ ○ /b                                                         262 B          58.2 kB
+ First Load JS shared by all                                  57.9 kB
  ├ static/pages/_app.js                                       957 B
  ├ chunks/bff7181c78c43daa5365775dd57fa1bbe47ac783.92878c.js  10.3 kB
  ├ chunks/framework.0f140d.js                                 40 kB
  ├ runtime/main.69db5b.js                                     5.95 kB
  └ runtime/webpack.b65cab.js                                  746 B

λ  (Server)  server-side renders at runtime (uses getInitialProps or getServerSideProps)
○  (Static)  automatically rendered as static HTML (uses no initial props)
●  (SSG)     automatically generated as static HTML + JSON (uses getStaticProps)

ncc: Version 0.22.1
ncc: Compiling file index.js
    0kB  dist/locales/es.json
    0kB  dist/locales/en.json
    0kB  dist/cjs3.js
    0kB  dist/index14.js
    0kB  dist/writable.js
    0kB  dist/transform.js
    0kB  dist/passthrough.js
    0kB  dist/duplex.js
    0kB  dist/index12.js
    0kB  dist/module.js
    0kB  dist/system.js
    0kB  dist/global.js
    0kB  dist/empty.js
    0kB  dist/amd-options.js
    0kB  dist/amd-define.js
    0kB  dist/index4.js
    0kB  dist/cjs1.js
    1kB  dist/index19.js
    1kB  dist/worker.js
    1kB  dist/readable.js
    1kB  dist/browser.js
    1kB  dist/index10.js
    1kB  dist/index8.js
    1kB  dist/harmony-module.js
    1kB  dist/ts.js
    1kB  dist/minify.js
    1kB  dist/static-paths-worker.js
    2kB  dist/locales/es.js
    2kB  dist/locales/en.js
    2kB  dist/main.js
    2kB  dist/index9.js
    2kB  dist/index6.js
    2kB  dist/_error.js
    3kB  dist/index7.js
    3kB  dist/cjs2.js
    3kB  dist/cluster.js
    4kB  dist/index18.js
    4kB  dist/index15.js
    4kB  dist/index13.js
    4kB  dist/processChild.js
    4kB  dist/router.js
    4kB  dist/index1.js
    5kB  dist/browser1.js
    5kB  dist/constants.json
    5kB  dist/service.js
    7kB  dist/loader.js
    7kB  dist/index3.js
    9kB  dist/string_decoder.js
    9kB  dist/index11.js
   13kB  dist/events.js
   14kB  dist/punycode.js
   16kB  dist/index16.js
   16kB  dist/assert.js
   19kB  dist/util.js
   23kB  dist/url.js
   47kB  dist/index5.js
   63kB  dist/cjs.js
  123kB  dist/index17.js
  971kB  dist/index2.js
11247kB  dist/index.js
12654kB  [30541ms] - ncc 0.22.1
Done in 36.05s.
```

And test it.

```sh
$ npx start-server-and-test :3000 'curl -s http://localhost:3000/a | grep -q "<div>a</div>"'
starting server using command "npm run start"
and when url "[ 'http://localhost:3000' ]" is responding
running tests using command "curl -s http://localhost:3000/a | grep -q "<div>a</div>""
> custom-server@1.0.0 start /tmp/custom-server-app
> NODE_ENV=production node ./dist/index.js
> Ready on http://localhost:3000
```

Great ! Still looking good 😏:ok_hand:

<br>
<br>

Now is where it hurts…  
As this build is now a standalone artefact, normally we can *simply* copy it in a docker image and run it.  
We create a `Dockerfile` with :

```Dockerfile
FROM node:12-alpine
COPY ./dist /app
WORKDIR /app
ENV NODE_ENV=production
ENV PORT=80
ENTRYPOINT [ "node", "index.js" ]
```

And a `.dockerignore` with :

```
**/node_modules
**/.next
```

We run

```sh
$ docker build -t custom-server-app .
Sending build context to Docker daemon   14.8MB
Step 1/6 : FROM node:10-alpine
 ---> 34a10d47f150
Step 2/6 : COPY ./dist /app
 ---> aa597eaa80ef
Step 3/6 : WORKDIR /app
 ---> Running in 417407653f97
Removing intermediate container 417407653f97
 ---> 6072cfcfff5d
Step 4/6 : ENV NODE_ENV=production
 ---> Running in 3b76bf475601
Removing intermediate container 3b76bf475601
 ---> 5d8dbd2d958d
Step 5/6 : ENV PORT=80
 ---> Running in c055863fa2e7
Removing intermediate container c055863fa2e7
 ---> 766b6e1c1d98
Step 6/6 : ENTRYPOINT [ "node", "index.js" ]
 ---> Running in 6c65c3ae9581
Removing intermediate container 6c65c3ae9581
 ---> c2d35341398c
Successfully built c2d35341398c
Successfully tagged custom-server-app:latest
```

And successfully end this long article introduction with an problem :

```sh
$ docker run --name supafrontend -p 3000:80 --rm custom-server-app:latest
/app/index.js:*
                throw new Error(`Could not find a valid build in the '${this.distDir}' directory! Try building your app with 'next build' before starting the server.`);
                ^

Error: Could not find a valid build in the '/app/.next' directory! Try building your app with 'next build' before starting the server.
    at Server.readBuildId (/app/index.js:*:*)
    [...]
```

The build is failing due to a trivial missing `.next` directory created by running next build. `ncc` did not bundle the `.next`. Apparently the directory is dynamically loaded. We can resolve this one by copying the `.next` directory in the dist directory locally or in the docker image. 

```Dockerfile
FROM node:10-alpine
COPY ./dist /app
COPY ./.next /app/.next
WORKDIR /app
ENV NODE_ENV=production
ENV PORT=80
ENTRYPOINT [ "node", "index.js" ]
```

Just ignore `.next/cache` in the `.dockerignore` :

```diff
/node_modules
**/.next/cache
```

In both cases it's the beginning of an missing modules horror story when you try to access to the page.

```sh
$ docker run --name supafrontend -p 3000:80 --rm custom-server-app:latest 
> Ready on http://localhost:80
{ Error: Cannot find module 'prop-types'
# [...]
code: 'MODULE_NOT_FOUND' }
```

=== Questions ?


<br>
<br>
<br>
<br>


## The die and retry process.

<br>
<br>

### Copy all the `node_modules` 💩

<br>

Clearly, the quickest solution is to copy all the `node_modules` in the image… It's the worst no optimal solution especially because it's copying all installed dependencies (dev, prod and optional) in an docker image manly made for production. In my monorepo case, that represent all the dependencies of all the other packages in the monorepo. We are talking big amount of useless modules for this image… More other, what's the point of making a standalone bundle if you already have all the `node_modules` in the image ?  
This ugly way of *"fixing"* the problem is out of scope. Still, it's giving use a previous of a working case… I don't think the output size is relatable as there are for sure duplicate modules between the copied `node_modules` and the `ncc` bundle but still... if we are going to add some `node_modules` in the image to make it work we should look at some worst case scenario size.  

You can see below that the `ncc` bundle plus all the `node_modules` is adding an extra 58.5MB to the image.

```
$ docker images --format "table {{.Size}}\t{{.Repository}}\t{{.Tag}}" -f reference=custom-server-app
SIZE                REPOSITORY          TAG
156MB               custom-server-app   with_all_node_modules
97.5MB              custom-server-app   no_working_base 
```

<br>
<br>

### Relatable test case

<br>

To frame the behavior we will make a test case like before by requesting a page when the image and expecting it to work if the page answers with `<div>a</div>`.

Here is the `docker_test_case.sh` I use :

```bash
#!/bin/bash

DOCKER_CMD="$(cat <<-EOF | tr '\n' ' '
docker run
--name supafrontend
-p 3000:80
--rm
custom-server-app:latest
EOF
)"

npx start-server-and-test \
  "timeout --signal=SIGKILL 5s ${DOCKER_CMD} ;  docker stop supafrontend" \
  :3000 \
  'curl -s http://localhost:3000/a | grep -q "<div>a</div>"'
```

With this we can test our changes.

<br>
<br>

### Be ready for more that two build steps

<br>

I will be using [nps](https://www.npmjs.com/package/nps) and [nps-utils](https://www.npmjs.com/package/nps-utils) as the npm scripts in the `package.json` aren't suitable anymore to contain all the scripts we will be poping .

```sh
# In terminal
$ yarn add -D nps nps-utils
```
```js
// In ./package.json
{
  "scripts": {
    "dev": "nps dev",
    "build": "nps build",
    "start": "nps"
  }
}
```
```yaml
# In ./package-scripts.yaml
scripts:
  default: NODE_ENV=production node ./dist/index.js

  dev: node server.js

  build: next build && ncc build server.js
```

<br>
<br>

### Manually copy the required node_modules

<br>

Going step by step, I figure out that each modules required different treatment to be Next.js custom server friendly. The first treatment is a good old copy paste of the whole modules case. 
In the error : 

```sh
$ yarn build && \
  docker build -t custom-server-app . && \
  sh docker_test_case.sh
# [...]
{ Error: Cannot find module 'prop-types'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.rf6O (/app/.next/server/static/CiwLw_mSR85QT83chX4pd/pages/_document.js:968:18)
    at __webpack_require__ (/app/.next/server/static/CiwLw_mSR85QT83chX4pd/pages/_document.js:23:31)
    at Object.VDXt (/app/.next/server/static/CiwLw_mSR85QT83chX4pd/pages/_document.js:157:41)
    at __webpack_require__ (/app/.next/server/static/CiwLw_mSR85QT83chX4pd/pages/_document.js:23:31)
    at Object.5 (/app/.next/server/static/CiwLw_mSR85QT83chX4pd/pages/_document.js:106:18)
    at __webpack_require__ (/app/.next/server/static/CiwLw_mSR85QT83chX4pd/pages/_document.js:23:31) code: 'MODULE_NOT_FOUND' }
```


We can see that the `_document.js` in the `.next` folder is the one making a direct request to a `prop-types` file inside the `node_modules`. To solve this requirement we need to make that file available from the dist folder. We are going to add a `node_modules` folder inside the `dist` folder so node with the whole `prop-types` module.

```yaml
# In ./package-scripts.yaml
scripts:
  default: NODE_ENV=production node ./dist/index.js

  dev: node server.js

  build:
    default: rimraf dist && nps build.next build.server build.modules
    next: next build
    server: ncc build server.js

    #

    modules:
      default: >-
        mkdirp dist/node_modules
        && nps build.modules.prop-types
      prop-types: ncp node_modules/prop-types dist/node_modules/prop-types
```

Let rebuild all and run our test :)

```sh
$ yarn build && \
  docker build -t custom-server-app . && \
  sh docker_test_case.sh
# [...]
{ Error: Cannot find module 'react'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.cDcd (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:823:18)
    at __webpack_require__ (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:23:31)
    at Object.VDXt (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:159:38)
    at __webpack_require__ (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:23:31)
    at Object.5 (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:106:18)
    at __webpack_require__ (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:23:31) code: 'MODULE_NOT_FOUND' }

```

<br>
<br>

### Bundle complex node_modules

<br>

We now have a missing `prop-types` module. We could just copy the module but it won't work as `prop-types` as mandatory dependencies. Instead we are going to go *meta* in this second treatment by bundling a file inside our `node_modules` and expose the result as a `dist/node_modules`. 



```sh
$ yarn build && \
  docker build -t custom-server-app . && \
  sh docker_test_case.sh
# [...]
{ Error: Cannot find module 'react'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.cDcd (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:823:18)
    at __webpack_require__ (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:23:31)
    at Object.VDXt (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:159:38)
    at __webpack_require__ (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:23:31)
    at Object.5 (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:106:18)
    at __webpack_require__ (/app/.next/server/static/arftJmX66uLvaLayvd9wU/pages/_document.js:23:31) code: 'MODULE_NOT_FOUND' }

```


```yaml
# [...]

    modules:
      default: >-
        mkdirp dist/node_modules
        && nps build.modules.prop-types
        && nps build.modules.react
      prop-types: ncp node_modules/prop-types dist/node_modules/prop-types
      react: ncp node_modules/react dist/node_modules/react
```



```sh
$ yarn build && \
  docker build -t custom-server-app . && \
  sh docker_test_case.sh
# [...]
{ Error: Cannot find module 'object-assign'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.<anonymous> (/app/node_modules/react/cjs/react.production.min.js:10:20)
    at Module._compile (internal/modules/cjs/loader.js:778:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:789:10)
    at Module.load (internal/modules/cjs/loader.js:653:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:593:12)
    at Function.Module._load (internal/modules/cjs/loader.js:585:3) code: 'MODULE_NOT_FOUND' }

```

`object-assign` is a dependency of `prop-types`


```yaml
# [...]

    modules:
      default: >-
        mkdirp dist/node_modules
        && nps build.modules.prop-types
        && nps build.modules.react
      prop-types: >-
        ncc build node_modules/prop-types/index.js
        -o dist/node_modules/prop-types
      react: >-
        ncc build node_modules/react/index.js
        -o dist/node_modules/react
```

```sh
$ yarn build && \
  docker build -t custom-server-app . && \
  sh docker_test_case.sh
# [...]
{ Error: Cannot find module 'styled-jsx/server'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.2+G7 (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:99:18)
    at __webpack_require__ (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:23:31)
    at Object.VDXt (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:161:38)
    at __webpack_require__ (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:23:31)
    at Object.5 (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:106:18)
    at __webpack_require__ (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:23:31) code: 'MODULE_NOT_FOUND' }
```

```yaml
# [...]

    modules:
      default: >-
        mkdirp dist/node_modules
        && concurrently --kill-others-on-fail
        "nps build.modules.prop-types"
        "nps build.modules.react"
        "nps build.modules.styled-jsx"
      prop-types: >-
        ncc build node_modules/prop-types/index.js
        -o dist/node_modules/prop-types
      react: >-
        ncc build node_modules/react/index.js
        -o dist/node_modules/react
      styled-jsx: >-
        ncc build node_modules/styled-jsx/server.js 
        -o dist/node_modules/styled-jsx/server
```

```sh
$ yarn build && \
  docker build -t custom-server-app . && \
  sh docker_test_case.sh
# [...]
{ Error: Cannot find module 'styled-jsx/server'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.2+G7 (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:99:18)
    at __webpack_require__ (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:23:31)
    at Object.VDXt (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:161:38)
    at __webpack_require__ (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:23:31)
    at Object.5 (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:106:18)
    at __webpack_require__ (/app/.next/server/static/qkQwP-RXLeHa9M6F7WaGF/pages/_document.js:23:31) code: 'MODULE_NOT_FOUND' }
```



```
$ docker images --format "table {{.Size}}\t{{.Repository}}\t{{.Tag}}" -f reference=custom-server-app
SIZE                REPOSITORY          TAG
156MB               custom-server-app   with_all_node_modules
98.8MB              custom-server-app   working_base 
97.5MB              custom-server-app   no_working_base 
```


<br>
<br>
<br>
<br>

## An ugly way to make it work.

We finally have it ! 
We tamed the beast !
The fight was ugly and the result too but we a fully working concept :)
