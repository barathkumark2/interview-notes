# Important for Hooks:
1. Hooks should always be declared at the top level.
2. Hooks should not be in a function or a condition.
3. Hooks should not be async in nature. Use useEffect,
make that all within & then make it.

## Hooks codes:

1. Counter using Use State:
```typescript
App.tsx
import './App.css'
import { Child } from './components/Child'

function App() {
  return (
    <>
      App Component
      <Child />
    </>
  )
}

export default App
```
Child.tsx
```typescript
import { useState } from "react"

export const Child = () => {
    const [count, setCount] = useState<number>(0);
    return(
        <div>
            <h3>Child Component</h3>
            <button onClick={() => setCount(prevState => prevState + 1)}>Increment</button>
            <h3>{count}</h3>
        </div>
    )
}
```
-----------------------
1a. Get formdata using uncontrolled component:

```typescript
export const Child = () => {

    const formHandler = (e)=> {
        e.preventDefault();
        console.log(e.target)
        const formData = new FormData(e.target)
        console.log(formData.get("name"))
        console.log(formData.get("username"))
    }

    return(
        <div>
            <form onSubmit= {formHandler}>
                <div>
                    <label htmlFor="name">Name</label>
                    <input type="text" name="name" placeholder = "Enter Name" />
                </div>

                <div>
                    <label htmlFor="username">Username</label>
                    <input type="text" name="username" placeholder = "Enter Username" />
                </div>
                <button>Submit</button>
            </form>
        </div>
    )
}
```
-----------------------
2. Use reducer for incremrent and decrement

Improving the useState with useReducer:
```typescript
import { useState } from "react"

export const Child = () => {
    const [count, setCount] = useState<number>(0);
    return(
        <div>
            <h3>Child Component</h3>
            <button onClick={() => setCount(prevState => prevState + 1)}>Increment</button>
            <button onClick={() => setCount(prevState => prevState - 1)}>Decrement</button>
            <h3>{count}</h3>
        </div>
    )
}

```
Complete code:
```typescript
App.tsx:
import './App.css'
import { Child } from './components/Child'

function App() {
  return (
    <>
      App Component
      <Child />
    </>
  )
}

export default App

```
Child.tsx:
```typescript
import { useReducer, type Reducer } from "react";

type Action = 
    | {type: "increment"} 
    | {type: "decrement"}

type State = {
    count: number;
}

export const UseComponent: React.FC = () => {

    const reducerFunction: Reducer<State, Action> = (state, action) => {
      switch (action.type) {
        case "increment":
          return {...state, count : state.count + 1};
        case "decrement":
          return {...state, count: state.count - 1};
        default:
          return state;
      }
    };

    const intialState: State = { count: 0 };
    const [state, dispatch] = useReducer(reducerFunction, intialState);
    return (
      <div>
        <h3>Use Component</h3>
        <button onClick={() => dispatch({ type: "increment" })}>
          Increment
        </button>
        <button onClick={() => dispatch({ type: "decrement" })}>
          Decrement
        </button>
        <h3>{state.count}</h3>
      </div>
    );
}
```
----------------------------------
3. UseEffect to fetch the data using fetch:
```typescript
import { useState } from "react"

export const Child = () => {

    const [list, setList] = useState([]);
    const url = "https://jsonplaceholder.typicode.com/todos";
    useState(() => {
        fetch(url).then(data => {
            return data.json();
        }).then(response => {
            return setList(response);
        }).catch(error => {
            console.log("Error experienced");
        })
    }, [])
   
    return(
        <div>
            <h3>Child Component</h3>
            {list && list.map(item => <li key={item.id}>{item.title}</li>)}
        </div>
    )
}
```
-------------------------
3.a. using async await:
```typescript
import { useState } from "react"

export const Child = () => {

    const [list, setList] = useState([]);
    const url = "https://jsonplaceholder.typicode.com/todos";
    useState(() => {
        const fetchData = async() => {
            const data = await fetch(url);
            const parsedData = await data.json();
            setList(parsedData);
        }

        fetchData();

    }, [])
   
    return(
        <div>
            <h3>Child Component</h3>
            {list && list.map(item => <li key={item.id}>{item.title}</li>)}
        </div>
    )
}
```
-------------
3. b. using Custom Hooks for fetching:

