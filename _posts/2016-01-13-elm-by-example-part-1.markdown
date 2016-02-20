---
layout: post
title: "Elm by Example: Soup to Nuts - Part 1"
date: "2016-01-13 00:18:54 -0600"
---

By now you have probably heard about Elm, the statically typed, immutable, Haskell inspired, polite and helpful, functional reactive language for the web.

It's extremely FAST too. It consistently performs better than React, Ember, Angular and others in the TODO MVC performance tests.

![performance comparison](http://elm-lang.org/diagrams/sampleResults.png)


I've been experimenting with Elm for the past few months and have come to really appreciate its style of programming. It is very similar to React in the sense that you can render modular components based on DOM events, but the functional style and syntactic sugar are a pleasure to work with.

My favorite thing about Elm is that it is statically typed, yet type inferred. What that means is you can prototype quickly, and don't have to use type annotations, but the compiler will infer the types for you by flowing through your code and failing to compile when you did something wrong. This gives rise to Elm's best feature:

**NO RUNTIME EXCEPTIONS!**

This is a really big deal! After having written a fair amount of Elm it almost feels irresponsible writing JavaScript without this feature. Elm accomplishes this by forcing you to handle values that can be null before allowing you to compile your project. It also makes sure that you handle all potential values when using conditionals/pattern matching.

As you work with Elm its awesomeness unfolds before you, and you will learn interesting Computer Science concepts, particularly if you have never worked with Haskell or other functional languages. Although Haskell can be hard to learn, Elm is very pragmatic and approachable and can be used to replace both standalone JavaScript libraries and rich UI components. Elm also lends itself really well for game programming due to its rich HTML5 Canvas abstraction and input interaction using signals.

## Motivation
My reason for writing this blog post is that I was struggling with some of the more advanced concepts of Elm, namely Signals, Mailboxes, and Ports. I started writing a post about how to roll out your own Model View Update pattern in Elm without the StartApp but it was hard to start without an initial example, so I decided to write this post first to lead into the next one.

In this two-part blog post I will take you through building your first Elm component - a Slack inspired quick channel switcher (Cmd+k).

I chose this component because it was small, practical, and combined multiple Signals, namely HTML Signals and Keyboard Signals, making it an ideal candidate for introducing Signals and Mailboxes.

![Slack channel switcher](https://www.dropbox.com/s/5fndqdbkoqf3y5r/Screenshot%202016-01-05%2009.23.54.png?dl=1)


## Prerequisites
I'm assuming basic familiarity with the Elm syntax. If you are not familiar with the Elm syntax see the [official syntax documentation](http://elm-lang.org/docs/syntax).

Consider the above to be Part 0.

## Getting Elm
To get started you will need to install Elm on your machine.

```sh
npm update && npm install -g elm
```

This article is written for Elm v0.16

You can also download the .pkg installer from the elm-lang.org website.

_You will also need a syntax highlighter for your editor. Here's the one I use for Vim: https://github.com/ElmCast/elm-vim_

## Installing required packages
Elm comes with an especially "polite" and quite "intelligent" package manager. It takes a Github relative url as an argument.

Create a project directory and cd into it.

Install the following packages:

```sh
elm package install evancz/elm-html
elm package install evancz/start-app
elm package install circuithub/elm-html-extra
```

## Bootstrapping the component
In your favorite code editor, create a `ChannelSwitcher.elm` file.

First we need to declare the component, this is done with one line in elm which should be at the top of your file.

```elm
module ChannelSwitcher where
```

Now, below that, we need to import all the necessary modules:

```elm
-- IMPORTS
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Html.Events.Extra exposing (..)
import String
import StartApp.Simple as StartApp
```

Note that I'm using `exposing (..)` on some of the imports, the `..` exposes all public functions from that module into the current scope so you can call them without prefixing with the module name.

It is usually a best practice to avoid this type of exposure as much as possible to prevent naming collisions and ambiguity. However in this case it provides convenience when writing HTML.

## MODEL VIEW UPDATE
Elm uses a Model View Update architecture which dictates the way data flows through an Elm application. You can think of an Elm application as a stream of events which are converted into actions, which then calculate the new state and render HTML.

I like annotating my code with sections so that it is organized and I know where to look for things so I label it like so:


```elm
module ChannelSwitcher where

-- IMPORTS
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Signal
import StartApp.Simple as StartApp

-- MODEL

-- UPDATE

-- VIEW
```

## Writing the HTML in Elm
Now that we imported all of the HTML attributes it's time to write some Elm-flavored HTML.

Under the view section declare a `view` function, follow that function with a `main` function, the entry point for any component. For now we will just use it to call view so we can see the HTML we generated.

```elm
-- VIEW
view =
  div [ class "container" ] [
    input [ class "search-box" ] [ ],
    ul [ class "collection" ] [
        li [ class "collection-item active" ] [ text "#Elm" ],
        li [ class "collection-item" ] [ text "#react.js" ],
        li [ class "collection-item" ] [ text "#ember" ]
    ]
  ]

main =
	view
```

If you are familiar with React the code above should seem familiar. Think of it as the `render` function in react.

The code above should be pretty self explanatory, the first square bracket of each element is its attributes, and the second is the tag content.

Both `text` and `class` are functions that we imported from `Html.Attributes` and the HTML elements come from exposing the `Html` package.

## Compiling and Running in your browser
Now we are ready to compile and run our application. In the terminal run elm make ChannelSwitcher.elm. This will generate an _index.html_ for you, go ahead and open that in your browser.

You should be able to see the following interface:

![First Step](https://www.dropbox.com/s/05upf35d98g91q3/Screenshot%202015-12-30%2014.09.10.png?dl=1)

## Defining the Model
The data that we need to flow through our component for it to generate the correct output should represent a list of channels.

We start by defining a record, and we will also define an initial model so we have something to work with.

```elm
-- MODEL
type alias Model =
  { channels: List String
  , selectedChannel: Int
  , query: String
  }

initialModel : Model
initialModel =
  { channels = ["Elm", "React.js", "Ember", "Angular 2", "Om", "OffTopic" ]
  , selectedChannel = -1
  , query = ""
  }
```

You don't have to call your type `Model` it can be anything.

## Defining Actions and the Update function
As the user is interacting with the component, a new "state" of the model will be calculated. For example, `selectedChannel` will start at `-1` and as we press the arrow keys up and down it will change to 0, 1, 2 etc.. Elm creates that new model using the `update` function. Our update function will take an `action` and return a new version of the model with a small modification.

This makes it very convenient since you can look at the action type definition (see below) and immediately know what kind of transformations can happen to the model in this component.

```elm
-- UPDATE
type Action = NoOp | Filter String | Select Int

update action model =
  case action of
    NoOp ->
      model

    Filter query ->
      { model | query = query }

    Select index ->
      { model | selectedChannel = index }
```

The `Action` type we defined is a [Union Type](http://elm-lang.org/docs/syntax#union-types) which allows us to perform [Pattern Matching](https://en.wikipedia.org/wiki/Pattern_matching) in the `update` function with the `case` statement.

The `Filter String` part is basically a [Tagged Union](https://en.wikipedia.org/wiki/Tagged_union) where `Filter` is the action tag with a `String` argument. This helps us differentiate it from other actions that may have a one string argument.

## Putting it all together with StartApp
It's time to put it all together using StartApp.
StartApp lets us declare which methods correspond with our model, update and view parts of our component.

Replace the `main` function with the following:

```elm
main : Signal Html
main =
  StartApp.start
    { model = initialModel
    , update = update
    , view = view
    }
```

If you try and compile the code so far you will get the following message:

```
==================================== ERRORS ====================================

-- TYPE MISMATCH ------------------------------------------- ChannelSwitcher.elm

The argument to function `start` is causing a mismatch.

51│   StartApp.start
52│>    { model = initialModel
53│>    , update = update
54│>    , view = view
55│>    }

Function `start` is expecting the argument to be:

    { ..., view : Signal.Address Action -> Model -> Html }

But it is:

    { ..., view : VirtualDom.Node }

Detected errors in 1 module.
```

That's because StartApp is passing a `Mailbox` address and the currently computed model to the view. Don't worry about understanding Mailboxes just yet, we will cover those in the second part of the tutorial. For now, to fix this error let's refactor our `view` function signature to the following, and add a type annotation while we are at it:

```elm
view : Signal.Address Action -> Model -> Html
view address model =
```

Now you should be able to compile but as you notice when you open `index.html` the component still does not filter the list. Next we will render the model and implement the search/filter functionality.

### Rendering the model
Let's render the model now instead of static data. Under the VIEW section we will add a new method that renders the `li` elements using the `channels` list on the model.

```elm
-- VIEW
renderChannel : String -> Html
renderChannel name =
  li [ class "collection-item" ] [ text <| "#" ++ name ]

renderChannels : List String -> Html
renderChannels channels =
  let
    channelItems = List.map renderChannel channels
  in
    ul [ class "collection" ] channelItems

view : Signal.Address Action -> Model -> Html
view address model =
  div [ class "card-panel" ] [
    input [ ] [],
    renderChannels model.channels
  ]
```

We created two new methods `renderChannel`, which renders an individual `li` representing a channel with the hash symbol (#), and `renderChannels` which uses a `List.map` to return a list of `li` elements. We then pass that list as the second argument of `ul`. Lastly, we call `renderChannels` from the `view` function, passing in the `model.channels`.

*Note: If you are wondering about the `<|` operator: it is a reverse pipe and it means the result of everything on the right of that operator (until the closure) will be piped into the function to the left of the operator. It's just a way to avoid parens.*

## Filtering the list
We are displaying the list rendered directly from the model, now it's time to filter the view according to the user input in the search box.

First we need to store the filter the user types in on the model. For that we will add an `onInput` event on the input box, so we can filter the list as the user is typing.

```elm
view : Signal.Address Action -> Model -> Html
view address model =
  div [ class "card-panel" ] [
    input [ onInput address Filter ] [],
    renderChannels model.channels
  ]
```

The Filter action tag provides us with a free "constructor" that takes in a string, that string will be passed in to onInput from the browser as `event.target.value` or in elm-html `targetValue`.

*Note: `onInput` is not yet part of the `elm-html` package, which is why we imported the `Html.Events.Extra` package which comes from [circuithub/elm-html-extra](https://github.com/circuithub/elm-html-extra)*

Then we will use the `List.filter` method to only display channels starting with the input text.

```elm
filterChannels : List String -> String -> List String
filterChannels channels query =
  List.filter (String.contains query) channels
```

Then use this function in the `view` function:

```elm
view : Signal.Address Action -> Model -> Html
view address model =
  div [ class "card-panel" ] [
    input [ onInput address Filter ] [],
    renderChannels (filterChannels model.channels model.query)
  ]
```

To make sure that the filter is case insensitive we will need to refactor the `filterChannels` and pass both the query and list item to `String.toLower`.

```elm
filterChannels : List String -> String -> List String
filterChannels channels query =
  let
    containsCaseInsensitive str1 str2 =
      String.contains (String.toLower str1) (String.toLower str2)
  in
    List.filter (containsCaseInsensitive query) channels
```

## Adding style
For styling the component we will create a new HTML file and include the [Materialize](https://github.com/Dogfalo/materialize) CSS library.

This is what your HTML should look like:

```html
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.5/css/materialize.min.css">
    <script src="channel_switcher.js"></script>
  </head>
  <body>
    <div id="elm-goes-here" class="container"></div>
    <script>
      Elm.embed(
          Elm.ChannelSwitcher,
          document.getElementById('elm-goes-here')
      );
    </script>
  </body>
 </html>
```

To compile the Elm file into `channel_switcher.js` use the `--output` flag:

```bash
elm make ChannelSwitcher.elm --output channel_switcher.js
```

When you open the HTML file you should see something like this:

![Final](https://www.dropbox.com/s/jcq1a3k1kitfgpd/Screenshot%202016-01-05%2008.46.21.png?dl=1)


And here is our Elm code so far:

```elm
module ChannelSwitcher where

-- IMPORTS
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import Html.Events.Extra exposing (..)
import String
import StartApp.Simple as StartApp

-- MODEL
type alias Model =
  { channels: List String
  , selectedChannel: Int
  , query: String
  }

initialModel : Model
initialModel =
  { channels = ["Elm", "React.js", "Ember", "Angular 2", "Om", "OffTopic" ]
  , selectedChannel = -1
  , query = ""
  }

-- UPDATE
type Action = NoOp | Filter String | Select Int

update action model =
  case action of
    NoOp ->
      model

    Filter query ->
      { model | query = query }

    Select index ->
      { model | selectedChannel = index }


-- VIEW
filterChannels : List String -> String -> List String
filterChannels channels query =
  let
    containsCaseInsensitive str1 str2 =
      String.contains (String.toLower str1) (String.toLower str2)
  in
    List.filter (containsCaseInsensitive query) channels

renderChannel : String -> Html
renderChannel name =
  li [ class "collection-item" ] [ text <| "#" ++ name ]

renderChannels : List String -> Html
renderChannels channels =
  let
    channelItems = List.map renderChannel channels
  in
    ul [ class "collection" ] channelItems

view : Signal.Address Action -> Model -> Html
view address model =
  div [ class "card-panel" ] [
    input [ onInput address Filter ] [],
    renderChannels (filterChannels model.channels model.query)
  ]

main : Signal Html
main =
  StartApp.start
    { model = initialModel
    , update = update
    , view = view
    }
```


# What's Next?
In the next post I will deconstruct some of the magic behind StartApp and show you how to build a version of this component with Signals, Mailboxes and Ports. This will allow us to add keyboard interaction and JavaScript interop.
