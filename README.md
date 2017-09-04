# React, Redux and styled-components Guide

A very highly opinionated guide to using React and styled-components.

This guide assumes you already have some knowledge of React, Redux and styled-components.

## Table of Contents

* [React and Redux](#react-and-redux)
  * [React Directory Structures](#react-directory-structures)
    * [Traditional (Rails-style)](#traditional-rails-style)
    * [Feature Based (Domain-style)](#feature-based-domain-style)
    * [Ducks Pattern](#ducks-pattern)
    * [Directory Structure Summary](#directory-structure-summary)
  * [Dumb, Smart, Presentational and Container Components](#dumb-smart-presentational-and-container-components)
    * [Presentational Component](#presentational-component)
    * [Container Component](#container-component)
    * [Typical Presentational Component Structure](#typical-presentational-component-structure)
    * [Typical Container Component Structure](#typical-container-component-structure)
    * [Summary of Presentational/Container Components](#summary-of-presentationalcontainer-components)
  * [Functional vs Class Components](#functional-vs-class-components)
    * [Functional Component](#functional-component)
    * [Class Component](#class-component)
* [styled-components](#styled-components)
  * [Component Structures with styled-components](#component-structures-with-styled-components)
    * [Monolithic Style](#monolithic-style)
    * [Traditional-based Style](#traditional-based-style)
  * [The Power of styled-components](#the-power-of-styled-components)
    * [Semantic Naming](#semantic-naming)
    * [BEM-style Modifiers](#bem-style-modifiers)
  * [Theming and Directory Structure](#theming-and-directory-structure)

## React and Redux

The following section contains my learnings and personal experience of directory structures and React components.

### React Directory Structures

The beauty of React is your whole app can be written in vanilla JS until you use their library features to render your DOM elements. Having said that, a structured app is always nice to have for the purpose of maintainability. You will notice that the structures defined below are heavily influenced by the use of Redux.

The following sections firstly illustrates the directory structure followed by some rhetorical questions that might help with your decision-making.

#### Traditional (Rails-style)

```markdown
project/
├──src/
│  ├──actions/
│  ├──components/
│  ├──constants/
│  ├──containers/
│  ├──reducers/
│  ├──routes/
│  ├──services/
│  ├──theme/
│  ├──utils/
│  ├──app.js (bootstrap the whole app)
├──package.json
├──README.md
```

Considerations:

* Are you new to [Flux](http://facebook.github.io/flux/) application architecture?
* Are you new to React and Redux?
* Need a particular way to manage application state?
* You have a well defined and not so complex project?
* Do you have team members that need/want to learn React and Redux?
* You might have experienced React/Redux developers joining later in the project...?

The penultimate point is very interesting because the separation of concerns in this directory structure lends itself well to either you learning these new libraries or simply teaching the rest of your team on how to utilise them.

The last point is also very valid because most React developers had started from or used this directory structure at some point. So if you're worried about project continuity then this is a good starting point.

#### Feature Based (Domain-style)

```markdown
project/
├──src/
│  ├──components/
│  ├──containers/
│  ├──routes/
│  ├──services/
│  ├──store/ (configure store for all reducers)
│  ├──theme/
│  ├──utils/
│  ├──app.js (bootstrap the whole app)
│  ├──reducers.js (combines all reducers)
├──package.json
├──README.md
```

Considerations:

* You have well defined components that independently access the global state?
* Your components do not rely on other component's actions and reducers?
* Aiming to write "atomic" or "node_modules" type components?
* Experienced with Flux architecture and Redux library?

So with "node_modules" or npm packages, they are all written in a way where they can be installed and imported to any projects. And, I think this is the whole idea of using a feature based directory structure. Your React components should be written in a way that is independent of one another and should accept some well defined props.

The last point covers those who know React and Redux, and are comfortable with using a different structure by wanting to group actions and reducers with the component itself. At some point in the beginning of a project there may have been some agreement that the components you have designed and going to implement will not rely on other actions and reducers. But projects always change...

#### Ducks Pattern

```markdown
project/
├──src/
│  ├──components/
│  ├──containers/
│  ├──ducks/ (quack quack)
│  ├──routes/
│  ├──services/
│  ├──store/ (configure store for all reducers)
│  ├──theme/
│  ├──utils/
│  ├──app.js (bootstrap the whole app)
│  ├──reducers.js (combines all reducers)
├──package.json
├──README.md
```

Considerations:

* You have used the Feature-based approach and have begun encountering those "changes"?
* Your power of foresight tells you that some of your components/containers will use different actions and reducers to access the global state?
* Ultimately, 95% of the time your action/reducer pairs only ever needs their associated action?
* Hell who needs structure? Just group your constants, actions and reducers together in a single file!
* You love ducks!

The main idea behind "Ducks" is to bundle these pieces together. For example, you have built a notification feature in your app but every component needs to dispatch a response to trigger a notification. Also, ONLY the notification feature is going to use those constants, actions and reducers. Add a duck to the pond, give it a name and marvel at its individuality.

So what you will end up is:

```markdown
project/
├──src/
│  ├──...
│  ├──ducks/
│  │  ├──Notification/
│  │  ├──Logout/
│  │  ├──...
│  ├──...
├──...
```

#### Directory Structure Summary

There are three main directory structures that you can decide to use. It seems to me that the "traditional" structure is one that is good for beginners or perhaps further down the line you are expecting more React developers to join. So this structure is easy to learn and easily recognisable by most React developers.

For experienced React developers, you will simply gain an understanding of how following a particular structure is not important. But more significantly, that you can identify, which structure or a combination of structures have been used, and that you can easily find where the actions and reducers are placed.

### Dumb, Smart, Presentational and Container Components

I think this topic is more important because it does influence how you design your directory structure. You will at some point come across the notion of dumb, smart, presentational and container components. If you follow the WET (write everything twice) principle then god help you. I prefer the DRY (don't repeat yourself) principle and this is where having this type of separation of concerns is very nice to have.

#### Presentational Component

These are components that will render to the DOM. It will contain your HTML elements and CSS styles. Nothing more.

However, there are some instances where you want your component to hold some state. If that's the case it should ONLY be presentational state. It might be a case that this particular component has a collapsible element and you want the component to remember whether it is open or closed. But, it should not store or handle global/application state.

Example presentational component:

```javascript
import React, { Component } from 'react'
import PropTypes from 'prop-types'

class Paragraph extends Component {
  static propTypes = {
    id: PropTypes.string,
    classname: PropTypes.string,
    children: PropTypes.func.isRequired
  }

  static defaultProps = {
    id: '',
    classname: ''
  }

  render() {
    return (
      <p id={props.id} className={props.classname} style={{ padding: 0, fontSize: '1rem' }}>
        {props.children}
      </p>
    )
  }
}

export default Paragraph
```

This is an overkill example by using a *Class Component* (more on this later). But firstly, I would like to point out that this is an excellent case to use [`PureComponent`](https://facebook.github.io/react/docs/react-api.html#react.purecomponent) rather than `Component`. That is because there are **no** deeply nested objects and arrays - only top level simple props waiting to be passed in.

Notice how we now have a reusable *Paragraph* component that accept props to modify some of it's attributes. In essense, we let other developers know that there's this component, it accepts the following props and will render a paragraph element with a padding of zero and font-size of 1rem.

#### Container Component

A container component does two things: fetches data and then renders its corresponding sub-component/s.

Normally, a container component is where the business logic resides. Maybe it's handling form inputs and needs to do validation before submitting via a service. Or, it could be grabbing data from somewhere, be it an API endpoint or a local database, then doing some enrichment before passing the data to a presentational component.

In most cases, our container component will need to access the application state, i.e. the Redux store. Therefore, we use React-Redux's feature to `connect` our container component to the Redux store.

Example container comopnent:

```javascript
import React, { Component } from 'react'
import { connect } from 'react-redux'

import Paragraph from 'components/paragraph'

class MyDiary extends Component {
  constructor() {
    super(props)

    this.state = {
      firstParagraph: '',
      language: props.language
    }
  }

  getDiaryEntries() {
    // make call to api or database
  }

  extractFirstEntry() {
    // store first entry into this.state.firstParagraph
  }

  translateParagraph() {
    // get language config from Redux store
  }

  render() {
    const { firstParagraph } = this.state

    return (
      <Paragraph id="my-paragraph" classname="MyDiary__first-entry">
        {firstParagraph}
      </Paragraph>
    )
  }
}

const mapStateToProps = (state) => {
  const { language } = state

  return {
    language
  }
}

export default connect(mapStateToProps)(MyDiary)
```

In this example, our container component is getting the language value from our Redux store, fetches data from an external sources and prepares the data before rendering to the Paragraph component. Notice how there is **no** HTML elements or CSS styling in this component.

One easy way to know if your component needs to be a container component is if it needs to access the Redux store.

> Note: your presentational component can also contain class methods to do some presentational logic.

#### Typical Presentational Component Structure

```markdown
project/
├──src/
│  ├──components/
│  │  ├──Button/
│  │  │  ├──tests/
│  │  │  ├──index.js
│  │  │  ├──style.css
│  │  │  ├──button-img.png
│  │  ├──Icon/
│  ├──...
├──package.json
├──README.md
```

The `components/` directory only contains presentational components. A presentational component typically consists of the JSX or .js file, a stylesheet and any associated static assets. If you're using a test framework like [Jest](https://facebook.github.io/jest/) then you can bundle your tests with the component.

#### Typical Container Component Structure

```markdown
project/
├──src/
│  ├──containers/
│  │  ├──LoginForm/
│  │  │  ├──tests/
│  │  │  ├──actions.js
│  │  │  ├──index.js
│  │  │  ├──reducer.js
│  │  ├──ProductSearch/
│  ├──...
├──package.json
├──README.md
```

The above container component structure follows the __*Feature based*__ directory structure. In this case the actions (with constants) and reducer is grouped within the container component. Most of the time, a container component will only need access to 1 pair of actions and reducer.

If this was following the Ducks pattern then the `LoginForm/` directory does not need *actions.js* and *reducer.js*.

#### Summary of Presentational/Container Components

Performance, reusability, maintainability and separation of concerns; these factors should be enough to convince you to follow the paradigm of using presentational and container components. In simple terms:

1. Presentional components are simple components that contain HTML elements and CSS styling
1. Container components fetches data, processes and passes it onto a presentational component

### Functional vs Class Components

ES6/ES2015 and above introduces quite a number of syntactic sugar to help us code in JavaScript that little bit easier. So you might feel a bit confused when you see different ways of writing React components. Rest assured, both functional and class components do the same thing, but if you want to handle state and have more functionality use Class components.

#### Functional Component

```javascript
import React from 'react'

const Paragraph = props => (
  <p style={{ padding: 0, fontSize: '1rem' }}>
    {props.children}
  </p>
)

export default Paragraph
```

Named as *functional component* makes sense because it is inherently a function (notice the Arrow Function *=>*). The ES5 form is basically:

```javascript
var Paragraph = function Paragraph(props) {
  return React.createElement(
    'p',
    { style: { padding: 0, fontSize: '1rem' } },
    props.children
  );
};
```

Also known as, and more fittingly, a *stateless component* because you cannot access React's state handling features.

#### Class Component

```javascript
import React, { Component } from 'react'

class Paragraph extends Component {
  constructor() {
    super(props)

    this.state = {
      message: '',
      config: props.config // do something with config state
    }
  }

  render() {
    const { id, classname } = props
    const { message } = this.state

    return (
      <p id={id} className={classname} style={{ padding: 0, fontSize: '1rem' }}>
        {message}
      </p>
    )
  }
}

export default Paragraph
```

If you want your component to store some state (notice it is NOT Redux state but *component state*) then use *class component*. You will then have full access to React state handling features and the [component lifecycle methods](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle).

## styled-components

No explanations here about styled-components as you can read more of it [here](https://www.styled-components.com/). So I'm just going to jump straight into the thick of it.

### Component Structures with styled-components

How you write your React components with styled-components is down to personal preference. But there are some fundamental conventions I would like to share that will hopefully help you write more maintainble components.

#### Monolithic Style

```javascript
import React from 'react'
import styled from 'styled-components'

const Container = styled.div`
  width: 960px;
`

const Header = styled.header`
  border-color: red;
`

const Footer = styled.footer`
  border-color: blue;
`

const MainNav = styled.nav`
  border-color: orange;
`

const MainContent = styled.section`
  border-color: green;
`

const HomePage = props => (
  <Container>
    <Header />
    <MainNav />
    <MainContent />
    <Footer />
  </Container>
)

/*
 * rendered page looks like this
 * <div>
 *   <header></header>
 *   <nav></nav>
 *   <section></section>
 *   <footer></footer>
 * </div>
 */

export default HomePage
```

What I have here is a very simple *HomePage* component. So I `import` both React and styled-component library, my styled HTML elements and render method are all in the same file. This example, using a *functional component*, also works with a *class component*. Observe the example below:

```javascript
import React, { Compoennt } from 'react'
import styled from 'styled-components'

const Container = styled.div`
  width: 960px;
`

const Header = styled.header`
  border-color: red;
`

const Footer = styled.footer`
  border-color: blue;
`

const MainNav = styled.nav`
  border-color: orange;
`

const MainContent = styled.section`
  border-color: green;
`

class HomePage extends Component {
  render() {
    return (
      <Container>
        <Header />
        <MainNav />
        <MainContent />
        <Footer />
      </Container>
    )
  }
}

export default HomePage
```

This is pretty much exactly the same except you've defined the styled elements outside of *class*. You can of course define them inside of *class* as shown below:

```javascript
import React, { Component } from 'react'
import styled from 'styled-components'

const Container = styled.div`
  width: 960px;
`

const Header = styled.header`
  border-color: red;
`

const Footer = styled.footer`
  border-color: blue;
`

class HomePage extends Component {

  navigationMarkup() {
    const MainNav = styled.nav`
      border-color: orange;
    `

    return (
      <MainNav />
    )
  }

  contentMarkup() {
    const MainContent = styled.section`
      border-color: orange;
    `

    return (
      <MainContent />
    )
  }

  render() {
    return (
      <Container>
        <Header />
        { this.navigationMarkup() }
        { this.contentMarkup() }
        <Footer />
      </Container>
    )
  }
}

export default HomePage
```

But of course, the laws of scoping applies and only those styled elements are tied to their respective class method. And, I generally don't see the point in this to be honest as it makes your methods look bloated. Moreover, I wouldn't mix my CSS coding with JS coding like that.

#### Traditional-based Style

Utilise the power of `import` and `export` and do something like this:

```markdown
project/
├──src/
│  ├──components/
│  │  ├──Button/
│  │  │  ├──tests/
│  │  │  ├──index.js
│  │  │  ├──style.js // <-- your styled elements live here
```

```javascript
// style.js

import styled from 'styled-components'

const Container = styled.div`
  width: 960px;
`

const Header = styled.header`
  border-color: red;
`

const Footer = styled.footer`
  border-color: blue;
`

const MainNav = styled.nav`
  border-color: orange;
`

const MainContent = styled.section`
  border-color: green;
`

export { Container, Header, Footer, MainNav, MainContent }
```

```javascript
// index.js

import React from 'react'
import {
  Container,
  Header,
  Footer,
  MainNav,
  MainContent
}

const HomePage = () => (
  <Container>
    <Header />
    <MainNav />
    <MainContent />
    <Footer />
  </Container>
)

export default HomePage
```

Just like how you would code React components with CSS or SASS and have a separate *style.css* file in the directory, you do the same with styled-components and have a *style.js*.

Now you might start to think, my **AboutUsPage** component can reuse the *style.js* from my **HomePage** component. I wouldn't bother because:

1. Maybe you're not componentising your page elements properly?
1. What if you need to refactor your HomePage presentational component into something else that needs different styling?
1. You're using a criss-cross pattern that will confuse the hell out of the other developer who's expecting the styles to be unique to **HomePage** component but finds that **AboutUsPage** is importing the same *style.js* too.

Keep it simple, stick to a known structure and pattern, and maintain seperation of concerns: JavaScript codes together and CSS codes together.

### The Power of styled-components

React is there to help you write reusable components and with styled-components you will write more semantic elements. Behold the example below:

#### Semantic Naming

```javascript
import React from 'react'
import styled from 'styled-components'

const Input = styled.input.attrs({
  fontSize: props => props.myBigBox ? '24px' : '10px',
  margin: props => props.myBigBox || '10px',
  padding: props => props.myBigBox || '10px'
})`
  border: 2px solid red;
  border-radius: 3px;
`

/*
 * Our rendered element looks like this:
 * <input id="" name="" type="" placeholder="" size="" required>
 */

const TextField = props => (
  <Input 
    type="text" 
    id={props.id} 
    name={props.name} 
    placeholder={props.placeholder} 
    size={props.size} 
    required={props.required} 
    myBigBox={props.myBigBox}
  />
)

const PasswordField = props => (
  <Input 
    type="password" 
    id={props.id} 
    name={props.name} 
    placeholder={props.placeholder} 
    size={props.size} 
    required={props.required} 
    myBigBox={props.myBigBox}
  />
)

const EmailField = props => (
  <Input 
    type="email" 
    id={props.id} 
    name={props.name} 
    placeholder={props.placeholder} 
    size={props.size} 
    required={props.required} 
    myBigBox={props.myBigBox}
  />
)

// export beautiful semantic form inputs
export { TextField, PasswordField, EmailField };
```

In the above example you can see how I've used one styled-component input element and have my three differently typed and semantically named fields, which all share the same style. And, because we're a good developer, we make sure we pass in the necessary *attributes* to render our input field so we're conforming to the HTML 5 semantic standards. Don't forget to enforce these with PropTypes.

#### BEM-style Modifiers

```javascript
import React from 'react'
import styled from 'styled-components'

const Button = styled.button.attrs({
  id: props => props.id,
  name: props => props.inputName,
  type: 'reset' // you can use static props too
})`

  /*
   * Just because we're using styled-components now doesn't mean
   * we should forget about using good CSS coding convention
   */

  // define the button structure first

  height: 25px;
  padding: 1em;
  width: 100%;

  // then use modifiers to change the look and feel!

  ${props => props.red && `
    background-color: #ff0000;
  `}

  ${props => props.green && `
    background-color: #00ff00;
  `}

  ${props => props.blue && `
    background-color: #0000ff;
  `}

  ${props => props.whiteFont && `
    background-color: #ffffff;
  `}

  ${props => props.blackFont && `
    background-color: #000000;
  `}
`

const MyForm = props => (
  <form>
    <Button red whiteFont>
    <Button green blackFont>
    <Button blue whiteFont>
  </form>
)

export default MyForm;
```

If you're from a BEM background then you'll recognise and love this approach. Our **Button** styled-component will now accept these extra props that have been defined and apply the called styles accordingly.

### Theming and Directory Structure

I will be using the [Create React App](https://github.com/facebookincubator/create-react-app) to demonstrate how you could structure your app with styled-components.

```markdown
project/
├──build/
├──config/
├──node_modules/
├──public/
├──scripts/
├──src/
│  ├──components/
│  │  ├──Button/
│  │  │  ├──index.js
│  │  │  ├──style.js // <-- separation of concerns
│  ├──routes/
│  ├──theme/         // <-- theme folder
│  │     ├──fonts/
│  │     ├──index.js/
│  ├──App.js
│  ├──App.style.js   // <-- global styles
│  ├──index.js
│  ├──registerServiceWorker.js
├──package.json
├──README.md
```

The following artefacts to take note of are:

1. My **Button** component has a separate *style.js* file to hold styled-components elements
1. I have a **Theme** directory within *src/* where I have my one source of truth values that defines my theme
1. There is now an App.style.js to use styled-components' [injectGlobal](https://www.styled-components.com/docs/api) API

Let's first take a look at *index.js*

```javascript
// src/index.js

import React from 'react';
import { render } from 'react-dom';
import { BrowserRouter } from 'react-router-dom';

import App from './App';

import registerServiceWorker from './registerServiceWorker';

render((
  <BrowserRouter>
    <App />
  </BrowserRouter>
), document.getElementById('root'));

registerServiceWorker();
```

Pretty standard entry to my React app by bootstrapping some libraries together.

Next up, we'll have a closer look at *App.js*

```javascript
// src/App.js

import React from 'react';
import { ThemeProvider } from 'styled-components'; // using styled-components ThemeProvider API

// normalize and font-awesome imported from node_modules/
import 'normalize.css'; // a reset style - very useful!
import 'font-awesome/css/font-awesome.min.css'; // because I like using icons in my app

import theme from './theme'; // my one source of truth
import './App.style'; // my one global stylesheet

import Header from './components/Header';
import Footer from './components/Footer';

import Routes from './routes';

// the theme applies to the whole app
const App = () => (
  <ThemeProvider theme={theme}>
    <div>
      <Header />
      <Routes />
      <Footer />
    </div>
  </ThemeProvider>
);

export default App;
```

Take note of how I have put the reset style [Normalize.css](https://necolas.github.io/normalize.css/) first and my *App.style.js* last. You don't want to apply a reset after your App style. So now the whole app will be 'Normalized', can use Font-Awesome icons and my React components can access the *theme variables* in `theme` object.

And now, our one source of truth, the theme variables:

```javascript
// src/theme/index.js

const theme = {
  font: {
    family: "'Inconsolata', monospace",
    base: '1rem'
  },
  color: {
    red: '#ff0000'
  },
  spacing: {
    default: '20px',
    half: '10px',
    quarter: '5px',
    double: '40px',
  },
};

export default theme;
```

I've got some theme variables in here so far but you get the picture. There will be much more as the App grows. In other projects, I have used a *colors.js* file to hold a full blown colour palette like [Material Designs colour definition](https://github.com/google/material-design-lite/blob/mdl-1.x/src/_color-definitions.scss). I then make references to the colour palette in `/src/theme/index.js`.

And finally, you want your App specific 'reset' or global styles for the whole app.

```javascript
// src/App.style.js

import { injectGlobal } from 'styled-components';

import Inconsolata from './theme/fonts/Inconsolata-Regular.ttf'; // <-- so that Webpack can do its bundling magic
import theme from './theme'; // <-- might have defined many theme variables in here to use within the body styling

const { font } = theme;

export default injectGlobal`
  @font-face {
    font-family: 'Inconsolata';
    font-style: normal;
    font-weight: 400;
    src: url(${Inconsolata}) format('truetype');
    unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2212, U+2215;
  }

  html {
    box-sizing: border-box;
    font-size: 100%;
    height: 100%;
    touch-action: manipulation;
    width: 100%;
  }

  *, *:before, *:after {
    box-sizing: inherit;
  }

  body {
    font-family: ${font.family};
    line-height: 1.618;
    margin: 0;
    min-height: 100%;
    width: 100%;
  }
`;
```

Now in the words of the [styled-components bible](https://www.styled-components.com/docs/api):

> We do not encourage the use of this. Try to use it once per app at most, if you must, contained in a single file. This is an escape hatch. Only use it for the rare @font-face definition or body styling.

Well I've done a cheeky one and defined a reset with *html* followed by the `box-sizing` attributes. You could define some other resets like `ul { list-style: none }` and other primitive element styles. As long as you're not defining *ID* or *Class* styles in this particular file.

> Note: you should be using some sort of web font loader to prevent the dreaded [FOIT](https://css-tricks.com/fout-foit-foft/) symptom.

And now, the **Button** component has access to the theme object due to the *ThemeProvider*. Additionally, it can `import` the theme variables directly if needed be.

To summarise, we have now defined a directory structure and pattern for theming and styling our React app. We've introduced a `theme` folder for the purpose of storing our one source of truth - the theme variables. We've made use of `injectGlobal` to apply a global style to our app. Inherently, the convention for styling the presentational components is to access the theme variables via `ThemeProvider` or import them directly. You can go further by adding a *utils* directory that holds *CSS mixins* courtesy of the [`css`](https://www.styled-components.com/docs/api) API and helper components that helps with layout styling.

I hope you find this guide helpful and good luck with choosing a directory structure for your React Redux app and utilising styled-components.