Child.tsx:
```typescript
import { useState } from "react"
import { useFetch } from "../hooks/useFetch";

export const Child = () => {
    const url = "https://jsonplaceholder.typicode.com/todos";
    const list = useFetch(url);
    return(
        <div>
            <h3>Child Component</h3>
            {list && list.map(item => <li key={item.id}>{item.title}</li>)}
        </div>
    )
}
```
useFetch.tsx:
```typescript
import { useEffect, useState } from "react";

export const useFetch = (url: string) => {
    const [list, setList] = useState([]);
    useEffect(() => {
        fetch(url).then(data => {
            return data.json();
        }).then(response => {
            return setList(response);
        }).catch(error => {
            console.log("Error experienced");
        })
    }, [url])
    return list;
}
```
Same useFetch.tsx using async await:
```typescript
import { useEffect, useState } from "react";

export const useFetch = (url: string) => {
    const [list, setList] = useState([]);
    useEffect(() => {
        const fetchData = async () => {
            const data = await fetch(url);
            const parsedData = await data.json();
            setList(parsedData);
        }
        fetchData();
    }, [url])
    return list;
}
```
-------------------
4. useRef to focus on the input element:
```typescript
import { useRef } from "react"

export const Child: React.FC = () => {
   
    const inputRef = useRef<HTMLInputElement | null>(null);

    const onClickHandler = () => {
        inputRef.current.focus();
    }

    return(
        <div>
            <h3>Child Component</h3>
            <input type="text" ref = {inputRef} />
            <button onClick={onClickHandler}>Focus</button>
        </div>
    )
}
```
---------------
5. Todo list with the key down to add the note to the list:
```typescript
import { useState, type ChangeEvent } from "react"

export const Child = () => {
    const [item, setItem] = useState<string>("");
    const [list, setList] = useState<string[]>([]);

    const addNewNote = (e: React.MouseEvent<HTMLButtonElement>) => {
        e.preventDefault();
        setList([...list, item]);
        setItem("");
    }

    const deleteNote = (item) => {
        const filteredNote = list.filter(l => l != item);
        setList(filteredNote);
    }

    return(

        <div>
            <h3>Child Component</h3>
            <input type="text" placeholder="Enter note" value={item} onChange={(e) => setItem(e.target.value)}
                onKeyDown={(e) => e.key == "Enter" && addNewNote(e)}
            />
            <button onClick={addNewNote}>Add</button>



            <h3>Notes</h3>
            {
                list.map(item => {
                    return (
                        <ul key={item}>
                            <li>{item} <button onClick={() => deleteNote(item)}>Delete</button></li>
                           
                        </ul>
                    )
                })
            }
        </div>
    )
}
```
-----------------
6. useContext to implement and show toggle theme:

Context.tsx
```typescript
import { Children, createContext, useState } from "react";

export const Context = createContext(null);

export const ContextProvider = ({children}) => {
    const [theme, setTheme] = useState("light");
    return (
        <Context.Provider value={{theme, setTheme}}>
          {children}
        </Context.Provider>  
    )
}
```
main.tsx
```typescript
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.tsx'
import { ContextProvider } from './Context.tsx'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <ContextProvider>
      < App />
    </ContextProvider>
  </StrictMode>,
)

```
Child.tsx
```typescript
import { useContext } from "react"
import { Context } from "../Context"

export const Child = () => {
    const {theme, setTheme} = useContext(Context);

    return(
        <div>
            <h3>Child Component - Theme - {theme}</h3>
            <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>Toggle</button>
        </div>
    )
}
```
-------------

7. React.memo to memorize the entire functional component:

Child.tsx:
```typescript
import { useState } from "react"
import { GrandChild } from "./Grandchild"

export const Child = () => {
    console.log("parent rendered")
    const [data, setData] = useState("");
    return(
        <div>
            <h3>Child Component</h3>
            <input type="text" placeholder="Enter text" value={data} onChange={(e) => setData(e.target.value)}/>
            <GrandChild name= {"barath"}/>
        </div>
    )
}
```
GrandChild.tsx:
```typescript
import React from "react";
export const GrandChild = React.memo(({name}) => {
    console.log("Child rendered")
    console.log(name)
    return(
        <div>
            Grand Child - {name}
        </div>
    )
})
```
-----------------
8. useMemo:

Child.tsx:
```typescript
import { useState, useMemo } from "react"

export const Child = () => {
    const [data, setData] = useState("");
    const [count, setCount] = useState(0);
   
    const onChangeHandler = (e) => {
        console.log("onchange handler")
        setData(e.target.value);
    }

    const onClickHandler = () => {
        console.log("onClick handler")
        setCount(prevState => prevState + 1)
    }

    const calculation = useMemo(() => {
        console.log("recalculating");

        const value = Number(data) * Number(data) * Number(data);
        return value
    }, [data])
   
    return(
        <div>
            <h3>Child Component</h3>
            <input type="text" placeholder="Enter text" value={data} onChange={onChangeHandler}/>
            <h4>{data}</h4>
            <button onClick={onClickHandler}>Count</button>
            <h4>{count}</h4>
            <h3>{calculation}</h3>
        </div>
    )
}
```

other example:
```typescript
import { useState, useMemo } from "react"

const list = ["apple", "mango", "orange"];

export const Child = () => {
    const [note, setNote] = useState("");
    const [count, setCount] = useState(0);

    const filteredData = useMemo(() => list.filter(item => {
        console.log("rerendering...")
        return item.includes(note)
    }), [note])

    return(
        <div>
            <h3>Child Component</h3>
            <div>
                <input type="text" placeholder="Enter text" value={note} onChange={(e) => setNote(e.target.value)} />
                {filteredData.map(item => <ul key={item}><li>{item}</li></ul>)}
            </div>

            <div>
                <button onClick={() => setCount(prev => prev + 1)}>Count</button>
                <h3>{count}</h3>
            </div>
        </div>
    )
}
```
-------------
9. useCallback
```typescript
import { useCallback, useEffect, useState } from "react"

export const Child = ( ) => {
    const [productId, setProductId] = useState(1);
    const [oriPrice, setOriPrice] = useState(100);
    const [disPrice, setDisPrice] = useState(0);
    const [inputData, setInputData] = useState("");

    const values = {
        1: 100,
        2: 200,
        3: 300
    }
    useEffect(() => {
        setOriPrice(values[productId]);
    }, [productId]);

    const discountCalculator = useCallback(() => {
        console.log("Rerendering...")
        let discount = 0;
        if(productId === 1) {
            discount = 0.1;
        } else if(productId === 2) {
            discount = 0.2;
        }else if(productId === 3) {
            discount = 0.3;
        }
        setDisPrice(oriPrice - oriPrice*discount);
    }, [productId])

    useEffect(() => {
        discountCalculator();
    }, [discountCalculator])

    return(
        <div>
            Child Component
            <div>
                <h3>Original Price - {oriPrice}</h3>
                <h3>Discount Price - {disPrice}</h3>
                <button onClick={() => setProductId(1)}>Product 1</button>
                <button onClick={() => setProductId(2)}>Product 2</button>
                <button onClick={() => setProductId(3)}>Product 3</button>
            </div>
            <div>
                <label htmlFor="input-text">input</label>
                <input type="text" id="input-text" placeholder="Enter text" value={inputData} onChange={(e) => setInputData(e.target.value)}/>
            </div>
        </div>
    )
}
```
------------
Exercises:
Validate phone number:

