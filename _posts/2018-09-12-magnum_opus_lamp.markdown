---
layout: post
title:      "MAGNUM OPUS: LAMP"
date:       2018-09-12 09:49:53 -0400
permalink:  magnum_opus_lamp
---

So, in terms of the ideation process of this last, Magnum Opus - final culmination of my months toiling away at learning web development, I thought of something I would personally need and use. So hence the application concept LAMP.

Your word is a lamp to my feet, a guide to my path. - Psalm 119:105

Truly, the greatest way to learn to code is through head on building something.

One huge takeaway is this: React is a granular approach to making the composite application or webpage a organized composite. Having small containers and components really help because they can be implemented across pages and routes in such a versatile way.

The User Interface is clean and easy to follow, especially when Redux stores all the objects across the different components. How convenient to have such a well connected and reliable way to streamline the workflow! If react is the nuggets of data, redux is the communicator/mediator. My Rails API backend is there the composite data components would be stored.

Redux dispatch actions and reducers kind of confused me at first, especially when I wasn’t really sure why certain props were mounting – like this.props.verses on the Verse Index page, but it was always on the second refresh of the context.

 

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

debugger;

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

So when I threw a debugger right after the render(), the first debugger would always yield an empty array of verses. Only after the async action was complete – after the componentDidMount(), did I get my loadVerses() through, hence, the array of my entire verses pulling up. So the tricky part was then catching this context and then rendering each verse (all 200+ of them) to the VerseCard components.

 

With a few tutorials, resources, and help, I figured out a way to render each verse based on that correct context. I used the same logic and pattern when rendering all the prayers to the Prayer route.

 

Overall, it was one thing to do the labs and learn the concepts and another to actually finally get to build something myself from scratch! Also, redux is complex, but definitely makes the flow easier in a bigger application like this.

 

One thing I would have done differently though, is this - I started building the client side first. It was sort of awkward, envisioning how the data would run back and forth the api when nothing was there yet. It felt a bit like working backwards - like reading a book from the end.

 

Overall, I’m really happy to say, this was definitely an eye-opener and I look forward to building many React-Redux applications!
