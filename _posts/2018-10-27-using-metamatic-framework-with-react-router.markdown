---
layout: post
title:  "Using Metamatic Framework With React Router"
date:   2018-10-27 22:28:12 +0300
categories: metamatic
---

There have been some questions in the air asking whether and how the Metamatic framework supports React routes.
The answer is that there is absolutely no problem to use Metamatic together with React routes. These two libraries both tackle a different
problem, so they by no means stand in any conflict with each other or obstruct each other in any way.

An example of a React main class implementing both React-Routes and Metamatic-based state container would look somewhat like this:

{% highlight javascript %}
import React from 'react';
import './App.css';
import {Management} from './component/Management.js';
import Login from './component/Login.js';
import {connect, disconnect} from 'metamatic';
import {ACCESS_STATE} from './config/states';
import {CarFilterList} from './component/CarFilterList';
import {CarDetails} from './component/CarDetails';
import {Route} from 'react-router-dom';
import {Header} from './component/layout/header/Header';
import BrowserRouter from 'react-router-dom/es/BrowserRouter';

export class App extends React.Component {

  constructor(props) {
    super(props);
    this.state = {};
  }

  componentDidMount = () => 
      connect(this, ACCESS_STATE, (state) => this.setState(state));

  componentWillUnmount = () => disconnect(this);

  getViewComponent = () => this.state.loggedIn ? <Management/> : <Login/>;

  isLoggedIn = () => this.state.loggedIn === true;

  renderAuthorizedContent = () => (
      <div className="container-fluid">
        <Route path='/' component={Header}/>
        <Route exact path='/cars' component={CarFilterList}/>
        <Route exact path='/cars/:carId' component={CarDetails}/>
      </div>
  )

  renderUnauthorizedContent = () => (
      <div className="container-fluid">
        <Route path='/' component={Header}/>
        <Login/>
      </div>
  )

  renderContent = () => this.isLoggedIn() ? this.renderAuthorizedContent() : this.renderUnauthorizedContent();

  render = () => (
      <BrowserRouter>
        {this.renderContent()}
      </BrowserRouter>
  )
}
{% endhighlight %}

In the example above, a React app's main class connects to a Metamatic state 'ACCESS_STATE' from which it obtains an important
information, namely, whether the user is loggedIn or not. Based on that info, it then renders two exclusive routing strategies. 
One which is for a user that is not logged in and another for someone who is logged in. Certainly, for those who are not logged in,
it's only meaningful to show a "Log In" view but those who are in will be provided the full scale of navigation urls inside the App.

Please check the complete example in the [Github repository](https://github.com/develprr/metamatic-car-app).