```typescript
import {useState} from "react";

export const UseComponent: React.FC = () => {
    const [number , setNumber] = useState("");
    const [error, setError] = useState("Invalid"); 
    const regex = /^((\+91?)|\+)?[7-9][0-9]{9}$/;

    const validateNumber = (e: React.ChangeEvent<HTMLInputElement>) => {
        const value = e.target.value;
        setNumber(value);
        if(regex.test(value)) {
            console.log("valid");
            setError("");
        } else {
            console.log("invalid");
            setError("Invalid")
        }
    }

    const handlePaste = (e: React.ClipboardEvent<HTMLInputElement>) => {
         e.preventDefault();
        const value = e.clipboardData.getData("text");
        if (regex.test(value)) {
          console.log("valid");
          setNumber(value)
          setError("");
        } else {
          console.log("invalid");
          setNumber("")
          setError("Invalid");
        }
    }

    return (
      <div>
        <h3>Use Component</h3>
        <form>
          <h4>Validate Number</h4>
          <input type="text" value={number} onChange={validateNumber} 
            onPaste = {handlePaste}/>
          {error == "Invalid" ? <p>Invalid mobile number</p>: <p>valid mobile number</p>}
        </form>
      </div>
    );
}

```


------------
10. redux using toolkit:

store.tsx
```typescript
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./slices/counterSlice";

export const store = configureStore({
    devTools: true,
    reducer: {
        counter: counterReducer
    }
})
```
Child.tsx:
```typescript
import { useDispatch, useSelector } from "react-redux"
import { decrement, increment } from "../slices/counterSlice";

export const Child = ( ) => {
    const value = useSelector(state => state.counter);
    const dispatch = useDispatch();
    console.log(value);
    return(

        <div>
            <h3>Child Component</h3>
            <button onClick={() => dispatch(increment())}>Increment</button>
            <button onClick={() => dispatch(decrement())}>Decrement</button>
            <h4>{value}</h4>
        </div>
    )
}
```
main.tsx:
```typescript
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.tsx'
import { Provider } from 'react-redux'
import { store } from './store.tsx'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <Provider store={store}>
      < App />
    </Provider>
  </StrictMode>,
)

```
counterSlice.tsx:
```typescript
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
    name: "counter",
    initialState: 0,
    reducers: {
        increment(state, action) {
            return state = state + 1;
        },
        decrement(state, action) {
            return state = state - 1;
        }
    }
})

export const {increment, decrement} = counterSlice.actions;
export default counterSlice.reducer;
```
--------------------
11. using createAsyncThunk using reduxtoolkit(RTK)

counterSlice.tsx:
```typescript
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

export const fetchTodos = createAsyncThunk(
    "todos/fetchTodos",
    async () => {        
        const response = await fetch("https://jsonplaceholder.typicode.com/todos");
        return response.json();
    }
)

const counterSlice = createSlice({
    name: "todos",
    initialState: {
        data : [],
        loading: false,
        error: null
    },
    reducers: {},
    extraReducers :(builder) => {
        builder.addCase(fetchTodos.pending, (state) => {
            state.loading = true;
        }).addCase(fetchTodos.fulfilled, (state, action) => {
            state.loading = false;
            state.data = action.payload;
        }).addCase(fetchTodos.rejected, (state, action) => {
            state.loading = false;
            state.error = action.error.message;
        })
    },
})

export default counterSlice.reducer;
```
Child.tsx:
```typescript
import { useDispatch, useSelector } from "react-redux"
import { fetchTodos } from "../slices/counterSlice";
import { useEffect } from "react";

export const Child = ( ) => {
    // const {data, error, loading} = useSelector(state => state.todos);
    const {data, error, loading} = useSelector(state => state.todos);
    const dispatch = useDispatch();
    useEffect(() => {
        dispatch(fetchTodos());
    }, [dispatch])

  if (loading) return <h3>Loading...</h3>;
  if (error) return <h3>Error: {error}</h3>;

  return (
    <div>
      <h3>Users List</h3>
      {data.map((user: any) => (
        <p key={user.id}>{user.title}</p>
      ))}
    </div>
  )
}
```
-----------------
12. Import dynamic elements using lazy.

There is another component called Child, which is getting imported.
```typescript
import {lazy, Suspense} from "react";

export const GrandChild = () => {
      const Child = lazy(() => import("./Child"));
      return (
            <div>
                  Grand Child
                  <Suspense fallback = {<p>Loading...</p>}>
                        <Child />
                  </Suspense>
            </div>
      )
}
```
Note:
When trying to import a named component, use
const Child = lazy(() => import("./Child").then(module => ({default : module.Child})))

