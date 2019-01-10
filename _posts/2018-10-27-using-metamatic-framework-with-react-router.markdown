---
layout: post
title:  "Using Metamatic Framework With React Router"
date:   2018-10-27 22:28:12 +0300
categories: metamatic
---
The Metamatic™ framework sits very well together with React routing scheme. The following  
example of a React main class implementing both React-Routes and Metamatic-based state container would look somewhat like this:

{% highlight javascript %}
import React from 'react';
import './App.css';
import {Management} from './component/Management.js';
import Login from './component/Login.js';
import {connectToStore, disconnectFromStores} from 'metamatic';
import {STORE_AUTHORIZATION} from './config/stores';
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
    connectToStore(
      this, 
      STORE_AUTHORIZATION, 
      (incomingStore) => this.setState(incomingStore)
    );

  componentWillUnmount = () => disconnectFromStores(this);

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

  renderContent = () => this.isLoggedIn() ? 
    this.renderAuthorizedContent() : this.renderUnauthorizedContent();

  render = () => (
    <BrowserRouter>
      {this.renderContent()}
    </BrowserRouter>
  )
}
{% endhighlight %}

In the example above, a React app's main class connects to a store 'STORE_AUTHORIZATION' from which it obtains an important
information, namely, whether the user is loggedIn or not. Based on that info, it then renders two exclusive routing strategies. 
One which is for a user that is not logged in and another for someone who is logged in. Certainly, for those who are not logged in,
it's only meaningful to show a "Log In" view but those who are in will be provided the full scale of navigation urls inside the App.

Please check the complete example in the [Github repository](https://github.com/develprr/metamatic-car-app).
