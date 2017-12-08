---
layout: post
title: Copy on Read with ES6 Proxy
category: blog
tags: [programming, web, es6, javascript]
---

In NREL's [FloorspaceJS](https://github.com/NREL/floorspace.js) app, we used to define default values in code and also in the json schema. This wasn't ideal, because the two sources could get out of sync. We decided to keep the values *only* in the schema, and read them into the app to use them.

I wrote some code to walk through the schema and return any default values it finds.

```javascript
import schema from 'schema/geometry_schema.json';

const readDefaults = (definition) => {
  if (_.has(definition, 'default')) return definition.default;
  if (definition.type === 'null') return null;
  if (definition.type === 'array') return [];
  if (definition.type === 'object') {
    return _.mapValues(definition.properties, readDefaults);
  }
  return null;
};

export const defaults = _.mapValues(schema.definitions, readDefaults);
```

Then we can use the defaults like this:

```javascript
export function Story(name) {
  return {
    ...defaults.Story,
    id: idFactory.generate(),
    name,
    color: generateColor('story'),
  };
}
```

If you're very clever, you might notice a problem with this approach. If a schema definition includes an array property, our factory function will put the *very same array* in each Story it creates. Pushing to one story's `spaces` property will add a value to *every* story. It even adds the new value to the array inside the default value, so it affects stories that haven't even been created yet.

The solution is that each time we use the default value, we should make sure that it is a fresh version. I wasn't a fan of requiring every use of the defaults object to include a `_.cloneDeep`. It seemed repetitive, and need extra code at the call-site is an ugly pattern.

Here's what I came up with using a Proxy object to enforce making a deep copy on read:

```javascript
const rawDefaults = _.mapValues(schema.definitions, readDefaults);
export const defaults = new Proxy(rawDefaults, {
  get(target, key) {
    return _.cloneDeep(target[key]);
  },
  set() {
    throw new Error('Please do not change the defaults.');
  },
});
```

Now code using the defaults object is *unabled* to mess with the original values.