----------------
13. Use Action State:
```typescript
import {useActionState} from "react";

export const Child = () => {

    const formHandlerFunction = async (prevState, formdata) => {
        await new Promise((res, rej) => setTimeout(res, 2000))
        if(formdata.get("username") !== "" ) {
            if(prevState == "Form Submit Success") {
                return "You already logged in"
            }
            return "Form Submit Success"
        }
        return "Form submit Failed"
    }

    const [state, formAction, isLoading] = useActionState(formHandlerFunction, "Fill the form");


    return(
        <form action = {formAction}>
            <div style = {{display: "flex", flexDirection : "column", textAlign: "center"}}>
                <label htmlFor = "">
                    Username : <input type = "text" name = "username"/>
                </label>
                <label htmlFor = "">
                    Password : <input type = "text" name ="password"/>
                </label>
                <button type = "submit">Submit</button>
                {isLoading? "Loading" : state}
            </div>
        </form>
    )
}

```
---------
14. use form status:
```typescript
import {useFormStatus} from "react-dom";


const SubmitButton = () => {
    const {pending} = useFormStatus();
    return  <button type = "submit">{pending ? "Submitting" : "Submitted"}</button>
}


export const Child = () => {


    const formAction = async () => {
        await new Promise((res, rej) => setTimeout(res, 2000))
        console.log("Form Submitted");
    }

    return(
        <form action = {formAction}>
            <div style = {{display: "flex", flexDirection : "column", textAlign: "center"}}>
                <label htmlFor = "">
                    Username : <input type = "text" name = "username"/>
                </label>
                <label htmlFor = "">
                    Password : <input type = "text" name ="password"/>
                </label>
                <SubmitButton />
            </div>
        </form>
    )
}
```
----------
15. Most frequently used React Libraries:
    1. React hook forms - For many form handling, wizard kinds
    2. Recharts - For charts
    3. Big Calander - For calender utilities
    4. React-beautiful-dnd - Drag and drop for list
    5. Tanstack table - for tables
    6. React joyride - Create guided tours
    7. React advanced cropper  - For image cropping
    8. React colorful - Tiny color picker component
    9. React spring - animation library
    10 ts articles - creating particles animation like crackers,, mouse click particles
    11. React popper - Like giving hint
    12. React PDF viewer
    13. React-i18next - For localisation of webpages
    14. React icons
    15. React audio p;ayer
    16. React image slider - images scroll

---------------
16. debounce implementation:
```typescript
import { useEffect, useState } from "react";

export function useDebounce(value: string, delay: number) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebounced(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debounced;
}
```
-----------------
17. Flatten array:
```typescript
export const Search = () => {

    const complexArray = [
        1,
        [2, 3],
        [4, [5, 6, [7, 8]]],
        [ , 9, 10], // Contains an empty/sparse slot
        [[[11, 12, [13, [14, 15]]]]]
    ];

    const flatArray = (complexArray: any): any[] => {
        return complexArray.reduce((acc: number[], current: number[] | number)=> Array.isArray(current) ? acc.concat(flatArray(current)) : acc.concat(current), []);
    }

    return(
        <div>
            <h3>Search Component</h3>
            {flatArray(complexArray)}
        </div>      
    )
}
```
------------
18. useSort custom Hook:

```typescript
import {useState, useEffect} from "react";

export const useSort = (names, order) => {
    const [list, setList] = useState([]);
    useEffect(() => {
        const data = order == 1? [...names].sort() : [...names].sort().reverse();
        console.log(data)
        setList(data)
    }, [order])
    return list;
}
```
------------
19. useFilter with custom hook:
```typescript

import {useEffect, useState} from "react";

export const useFilter = (names: string[], input: string) => {
    const [list, setList] = useState<string[]>([]);
    useEffect(() => {
        const filteredData: string[] = names.filter((item: string) => item.toLowerCase().includes(input.toLowerCase()));
        console.log(filteredData);
        setList(filteredData)
    }, [input])
    return list;
}
```
------------
Interviewer was briefing me the their project
Self introduction with previous project experience
Roles and responsibilities
What have you done the previous which was challenging in that ?
------------------------------------------------------
### 5. Micro frontend architecture - need to explain in details?
Micro frontend architecture is a design pattern where a large web application is broken down into smaller, semi-independent modules or "micro apps" that work together to create a single, cohesive user experience. It applies the principles of microservices to frontend development, allowing different teams to build, test, and deploy specific features (like a shopping cart or user profile) independently without affecting the rest of the application.

Key Concepts and Components
The Shell/Container App: A "parent" application that acts as an entry point. It handles global concerns like user authentication, navigation, and the dynamic loading of various micro frontends into the layout.
Independent Micro Apps: Each module is self-contained with its own UI, business logic, and data models. For example, the Amazon product detail page, cart, and reviews are handled by separate teams as independent applications.Team Autonomy: Cross-functional teams have "end-to-end"
ownership, from the database to the user interface. Each team can even choose its own technology stack—mixing React, Vue, or Angular within the same page if necessary

Common Implementation Patterns
1.Build-Time Composition: Micro frontends are published as separate packages and integrated during the build process.
2.Server-Side Composition: The server decides which micro apps to include and sends the fully rendered HTML to the browser.
3.Client-Side/Runtime Composition: The browser fetches and mounts micro apps dynamically as the user navigates.
A.Module Federation: A popular JavaScript technology (available in Webpack) that allows apps to share code and load modules from each other at runtime.
B.Iframes: A simple way to isolate apps, though it lacks flexibility for passing data.
C.Single-SPA: A "meta-framework" that coordinates the mounting and unmounting of multiple frameworks in one app without page refreshes.

------------------------------------------------------
How to configure multiple versions of node applications in local?
------------------------------------------------------
What is Module federation?
Module Federation is a powerful architectural pattern and a flagship feature of Webpack 5 that allows multiple independently built and deployed JavaScript applications to share code and resources at runtime.

Think of it as "microservices for the frontend." Instead of bundling all your code into one massive file at build time, different parts of your app remain separate and are only pulled together when the user actually needs them in the browser.

