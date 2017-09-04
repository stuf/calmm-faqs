# How do I `x`?

## Contents

 * [Translation and Localisation](#translation)
   * [Generic translation](#generic-translation)
   * [Translation in other tags](#translation-in-other-tags)
   * [Translation in custom component](#translation-in-custom-component)
 * [Components](#components)
   * [Action on mount](#action-on-mount)
   * [Action on unmount](#action-on-unmount)
   * [Action on both mount/unmount](#action-on-both-mount-unmount)
 * [Observables](#observables)

---

## Translation and Localisation

For all UI translation needs, use the `<T />` component found in `src/common/translation.js`.

<details>
  <a name="generic-translation"></a>
  <summary>Generic translation</summary>

  _I just want a generic translation without anything special_

  ```jsx
  // src/components/my-component.js
  import T from '../common/translation';

  const MyComponent = () =>
    <div>
      <T>myTranslation.stringId</T>
    </div>;
  ```

  ```js
  // src/locales/fi.json
  {
    // ...
    "myTranslation.stringId": "My translation string"
    // ...
  }
  ```
</details>

<details>
  <a name="translation-in-other-tags"></a>
  <summary>Translation in some other HTML tag than <code>&lt;span /&gt;</code></summary>

  ```jsx
  // src/components/my-page-component.js
  import T from '../common/translation';

  const MyComponent = () =>
    <div>
      <T component="h1">myHeading</T>
    </div>;
  ```

  ```js
  // src/locales/fi.json
  {
    // ...
    "myHeading": "My translated heading"
    // ...
  }
  ```
</details>

<details>
  <a name="translation-in-custom-component"></a>
  <summary>Translation in custom component like <code>&lt;CustomLabel /&gt;</code></summary>

  ```jsx
  // src/scenes/home/index.js
  import * as U from 'karet.util';
  import T from '../../common/translation';
  import { CustomLabel } from '../../components';

  const HomeScene = ({ match, history, state }) =>
    <div>
      {/* Give the component you want localized to the `component` attribute of `T` */}
      <T component={CustomLabel}>{U.view('currentPage', state)}</T>
    </div>;
  ```

  ```js
  // src/locales/fi.json
  {
    // ...
    "homePage": "Translated home page",
    "formPage": "Translated form page",
    // ...
  }
  ```
</details>

---

## Components

### Action on mount

> **Example case:** Perform a REST request when the component has been mounted into the DOM

<details>
  <summary>
    With <code>U.refTo</code>
  </summary>

  ```jsx
  const Component = ({ state, dom = U.variable(), rest = U.variable() }) =>
    <div ref={U.refTo(dom)}>
      {U.seq(dom,
            U.flatMapLatest(restRequest('/abc')),
            U.set(rest))}
      <div>
        {rest.map(JSON.stringify)}
      </div>
    </div>;
  ```
</details>

<details>
  <summary>
    Without <code>U.refTo</code>
  </summary>

  ```jsx
  const Component = ({ state, dom = U.variable(), rest = U.variable() }) =>
    <div ref={node => node && dom.set(node)}>
      {U.seq(dom,
            U.flatMapLatest(restRequest('/abc')),
            U.set(rest))}
      <div>
        {rest.map(JSON.stringify)}
      </div>
    </div>;
  ```
</details>

---

### Action on unmount

>**Example case:** Clean out some part of the state when component is unmounted.

> NOTE: This action will happen on unmount, regardless of what action is taken, e.g. back button, navigation event, etc. _It will happen when the component is removed from the DOM._

> NOTE: We can not use `U.refTo` for using on unmount, as `U.refTo` _only_ writes the ref to an atom when it exists. Information in the `karet.util` docs [here](https://github.com/calmm-js/karet.util#U-refTo).

<details>
  <a name="action-on-unmount-without-u-refto"></a>
  <summary>
    Without <code>U.refTo</code>
  </summary>

  ```jsx
  const Component = ({ state, dom = U.variable() }) =>
    <div ref={node => !node && state.set(undefined)}>
      <div>
        <p>Lorem ipsum</p>
        <a href="/">Back to home page</a>
      </div>
    </div>;
  ```
</details>

---

### Action on both mount/unmount

<details>
  <a name="action-on-both-mount-unmount"></a>
  <summary>Action on both mount/unmount</summary>

  ```jsx
  const Component = ({ state, dom = U.variable(), rest = U.variable() }) =>
    <div ref={node => node ? state.set('foo') : state.set(undefined)}>
      {U.seq(dom,
            U.flatMapLatest(restRequest('/abc')),
            U.set(rest))}
      <h1>{U.view(L.identity, state)}</h1>
      <div>
        {rest.map(JSON.stringify)}
      </div>

      <a href="/">Back to home page</a>
    </div>;
  ```
</details>

---

## Observables
