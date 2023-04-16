一个 page 维护一个 state store 和一个 view tree，它俩都是该 page 所维护的数据。
通过 event 来改变 page 所维护的数据，比如改变一个 key state，或一个 key view map。
event 也可以从远端获取数据。

state -> onValue -> event -> state/view
view -> event -> state/view


### Mono

Represent a compomont. A compomont must has a tree structure, and initial value of `subviews` represent the initial tree. The tree can be changed during the app running by the `views` events.

```typescript
export type Mono = {
  type?: PageType;
  to?: To;
  state?: KeyPageState;
  subviews?: Subview[];
  event?: KeyPageEvents;
  onValue?: OnValueTask[];
};
```


### KeyPageState

Each page could has 1 state map.

```ts
type KeyPageState = Record<string, PageState>;
```


### To

A reserved state, used for navigation and tab control.

```ts
type To = {
  value: string[];
  onValue?: OnValueTask[];
};
```


### PageState

To store a state on a page. All tasks in the array would be called when value assigned.

```ts
type PageState = {
  value: unknown;
  onValue: OnValueTask[];
};
```


### OnValueTask

An object linked to another object which bring a value. This kind of object has `OnValueTask`.

|Object|Receive|
|-|-|
|`Mono`|receive value from parent `Mono`|
|`PageState`|receive value from `state` `PageEvent`|
|`fetch` `PageEvent`|receive value from itself|

- `keyPath` is used to parse the received value.
- `type` is used to force convert the parsed value to a certain type.
- `from` is used to replace all value equal to `from` in `event`.
- `map` is a 2 * 2 number array, and is used to map value base on it. Not support string mapping now.
- `event` is an array, all events in `event` will be called.

```ts
type OnValueTask = {
  keyPath?: string[];
  type?: 'number' | 'string';
  from?: string;
  event?: PageEvent[];
  map?: number[][];
};
```


### Event

An event is change state and view, or fetch value.

```ts
type Event = ToEvent | StateEvent | FetchEvent | ViewEvent;
```


### ToEvent

Event that switch tab or change navigation.

```ts
type ToEvent = {
  task: 'to';
  type?: 'push' | 'pop' | 'dismiss' | 'select';
  key?: string;
};
```


### StateEvent

Event that change value of a state.

```ts
type StateEvent = {
  task: 'state';
  parent?: boolean;
  key: string;
  value?: unknown;
  type?: 'push' | 'pop' | 'add' | 'subtract' | 'multiply' | 'divide' | 'mod';
};
```


### FetchEvent

Event that fetch value.

```ts
type FetchEvent = {
  task: 'fetch';
  url?: string;
  method?: 'get' | 'set';
  app?: {
    key: string;
    secret: string;
  };
  user?: {
    key: string;
    secret: string;
  };
  onValue?: OnValueTask[];
};
```


### ViewEvent

Event that change view tree.

```ts
type ViewEvent = {
  task: 'views';
  // 这里的unknown其实是Subview，是不是叫做keySubview更好？但由于里面可以有from，没法直接写Subview
  value?: Record<string, unknown>;
  duration?: number;
};
type Event = ToEvent | StateEvent | FetchEvent | ViewEvent;
```