How It Works:
The Core Roles Module Federation works by defining two main roles for your applications:
The Host: The "parent" application that requests and consumes modules from other apps.
The Remote: The "independent" application that "exposes" its own components, functions, or pages for others to use.

#### Key Features and Why They Matter:
<pre>
1. Dynamic Loading: Code is fetched from a remote server only when it's required. If you update a "Remote" component (like a header), every "Host" using it gets the update instantly without needing a full rebuild or redeploy.
2. Shared Dependencies: It avoids downloading the same library twice. For example, if both your Host and Remote apps use React, the browser will only download React once and share it between them.
3. Team Autonomy: Different teams can work on different parts of the UI using their preferred release schedules and CI/CD pipelines.
4. Flexible Technology Stack: While often used within a single framework like React or Angular, it can technically allow components from different ecosystems to coexist in a single user experience.
</pre>
------------------------------------------------------
#### How does the web pack compiling logics?

Think of Webpack like a translator and packer for your website’s code. Here is the logic in 4 simple steps:
<pre>
1. The Starting Line (Entry): Webpack looks at your main file (like index.js). It sees every file that the main file "imports" or "needs."
2. The Map (Dependency Graph): It follows those imports to the next files, and the next, until it has a complete map of every single image, CSS file, and script your project needs to run.
3. The Translator (Loaders): Browsers only understand basic HTML, CSS, and JS. If you have "fancy" files (like Sass or TypeScript), Webpack uses Loaders to translate them into plain code the browser can read.
4. The Final Package (Bundling): Webpack takes that massive pile of files, cleans out the unused parts, squashes them together, and spits out one or two small files (the "bundle") that you actually put on your server.In short: It takes a messy folder of 100 files and turns it into 1 tidy, "browser-ready" file.
</pre>
------------------------------------------------------

#### Why we have to follow micro front end in larger applications ? What is the advantage of that?
Key Features and Why They Matter:
<pre>
1.Dynamic Loading: Code is fetched from a remote server only when it's required. If you update a "Remote" component (like a header), every "Host" using it gets the update instantly without needing a full rebuild or redeploy.
2.Shared Dependencies: It avoids downloading the same library twice. For example, if both your Host and Remote apps use React, the browser will only download React once and share it between them.
3.Team Autonomy: Different teams can work on different parts of the UI using their preferred release schedules and CI/CD pipelines.
4.Flexible Technology Stack: While often used within a single framework like React or Angular, it can technically allow components from different ecosystems to coexist in a single user experience.
</pre>
------------------------------------------------------


#### How to pass the data from one MF to another MF?
Passing data between microfrontends depends entirely on whether they are running on the same page simultaneously (same DOM/runtime) or if they are decoupled, cross-page apps where a page reload or route change occurs.

1. When MFs Coexist on the Same Page (Same Runtime)
If your microfrontends are loaded onto the exact same page at the same time, you have a few clean, decoupled options:
A. Custom DOM Events (Recommended for Decoupling)
This is the most standard, framework-agnostic approach. One microfrontend dispatches a native browser event on the window or document object, and the other listens for it.
```typescript
MF A (Sender):
TypeScript
const dataToSend = { userId: "12345", theme: "dark" };
const event = new CustomEvent("mf-data-exchange", { detail: dataToSend });
window.dispatchEvent(event);

MF B (Receiver):
TypeScript
window.addEventListener("mf-data-exchange", (event: Event) => {
  const customEvent = event as CustomEvent;
  console.log("Data received:", customEvent.detail);
});
```

B. Shared State / Pub-Sub Event Bus
If you want something more structured than raw window events, you can create a lightweight utility package (or share a JS object via your orchestrator) that acts as an Event Bus. Libraries like pubsub-js or RxJS BehaviorSubject work well here.
Alternatively, if you use Module Federation, you can expose a shared Redux store or a lightweight reactive state object from a shared container shell that both MFs import dynamically.
2. When MFs Cross Page Boundaries (Routing / Navigation)
If navigating from MF A on page one to MF B on page two, runtime memory is completely wiped. You have to use web storage or routing parameters:
A. Web Storage (localStorage or sessionStorage)
Ideal for larger payloads or session states (like user configurations or auth details).
MF A (Sender): sessionStorage.setItem('shared_config', JSON.stringify(configData));
MF B (Receiver): const data = JSON.stringify(sessionStorage.getItem('shared_config'));
B. URL Query Parameters & Route State
Best practice for deep linking, search filters, or simple IDs. If you are using libraries like react-router-dom, you can pass state during navigation without cluttering the visible URL.

```typescript
MF A (Sender):
TypeScript
navigate("/profile", { state: { userId: "12345" } });

MF B (Receiver):
TypeScript
const location = useLocation();
const userId = location.state?.userId;
```

3. Parent/Shell Orchestrator Props: 
If you are using a framework like Single-SPA or a custom host application that explicitly mounts the microfrontends, the parent application can pass down data and callback functions directly via Props or attributes, just like a standard component tree.
TypeScriptdrive
// Inside the Orchestrator/Host Shell
```typescript
  <MicroFrontendA userToken={token} onSessionExpire={handleExpiry} />
```

---------------

Windows Event listener
In React, window event listeners are used to listen to browser-level events like:

resize
scroll
keydown / keyup
```typescript
import { useEffect } from "react";

const App = () => {
  useEffect(() => {
    const handleResize = () => {
      console.log("Width:", window.innerWidth);
    };

    window.addEventListener("resize", handleResize);

    // ✅ Cleanup (VERY IMPORTANT)
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return <div>Resize the window</div>;
};

export default App;
```
Why cleanup is important?
If you don’t remove it:

