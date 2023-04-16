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

```typescript
type KeyPageState = Record<string, PageState>;
```


### To

A reserved state, used for navigation and tab control.

```typescript
type To = {
  value: string[];
  onValue?: OnValueTask[];
};
```


### PageState

To store a state on a page. All tasks in the array would be called when value assigned.

```typescript
type PageState = {
  value: unknown;
  onValue: OnValueTask[];
};
```


### OnValueTask

An object linked to another object which bring a value.

|Object|Receive|
|-|-|
|`Mono`|receive value from parent `Mono`|
|`PageState`|receive value from `state` `PageEvent`|
|`fetch` `PageEvent`|receive value from itself|

- `keyPath` is used to parse the received value.
- `type` is used to force convert the parsed value to a certain type.
- `from` is used to replace all value equal to `from` in `event`.
- `event` is an array, all events in `event` will be called.
- `map` is a 2 * 2 number array, and is used to map value base on it. Not support string mapping now.

```typescript
type OnValueTask = {
  keyPath?: string[];
  type?: 'number' | 'string';
  from?: string;
  event?: PageEvent[];
  map?: number[][];
};
```
