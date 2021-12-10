# <p><img style="vertical-align:middle;" align="left" alt="logo" src="https://user-images.githubusercontent.com/79456243/145360178-12110388-142d-45e0-9903-95463bb46038.png"> react-datanomy</p>

### Helper library for organize you state management, using native React.js features

This is most clear way for manage state in React.js, with unnecessary of writing extra excess code, allowing to keep you attention on what you do, instead how

#### Preferencies:

1. Super small: 870 bytes ungzipped!
2. Support both, class and functional components
3. Provide uniform bus, used universally throught all you components tree
4. Without side dependencies - only React enough
5. Have no complex code constructions - very ease to use

## Installation:

```
npm i react-datanomy
```
or
```
yarn add react-datanomy
```
## API:

Datanomy receive **initialState**, **reducers** and optionally **scenarios** and returns array with **StoreProvider**, **StoreHook** and **StoreContext**. **StoreProvider** supply universal structure, named **SAS Bus**, and then **StoreHook**, **StoreContext.Consumer**, or **contextType** via **StoreContext**, consume it in the next unified form:

```js
[currentState, memoizedActions, memoizedScripts]
```

**SAS Bus** (**SAS** from [**S**tate, **A**ctions, **S**cripts]) acts as data bus in the electronic, or like building infrastructere, where independend from architecture complexity, each room can be connected to electricity, water, internet, security system, gas, etc. - to the any network, which independend from others and also paved through whole building. According to that analogy, React context play a cable duct role.

**initialState** is a starting store state.

**reducers** is a hash of clear functions, indexed by actions names, which receives one or two arguments: **currentState** and optional **payload** and returns **newState**.

**scenarios** is a function, which receive **getState** method and **actions** in arguments and returns a hash with **scripts**.

**getState** is a function, which allways return cureent actual state inside **scripts**

**currentState** is an current actual state, returned from useReducer inside **Datanomy**.

**acrions** is a memoized hash of methods, which formally wrappers for **dispatch**, returned from useReducer, called with action name in `type` field and optional **payload** from **action** argument.

**scripts** is a  memoized hash of methods, which can receives optional **payload** and returns nothing. They are can be regular, or async functions, and contains a logic of any complexity, which allways have an access to the current actual state by using **getState**, and call any **actions**, which are closured from **scenarios** method scope.

**StoreProvider** is component, which wrap React Context.Provider and supply SAS Bus to his value argument

**StoreHook** is a wrapper for a regular React useContext, with **StoreContext** as argument

**StoreContext** is a regular React Context, created using createContext, called with **initialState** as argument

## Usage:

### 1. Create you store module:

`file: src/stores/counterStore.js`
```js
// Import createDatanomy from react-datanomy:
import { createDatanomy } from 'react-datanomy'

// Declare you initial state:
const initialState = {
  counter: 0,
  someData: [],
}

// Declare state reducers:
const reducers = {
  increment: (state) => ({ ...state, counter: state.counter + 1 }),
  decrement: (state) => ({ ...state, counter: state.counter - 1 }),
  add: (state, payload) => ({ ...state, counter: state.counter + payload }),
  sub: (state, payload) => ({ ...state, counter: state.counter - payload }),
  reset: (state) => ({ ...state, counter: 0 }),
}

// Optionally declare some complex scenarios:
const scenarios = (getState, actions) => {
  derivedAdd: async (payload) => {
    console.log(getState().counter) // for example 10
    await new Promise((resolve) => {
      setTimeout(() => resolve(acttions.add(payload)), 100)
    })
    console.log(getState().counter) // 10 + payload
  },
}

// Call createDatanomy with above declarations and export it retuns:
export const [ 
  CounterProvider, 
  useCounter,
  CounterContext
] = createDatanomy(initialState, reducers, scenarios)
```

#### Same, using typescript:
```tsx
// Additionally import Treducers and TScenarios types:
import { TReducers, TScenarios, createDatanomy } from 'react-datanomy';
 
// Declare you store interface
interface TCounterStore = {
  counter: number;
  someData: Array<any>;
};

// Type initial state using you store interface
const initialState: TCounterStore = {
  counter: 0,
  someData: [],
};

// Type reducers, using TReducers with store interface as generic argument
const reducers: TReducers<TCounterStore> = {
  increment: (state) => ({ ...state, counter: state.counter + 1 }),
  decrement: (state) => ({ ...state, counter: state.counter - 1 }),
  add: (state, payload) => ({ ...state, counter: state.counter + payload }),
  sub: (state, payload) => ({ ...state, counter: state.counter - payload }),
  reset: (state) => ({ ...state, counter: 0 }),
};

// Type scenarios, using TScenarios with store interface as generic argument
const scenarios: TScenarios<TCounterStore> = (getState, actions) => {
  derivedAdd: async (payload) => {
    console.log(getState().counter) // for example 10
    await new Promise((resolve) => {
      setTimeout(() => resolve(acttions.add(payload)), 100)
    })
    console.log(getState().counter) // 10 + payload
  },
};

export const [ 
  CounterProvider, 
  useCounter,
  CounterContext
// Probide store interface as generic argument to the createDatanomy
] = createDatanomy<TCounterStore>(initialState, reducers, scenarios)
```

### 2. Import and connect You store provider to the DOM branch, or whole app
`file: src/App.jsx`
```js
import { CounterProvider } from './store/counterStore'
import HookComponent from './components/HookComponent'
import ConsumerComponent from './components/ConsumerComponent'
import ClassComponent from './components/ClassComponent'
import './App.css';

function App() {
  return (
    <CounterProvider>
      <div className="App">
        <HookComponent/>
        <ConsumerComponent/>
        <ClassComponent/>
      </div>
    </CounterProvider>
  );
}

export default App;
```
### 3. Connect to the Store using hook in functional component:
`file: src/components/HookComponent.jsx`
```js
import { useCounter } from '../store/counterStore'

function HookComponent() {
  const [{
    counter
  }, {
    increment, decrement
  }, {
    derivedAdd
  }] = useCounter()
  return (
    <div>
      {/* TODO: Complete readme*/}
    </div>
  );
}

export default HookComponent;
```
// TODO: Complete readme
