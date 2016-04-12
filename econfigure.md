---
layout: page
title: Configure
permalink: /configure/
---

If you have a solid understanding of redux basics and what the ember bindings offer, this section will help you unlock the potential of ember-redux by showing all the extension points!

**Enhancers**

In redux [enhancers][] allow you to write a function that produces a "new and improved" store creator. The most well known enhancer is one that enables [time travel debugging][]. To write a custom enhancer that enables time travel debugging (with help from the [chrome dev tools][]) just add a new directory named `enhancers` and inside it a single file `index.js`

```js
//app/enhancers/index.js
import redux from 'npm:redux';

var devtools = window.devToolsExtension ? window.devToolsExtension() : f => f;

export default redux.compose(devtools);
```

**Middleware**

In redux [middleware][] allows you to write a function that produces a "new and improved" dispatch. By default we include [redux-thunk][] to enable async actions. If you want to override this just add a new directory named `middleware` and inside it a single file `index.js`

```js
//app/middleware/index.js
import thunk from 'npm:redux-thunk';

var resolved = thunk.default ? thunk.default : thunk;

var warnz = function({dispatch, getState}) {
  console.warn('wait!');
  return next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState);
    }

    return next(action);
  };
};

export default [resolved, warnz];
```

**Reducers**

In redux [reducers][] take the current state along with some action and return a new state. One reducer that is special to `ember-redux` is named `optional.js` and it allows you to audit any/all events that flow through dispatch. One example of this might be to write each event to a special audit list.

```js
//app/reducers/optional.js
import Ember from 'ember';
import AuditLog from '../utilities/audit';

export default function(combine) {
    return (state, action) => {
        var entries = AuditLog.entries;
        entries.push({
            uuid: Ember.uuid(),
            action: action,
            state: state
        });
        return combine(state, action);
    };
}
```

[chrome dev tools]: https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en
[time travel debugging]: https://www.youtube.com/watch?v=xsSnOQynTHs
[enhancers]: https://github.com/reactjs/redux/blob/master/docs/Glossary.md#store-enhancer
[middleware]: https://github.com/reactjs/redux/blob/master/docs/Glossary.md#middleware
[reducers]: https://github.com/reactjs/redux/blob/master/docs/Glossary.md#reducer
[redux-thunk]: https://github.com/gaearon/redux-thunk