❌ Memory leaks
❌ Duplicate listeners
❌ Performance issues


Code for Scroll event:
```typescript
useEffect(() => {
  const handleScroll = () => {
    console.log("Scroll Y:", window.scrollY);
  };

  window.addEventListener("scroll", handleScroll);

  return () => {
    window.removeEventListener("scroll", handleScroll);
  };
}, []);


Code for keydown:


useEffect(() => {
  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === "Enter") {
      console.log("Enter pressed");
    }
  };

  window.addEventListener("keydown", handleKeyDown);

  return () => {
    window.removeEventListener("keydown", handleKeyDown);
  };
}, []);


Note that this will not work:
useEffect(() => {
  window.addEventListener("resize", () => {
    console.log("resize");
  });

  return () => {
    window.removeEventListener("resize", () => {}); ❌
  };
}, []);
```
This won’t work because it's a different function reference. So you need to create common function and use it.

--------------

How do you decide for which data you will store it in global which data will be stored in local ?
What is the advantage of inheritance?
Type script Unions and Intersections ?
How to create Typescript interface ? Why we need to create ? Give me one live example
Git CICD deployments in previous project ? If the pods are failed how will you identify
Give one live example of Jest api call for larger api response ? - have to explain detailed
Why we need to use Mock data in test files ? What is the purpose of it?
Function overloading?

---------------
Debounce & Throttling-
Debouncing and throttling are performance optimization techniques used to limit how often a function executes in response to frequent events like typing, scrolling, or window resizing.
While they sound similar, their execution patterns are completely opposite:
debounce delays execution until activity completely pauses, whereas
throttle guarantees execution at a steady, predictable interval during continuous activity.

One-line Memory Trick
Debounce = WAIT then run
Throttle = RUN then WAIT

Debounce waits until the user stops triggering events,
whereas throttle ensures the function runs only once within a fixed time interval.

Ultra Simple Analogy
Imagine doorbell 😄
Debounce → Wait until person stops pressing → open door once
Throttle → Open door once every 2 sec max, no matter how many times pressed

Debounce → Wait → then update
Throttle → Update → then wait


Debounce code:
```typescript
Debounce with filter:
import { useEffect, useState } from "react"

export const useDebounce = (value) => {
    const [debouce, setDebounce] = useState(value);
    console.log("in debounce");
    useEffect(() => {
        const id = setTimeout(() => setDebounce(value), 2000);
        return () => clearTimeout(id);
    }, [value])
    return debouce;
}
```
AppComponent:
```typescript
import { useEffect, useState } from "react";
import { ChildComponent } from "./ChildComponent";
import { NewComponent } from "./NewComponent";
import { useDebounce } from "./useDebounce";

export const ClassComponent = () => {
    const names = [
  "Aarav","Vivaan","Aditya","Arjun","Sai","Reyansh","Krishna","Ishaan","Shaurya","Atharva",
  "Dhruv","Kabir","Ritvik","Vedant","Aryan","Yash","Rohan","Karthik","Siddharth","Harsh",
  "Manav","Nikhil","Vikram","Anirudh","Rahul","Amit","Ajay","Varun","Rajat","Deepak",
  "Surya","Pranav","Abhinav","Saurabh","Kunal","Mayank","Tarun","Ravi","Jatin","Gaurav",
 
  "Aanya","Diya","Saanvi","Aadhya","Kiara","Ananya","Ira","Myra","Sara","Navya",
  "Anika","Riya","Meera","Ishita","Kavya","Pooja","Sneha","Neha","Priya","Shreya",
  "Tanvi","Ritika","Swati","Divya","Anushka","Nisha","Aarti","Komal","Payal","Simran",
  "Jyoti","Rashmi","Pallavi","Preeti","Madhuri","Kavita","Bhavna","Monika","Sangeeta","Deepa",

  "Liam","Noah","Oliver","Elijah","James","William","Benjamin","Lucas","Henry","Alexander",
  "Mason","Michael","Ethan","Daniel","Jacob","Logan","Jackson","Levi","Sebastian","Mateo",
  "Jack","Owen","Theodore","Aiden","Samuel","Joseph","John","David","Wyatt","Matthew",
  "Luke","Asher","Carter","Julian","Grayson","Leo","Jayden","Gabriel","Isaac","Lincoln",

  "Olivia","Emma","Ava","Sophia","Isabella","Mia","Amelia","Harper","Evelyn","Abigail",
  "Emily","Ella","Elizabeth","Camila","Luna","Sofia","Avery","Mila","Aria","Scarlett",
  "Penelope","Layla","Chloe","Victoria","Madison","Eleanor","Grace","Nora","Riley","Zoey",
  "Hannah","Hazel","Lily","Ellie","Violet","Lillian","Zoe","Stella","Aurora","Natalie",

  "Arun","Bala","Chandran","Dinesh","Elango","Ganesh","Hari","Ilango","Jagan","Kumar",
  "Logesh","Mani","Naveen","Prakash","Ramesh","Selva","Thiru","Uday","Vasanth","Karthi",
  "Senthil","Murugan","Perumal","Raja","Saravanan","Vel","Arivazhagan","Boopathy","Siva","Vetri",

  "Akash","Bharath","Charan","Darshan","Eshwar","Farhan","Gopi","Hemanth","Inder","Jeevan",
  "Keshav","Lokesh","Mahesh","Naresh","Omkar","Praveen","Rohit","Sachin","Teja","Umesh",
  "Vijay","Yogesh","Zuber","Anil","Bipin","Chirag","Dev","Eklavya","Faiz","Girish"
];

    const [userInput, setUserInput] = useState("")
    const userInputE  = useDebounce(userInput);

    const filteredList  = names.filter(item => item.toLowerCase().includes(userInputE.toLowerCase()))
   

    return(
        <div>
            <label htmlFor="search">Search:</label>
            <input type="text" onChange={(e) => setUserInput(e.target.value)} placeholder="Enter text to search" />
            {/* <NewComponent name="bar"/> */}

            <h4>Names List</h4>
            {filteredList && filteredList.map(item => <li key={item}>{item}</li>)}

        </div>
    )
}

```
---------------
Props and state differences ?
---------------
Redux and RTK difference ?


