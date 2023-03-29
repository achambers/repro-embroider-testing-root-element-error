# repro-embroider-testing-root-element-error

This repo reproduces an error I'm experiencing with running tests from localhost:4200/tests under embroider when `storeConfigInMeta: true`.

## How to reproduce

1. Clone this repo

```bash
git clone git@github.com:achambers/repro-embroider-testing-root-element-error.git
```

2. Install dependencies

```bash
cd repro-embroider-testing-root-element-error && yarn install
```

3. Run the dev server

```bash
ember serve
```

4. Visit http://localhost:4200/tests

5. Observe an error in the console

<img width="534" alt="image" src="https://user-images.githubusercontent.com/416724/228571776-6f2424f6-9e38-4f19-a73e-d88d03c619e3.png">


## Extra context

Note that, in this example we have defined the `rootElement` for non-test builds, in this case `.cheese`
https://github.com/achambers/repro-embroider-testing-root-element-error/blob/92558c8c6bfdbd7cc5b3603e2ac8a8b3b7499707/config/environment.js#L17

We have also set `storeConfigInMeta: false`
https://github.com/achambers/repro-embroider-testing-root-element-error/blob/92558c8c6bfdbd7cc5b3603e2ac8a8b3b7499707/ember-cli-build.js#L7

If you set a debugger at the line that is throwing the error - `at Class.setup (event_dispatcher.js:145:1)` - you'll notice that the `rootElementSelector` property just above it is set to `.cheese`. This element doesn't exist in `test` as the root element is actually `#ember-testing`.

If you do a search through the JS for `"rootElement":` you will see it defined twice, both times with values of `.cheese`.
<img width="455" alt="image" src="https://user-images.githubusercontent.com/416724/228573076-884fcdda-3ad2-47a9-994a-977e3520ee95.png">

If you then set `storeConfigInMeta: true` and restart the server you will notice the following things:

1. The tests pass.
2. The value of `rootElementSelector` in `event_dispatcher.js` is `#ember-testing` which is what we want.
3. Searching the `"rootElement":` only gives one result
<img width="525" alt="image" src="https://user-images.githubusercontent.com/416724/228573887-b24481d8-ba8d-4bd4-98e3-a13748417c1b.png">
5. The config that is in the meta tag contains `rootElement: '#ember-testing'`
<img width="907" alt="image" src="https://user-images.githubusercontent.com/416724/228574108-c2974f02-892d-422c-b57c-9665857a17e1.png">
