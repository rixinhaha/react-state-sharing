### Answers to questions from react-state sharing

Link to slides: https://docs.google.com/presentation/d/1-wzHDakVsRKzFGAj_AmRj86fN-MS9-Ad_tFtxDGNNVY/edit?usp=sharing

This sharing will briefly introduce the following topics:

- Refresher on state
- Reconciliation algorithm
- Misconceptions of setState
- React Hooks internals
- Problems with our code
- Global state

**If we update the variable we passed to the state, does the state in react get changed?**

- If we update the directly in react without using setState call, the state in react will be changed. However, this will eventually introduce bugs in the rendering of the updated UI (esp if we have pure components that will not always update).

**URL link to article saying that set state behaving differently in promise.**

- https://blog.logrocket.com/simplifying-state-management-in-react-apps-with-batched-updates/

**When I use await, does it mean the batching of render is not done.**
e.g. "So this will cause 1 or 2 renders? const a = await apiCall(); setItem(a); setItem2(a);"

- Behavior is verified for the below example. On component did mount (useEffect with empty dependency array), there are 2 setState calls made and there are 2 renders in this case, one after setCount and one after setData.
- Copy this code and you should see a total of 3 console.logs the first one due to the initial mounting, the second one which sets the count to and finally, the last one that sets data to be error.

```JavaScript
import React, { useState, useEffect } from "react";
import axios from "axios";

function App() {
  const [data, setData] = useState("");
  const [count, setCount] = useState(0);



  useEffect(() => {
    async function fetchData() {
      await axios.get("https://quotes.rest/quote/random").catch(() => {
        setCount(20);
        setData("error");
      });
    }
    fetchData();
  }, []);

  console.log("Count: " + count + ", Data: " + data);

  return (
    <ul>
      <li>{data}</li>
    </ul>
  );
}

export default App;

```

**For the code in slide 12, how many renders are there?**

- There are 2 renders with each increment/decrement function. Every time we do setState in a promise/async function, there is no state-batching. Each individual setState will result in a render.

**Can prevState work with hook?**

- Yes it can. https://reactjs.org/docs/hooks-reference.html#functional-updates