---------------
Lazy loading concept - Detailed explanation with one live example
```typescript
import './App.css'
import {lazy, Suspense} from "react";

const Child = lazy(() => import("./components/Child").then(module => ({default: module.Child})))

function App() {
  return (
    <>
      App Component
      <Suspense fallback = {<p>Loading...</p>}>
        <Child />
      </Suspense>
    </>
  )
}

export default App
```

Decoretors - what is the use ? why we need that?

---------------

Difference between any, unknown, never in TS.

1. Any -> can be any type, prefer not to use by default.


let data: any = "hello";
data = 10;
data.foo.bar(); // ✅ No error (but risky)

2. Unknown: Better compared to any as before using, type checking has to be made.

let value: unknown;

value = "Hello";
value = 42;

// ❌ Error
console.log(value.toUpperCase());

// ✅ Safe usage (type narrowing)
if (typeof value === "string") {
  console.log(value.toUpperCase());
}

3. Void: Does not return anything


function logMessage(msg: string): void {
  console.log(msg);
}



4. null and undefined

let x: null = null;
let y: undefined = undefined;

5. object: Any non primitive:
let obj: object = { name: "Barath" };

// ❌ Not allowed
obj = 10;

4. never: value that never happens


type Shape = "circle" | "square";

function getArea(shape: Shape) {
  switch (shape) {
    case "circle":
      return 3.14;
    case "square":
      return 4;
    default:
      const _exhaustive: never = shape; // ✅ compile-time safety
  }
}

--------------------
Readonly utility type:

const list: readonly string [] = ["A", "B", "C"];

list.push("D") // If readonly, I cannot push new values into array
console.log(list);
--------------------
I can use the type object for types right? Why is it not preferred?
object type by default means any non primitive types.
It can be an object, an array or a function

