+++
title="Refactoring with Applicative - a small example in Haskell."
date=2018-05-27
draft=false
[taxonomies]
tags=["blog", "haskell", "refactoring"]
+++

Recently I came across a neat little refactoring example that I thought
might prove useful, particularly to those starting out with Haskell.

#### Setting the scene

Say you have a file of comma-separated data, where each row is meant
to represent the length, breadth and height of a box. Our task is to calculate
the volume of each box. Simple, right? Well, like in real life, the data is not
complete and sometimes values are missing. We want to handle these cases by
simply not calculating a volume if any of the dimensions of the box are missing.

    l,b,h (inputs)      v (our output)
    ---------------------------------
    4,5,1               20
    1,,                 -
    3,4,                -
    1,2,4               8


### Maybe volume

We'll work towards a function to calculate volume - something with a signature like this:

```haskell
maybeVolume :: Maybe Length -> Maybe Breadth -> Maybe Height -> Maybe Volume
```

To illustrate the patterns at play in this example let's start with something a bit simpler.
Let's work with Area before we get to Volume.

A great place to start is with some data types.

```haskell
newtype Length =
  Length {
    getLength :: Int
  } deriving (Eq, Show)
```
```haskell
newtype Width =
  Width {
    getWidth :: Int
  } deriving (Eq, Show)
```
```haskell
newtype Area =
  Area {
    getArea :: Int
  } deriving (Eq, Show)
```

So with these types our pure function for `area` is:

```haskell
area :: Length -> Width -> Area
area l w =
  Area $ (getLength l) * (getWidth w)
```

Working towards the problem at hand, let's extend this to
a function that handles missing values. [`Maybe`](https://wiki.haskell.org/Maybe)
encodes the concept that a value may be missing. Following a simple pattern
matching approach gets us:

```haskell
marea :: Maybe Length -> Maybe Width -> Maybe Area
marea ml mw =
  case ml of
    Nothing ->
      Nothing
    Just l ->
      case mw of
        Nothing ->
          Nothing
        Just w ->
          Just $ area l w
```

This is fine but it is a little verbose and is ripe for some simplification.

Let's use the fact that `Maybe` is a monad. The bind operation passes through Just,
while Nothing will force the result to always be Nothing. Using `do` notation we
can rewrite `marea` like so:

```haskell
marea' :: Maybe Length -> Maybe Width -> Maybe Area
marea' ml mw = do
  l <- ml
  w <- mw
  pure $ area l w
```

The shape of `marea'` is exactly the motivating example for introducing applicative in
McBride and Patterson's, [Applicative Programming with Effects](https://www.staff.city.ac.uk/~ross/papers/Applicative.html).
Using applicative notation gets us to a one-liner.

```haskell
marea'' :: Maybe Length -> Maybe Width -> Maybe Area
marea'' ml mw =
  area <$> ml <*> mw
```

Armed with these examples, let us return to `maybeVolume.` We will need a couple
of extra data types.

```haskell
newtype Breadth =
  Breadth {
    getBreadth :: Int
  } deriving (Eq, Show)
```
```haskell
newtype Volume =
  Volume {
    getVolume :: Int
  } deriving (Eq, Show)
```

And then our pure function for `volume` is simply:
```haskell
volume :: Length -> Width -> Breadth -> Volume
volume l w b =
  Volume $ (getLength l) * (getWidth w) * (getBreadth b)
```

Skipping over the simplest version of the function (that uses pattern matching),
here are the monadic and applicative versions of `maybeVolume`

```haskell
-- monadic approach
mvolume :: Maybe Length -> Maybe Width -> Maybe Breadth -> Maybe Volume
mvolume ml mw mb = do
   l <- ml
   w <- mw
   b <- mb
   pure $ volume l w b

-- and with applicative
mvolume' :: Maybe Length -> Maybe Width -> Maybe Breadth -> Maybe Volume
mvolume' ml mw mb =
    volume <$> ml <*> mw <*> mb
```

To wrap up, we've seen how to take perfectly good code that uses
pattern matching, to code that uses idiomatic Haskell patterns like
monadic `do` notation, and composable applicative notation. It's routine
to run into monads and applicatives in Haskell codebases and I found
simple examples like this one to help concretely connect with them.

Highly recommend the McBride and Patterson [paper](https://www.staff.city.ac.uk/~ross/papers/Applicative.html)
on applicatives by the way. Same goes for pretty much anything written by Conor McBride.

