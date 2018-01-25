# RouteHook

A  React Component that wraps the `Route` Component of React-Router v4 with `onEnter` and `onChange` hooks.

## Motivation

Although react-router v4 is great, and declarative routing is much better than static routing, there was one lost that seemed kind of unnecessary.

Needing to write dummy components as classes only to make use of their lyfecyle hooks as, `componentDidMount` when the pathname matches and the component is mounted, or `componentWillReceiveProps` when the pathname changes it's a bit annoying.

Looking at a beautiful Stateless Functional Component, being turned into a React Class just to dispatch an action in its LifeCycle Hook... it just breaks my heart.

This component only purpose is to keep our dummy components as statless pure functions, and yet, be able to keep using our hooks when we navigate through our app.


## Installation

```
npm install --save react-router-routehook
```

## Usage

`RouteHook` takes the `path`, `component`, `render`, `exact`, and `strict` props as the official `Route` does, and takes two additional optional props:

- `onEnter`
- `onChange`

> **IMPORTANT** `children` prop is not supported.

### onEnter

`onEnter` will run when the component beeing rendered by the route is mounted, and will receive as arguments the router props, this means, an object with the properties: `match`, `location` and `history`.

onEnter is basically a wrapper around the functionality of `componentDidMount`.

### onChange

`onChange` will run when the component will receive new router props, and will receive as arguments the new props and the old ones. 

onChange is basically a wrapper around the functionality of `componentWillReceiveProps`.

### Example


```JSX
import React from 'react';
import RouteHook from 'react-router-routehook';

const Data = (props) => (
  <div>
    {props.data}
  </div>
);

class AppContainer extends React.Component {

  state = {
    data: '',
  }

  fetchData = (props) => {
    axios.get(`/api/${props.match.params.id}`)
      .then(res => this.setState({ data: res.data }))
  }

  shouldFetchData = (newProps, oldProps) => {
    if (oldProps.match.params.id !== newProps.match.params.id) {
      this.fetchData(newProps)
    }
  }

  render() {
    return (
      <RouteHook 
        path="/:id"
        onEnter={this.fetchData}
        onChange={shouldFetchData}
        render={(routerProps) => <Data data={this.state.data} />}
      />
    )
  }
} 

```