✅ Allowed:
let a : object = {name: "B"};
a = ["a", b"]
a = ():void;


// ❌ Not allowed
value = 10;
value = "hello";

Problem with object
👉 It is too generic and not useful
TypeScript doesn’t know anything about the structure.
👉 So:

“Yes, it’s an object… but I have NO idea what’s inside”

Alternatives:
1. Use Exact Object Types (Best ✅)
type User = {
  name: string;
  age: number;
};

let user: User = { name: "Barath", age: 30 };
console.log(user.name); ✅

2. Use Record<string, unknown> (Flexible ✅)

let data: Record<string, unknown>;
data = { name: "Barath", age: 30 };

 3. Use unknown (When unsure ✅)

let response: unknown;

// Narrow before use
if (typeof response === "object" && response !== null) {
  console.log(response);
}

4. Use any (Avoid ❌)

let data: any;
data.foo.bar(); // no error, risky

------------------------
Tuples:
Maintaitns the same order

-----------------------
Utility types in typescript:

1. Required: All properties are mandatory:

interface Obj {
  name: string;
  age: number;
  completed: boolean;
}

type ObjNew  = Required<Obj>

const newObj: ObjNew  = {
    name : "Bara",
    age: 23,
    completed: true
}

2. Partial: Some fields are optional or all fields can also be optional. But there cannot be a new property for the object.
3. Readonly: The property values cannot be modified.
interface Obj {
  name: string;
  age: number;
  completed: boolean;
}

type ObjNew  = Readonly<Obj>

const newObj: ObjNew  = {
    name : "b",
    age: 23,
    completed: true
}

newObj.age = 50 // Cannot assign to 'age' because it is a read-only property.

4. Pick & 5. Omit:
Pick -> Pick only the necessary fields needed and
Omit -> Omit those fields mentioned.

interface Obj {
  name: string;
  age: number;
  completed: boolean;
}

type ObjNew  = Pick<Obj, "name" | "age">
Now ObjNew type will have only name and age types. If you provide existing property, we get the field does not exists.

type ObjNew  = Pick<Obj, "name" | "age">
Only completed field exists in ObjNew type.

---------------------
If I have object, I can use record,

const names : Record<string, unknown> = {
    a: "B",
    age: 23,
    gender: "M"
}
---------------------

Why we need Generics in the first place?

function wrapInArray(value: number | string):(number | string) [] {
  return [value]
}

console.log(wrapInArray(5))


Alternate generics:
function wrapInArray<T>(value: T): T[] {
  return [value]
}

console.log(wrapInArray(5))

Another scenario of Generics:
```typescript
function details(id: number, name: string) {
    return {
        id,name
    }
}

const value1 = details(1, "AB")

All works fine, but now what if id can be string or number.

function details<T>(id: T, name: string) {
    return {
        id,name
    }
}

const value1 = details<number>(1, "AB")
const value2 = details<string>("1", "AB")
```
// const value2 = details("1", "AB") -> This also works as TS can infer the type.
Now we made sure, T is generic which can be of number and string.

Another Example:
```typescript
/*
function randomFunction(array: (number | string)[]) {
    const index = Math.floor(Math.random()*(array.length));
    return array[index];
}
*/

function randomFunction<T>(array: T[]) {
    const index = Math.floor(Math.random()*(array.length));
    return array[index];
}

console.log(randomFunction([1,2,3,4,5]))

console.log(randomFunction(["1","2","3","4","5"]))
```


Generics with fetch:
```typescript
interface User{
    username: string
}

interface Message {
    content: string
}

interface Image {
    url : string
}


async function fetchData <T>(path: string): Promise<T> {
    const data = await fetch(path);
    const res = data.json();
    return res;
}


const run = async () => {
    const user = await fetchData("/users");
    const message = await fetchData("/message");
    const image = await fetchData("/image");

}

Generic function with the function passed as parameter.

function callAndReturn<T>(fn: () => T): T {
  return fn();
}

const result = callAndReturn(() => 'asdf');
```
----------------
Extends in Generics:
```typescript
function merge<T,U>(obj1: T, obj2:U) {
    return {...obj1, ...obj2}
}

const result= merge({id: 1}, {name: "BA"});

But this also works which are clearly not objects.
const result= merge(1, "BA");

So we can use extends and extend the object.

function merge<T extends object,U extends object>(obj1: T, obj2:U) {
    return {...obj1, ...obj2}
}

const result= merge({}, {});
```
This works fine.

----------------------
```typescript
interface Product {
  name : string;
  count: number;
}

type ProductKeys = keyof Product;
const key : ProductKeys = "stirng" // will throw error.
// can be either name or count only.
```

Typescipt annoataiton is nothing but to add type to the property.

const name: string = "Ba"; // This string is called type annotations.

Type assertion is using as keyword.
Say for fetch we are using fetch to make the request. in response, we say as response as Todos, where Todos have the exact list of the objects properties.

------------------
Commonly used libraries for react:
1. Tailwind
2. Axios - to call APIs
3. prop-types
4. react-window -> If the list is very big, we render only what is shown on the screen. Used generally if the list is not very big.

Virtualization in React: Improving Performance for Large Lists:
Virtualization is a technique that optimizes this by only rendering visible elements and efficiently managing DOM updates.

Advantages:
DOM updates: Only visible elements are in the DOM.
Memory usage: Fewer elements mean less memory consumption.
Render time: Minimizing DOM operations speeds up the UI.

----------------------
Prop types:
```typescript
import {propTypes} from "prop-types";

const App1 = ({name, age}) => {
  return(<div>{name} - {age}</div>)
}

App1.propTypes = {
  name : PropTypes.string.isRequired,
  age: ProptTypes.number
}
```
-------
Closures:

The variable declared in the outerfunction can be accessed even when the scope of the function is already completed.
```typescript
function outer() {
  let count = 10;
  return function() {
    return count = count + 1
  }
}

const inner = outer();
console.log(inner())
console.log(inner())
console.log(inner())
```
Real world example : useState in React.

-----------------------
Composition vs inheritance in react:

React explicitly recommends using composition instead of inheritance to reuse code between components.
The official team has noted that across thousands of components, they have not found any use cases where component inheritance hierarchies would be recommended.
React's core philosophy centers on a component-driven architecture where features are combined like building blocks rather than extended down a rigid tree.

---------------------
Redux Toolkit and Zustand are both centralized, immutable state management libraries for React, but they target drastically different development philosophies.


| Feature | Redux Toolkit (RTK)  | Zustand |
| :--- | :---: | ---: |
| Philosophy | Highly structured and opinionated | Minimalist and flexible |
| Boilerplate|Moderate (requires Slices/Actions/Reducers)|Extremely Low (hook-based definitions)|
|Bundle Size|~40 KB minified (heavy)|~3 KB minified (ultra-lightweight)|
|React Context |Requires <Provider> |wrapperNo Context Provider required|
|Data Fetching|Built-in via RTK Query|Requires external tools (e.g., TanStack Query)|
|Best Used For|Large apps, multi-team projects, rigid data flows|Rapid shipping, small to medium apps, simple global UI|

---------------------
How will you implement APi call with retry logics:
1. using @tanstack/react-query library.
```typescript
import {useQuery} from "@tanstack/react-query";
export const TanStackQuery = () => {
   
    const {data, isPending, error} = useQuery({
        queryKey: ['Todos'],
        queryFn: () => fetch("https://jsonplaceholder.typicode.com/todos").then(res => res.json()),
        retry: 3,
        retryDelay: (attempt) => Math.min(1000 * 2 ** attempt, 30000), // Exponential backoff
    })

    if (isPending) return <span>Loading...</span>
    if (error) return <span>Oops!</span>

    return <ul>{data.map(t => <li key={t.id}>{t.title}</li>)}</ul>    
}
```
We need to wrap like a provider around the App component.
```typescript
import {TanStackQuery} from "./components/TanStackQuery";
import {useState} from "react";

function App() {
  return (
    <>
      App Component
      <TanStackQuery />
    </>
  )
}

export default App
```
------------------

Events in React for Typescript:

```typescript
// Input
React.ChangeEvent<HTMLInputElement>

// Textarea
React.ChangeEvent<HTMLTextAreaElement>

// Select
React.ChangeEvent<HTMLSelectElement>

// Form submit
React.FormEvent<HTMLFormElement>

// Button click
React.MouseEvent<HTMLButtonElement>

// Div click
React.MouseEvent<HTMLDivElement>

// Keyboard
React.KeyboardEvent<HTMLInputElement>

// Focus / Blur
React.FocusEvent<HTMLInputElement>

// Paste / Copy
React.ClipboardEvent<HTMLInputElement>

// Drag & Drop
React.DragEvent<HTMLDivElement>
```