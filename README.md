# ReContainers
[![Build Status](https://travis-ci.org/Astrocoders/ReContainers.svg?branch=master)](https://travis-ci.org/Astrocoders/ReContainers)

Type safe high order components for ReasonReact

```
$ yarn add re-containers
```

## Inspired by 

Inspired by [recompose](https://github.com/acdlite/recompose/), [react-powerplug](https://github.com/renatorib/react-powerplug) and [smalldots](https://github.com/smalldots)

# Containers

Available API

## WithState

```ocaml
module WithState = ReContainers.WithState.Make({
  type state = int;
});

<div>
  <WithState initialState=0>
    ...(({ self, send }) => {
      <div>
        <p>ReasonReact.string("Count is " ++ string_of_int(self.state))</p>
        <button onClick=((_) => send(Set(self.state + 1)))>(ReasonReact.string("+"))</button>
        <button onClick=((_) => send(Set(self.state - 1)))>(ReasonReact.string("+"))</button>
      </div>
    })
  </WithState>
</div>
```

## Component
Nice for lifecycle helpers

```ocaml
<Component didMount willUnmount willUpdate>
  ...((self) => {
    <div />
  })
</Component>
```

## Loader

Handling promises

```ocaml
module ReLoader = ReContainers.Loader.Make({
  /* Promise result */
  type t = int;
});
```


```ocaml
/* Could be a fetch for instance */
let makeTimeout = () =>
  Js.Promise.make((~resolve, ~reject as _) =>
    Js.Global.setTimeout(() => resolve(. 0), 2000) |> ignore
  );

<ReLoader>
  ...(
       ({state, load}) =>
         <div className="App">
           <div className="App-header">
             <img src=logo className="App-logo" alt="logo" />
             <h2> (ReasonReact.string(message)) </h2>
             <button onClick=(_e => load(makeTimeout()))>
               (ReasonReact.string("Click me"))
             </button>
           </div>
           (
             switch (state) {
             | Loading =>
               <p> (ReasonReact.string("Hang a sec")) </p>
             | Error(string) => <p> (ReasonReact.string(string)) </p>
             | Success(_promiseResult) => <p> (ReasonReact.string("All good")) </p>
             | Empty => ReasonReact.null
             }
           )
         </div>
     )
</ReLoader>
```

## ReList
It manages the state of lists for you

Make
```ocaml
module ReList = ReContainers.ReList.Make({
  type t = { name: string, age: int };
});
```

Usage

```ocaml
<ReList initial=[{ name: "Dio Brando", age: 123 }]>
  ...(({ list, pull, push }) => (
    <Wrapper>
      <CharFormInput onSubmit=(({ values }) => push(values)) />

      (
        list
        |> List.map(todo => (
          <CharItem onDelete=pull(({ age, name }) => char.name == name && char.age == age)>
            (ReasonReact.string(char.name))
          </CharItem>
        ))
        |> Array.of_list
        |> ReasonReact.array
      )
    </Wrapper>
  ))
</ReList>
```
