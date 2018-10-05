---
layout: post
title:      "So, what exactly are Bind Action Creators?"
date:       2018-10-05 22:15:10 +0000
permalink:  so_what_exactly_are_bind_action_creators
---


I love Redux. I really do.

but, it wasn't always like this.

So the whole entire redux data flow was a bit jumbled up in my mind. Yes yes, it’s one thing to do the labs and be spoon fed and another thing to build it out from scratch on your own.

Look for example something like this - 
```
import React from 'react';
import { connect } from 'react-redux';
import VerseCard from '../components/VerseCard';
import { loadVerses } from '../actions/verses';

class VerseIndex extends React.Component {

  componentDidMount() {
    this.props.loadVerses()
    }


  render() {
    return(
      <div>
      { this.props.verses !== [] ? this.props.verses.map( verse =>
      <VerseCard key={verse.id} index={verse.id} verse={verse} /> ) : null}
    </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    verses: state.verses
  }
}

export default connect(mapStateToProps, {
  loadVerses
})(VerseIndex)

```

In the rails api backend, when the componentDidMount lifecycle is called, this.props calls the loadVerses() function – which is this line of code:

```

export const loadVerses = () => {
  return dispatch => {
    return fetch(`http://localhost:3001/verses`)
    .then(res => res.json())
    .then(verses => dispatch(getVerses(verses)))
  }
}
```

then if you caught that last bit, the getVerses() action creator is called.

```
const getVerses = verses => {
  return {
    type: "LOAD_ALL_VERSES",
    verses
  }
}
```	
this then goes to the reducer finally!

```
export default (
  state = [], action) => {

  switch(action.type){
    case "LOAD_ALL_VERSES":
      return action.verses
    default:
      return state
  }
}	

```

so, let’s run through that one more time. 
•	loadVerses() – dispatch action to the backend Rails API 
•	once the promise is fulfilled in the backend, the store is listening to the dispatch – which is the getVerses()
•	once the action.payload and action.type is determined, in this case “LOAD_ALL_VERSES”, then the verses (the payload) is processed in the reducer,  which then pulls up all the data, which is “verses” in this case.

So, essentially, I wouldn’t be needing mapDispatchToProps in my VerseIndex component, nor bindActionCreators. 
But I guess a hoity toity way of doing things would be to utilize the said bindActionCreators in mapDispatchToProps in the connect() function. It would look something like this for the VerseIndex page.

```
const mapDispatchToProps = (dispatch) => {
return {
    getVerses: bindActionCreators(loadVerses, dispatch),
  }
};


```

The docs make it pretty straightforward – “Only use case for bindActionCreators when you want to pass some action creators down to a component that isn’t aware of Redux, and you don’t want to pass dispatch or the Redux store to it.”

Only necessary if you need to pass down to child component and the child component is a stateless component.

Go to https://redux.js.org/api/bindactioncreators to learn more. 


