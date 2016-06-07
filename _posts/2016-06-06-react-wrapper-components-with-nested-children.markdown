---
layout: post
title: "React wrapper components with nested children"
date: "2016-06-06 23:27:03 -0500"
---


Say you want to create a component that acts as a wrapper such that you can pass it nested child components like so:

```html
<WrapperComponent title="I am the wrapper">
  <ChildComponent body="Hello from child component" />
</WrapperComponent>
```

This can be achieved by using this.props.children as in this example:

```jsx
class WrapperComponent extends Component {
  render() {
    return (
      <div className="wrapper">
        <h1>{this.props.title}</h1>
        {this.props.children}
      </div>
    );
  }
}

class ChildComponent extends Component {
  render() {
    return (
      <p>{this.props.body}</p>
    );
  }
}

class App extends Component {
  render() {
    return (
      <WrapperComponent title="I am the wrapper">
        <ChildComponent body="Hello from child component" />
      </WrapperComponent>
    );
  }
}
```

Mounting App will produce the following markup:

```html
<div class="wrapper">
  <h1>I am the wrapper</h1>
  <p>Hello from child component</p>
  </div>
```

