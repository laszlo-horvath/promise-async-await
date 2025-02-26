# 60-Minute Coding Exercise: Promise vs. Async Await

### Step 0: Let's start by creating a new JavaScript file.
`promise.js`

### Step 1: Create a basic promise.
```javascript
  const myPromise = new Promise();
```

#### Step 1.1: Log out the promise object
```javascript
  const myPromise = new Promise();
  console.log(myPromise);
```

### Step 2: Now, resolve the Promise with the number 5.
```javascript
  const myPromise = new Promise((resolve) => resolve(5));
```

#### Step 2.1: Log out the promise object again
```javascript
  const myPromise = new Promise((resolve) => resolve(5));
  console.log(myPromise);
```

#### Step 3: Now, reject the Promise with an error ("500 - server error").
```javascript
  const myPromise = new Promise((resolve, reject) => reject("500 - server error"));
```

#### Step 3.1: Log out the promise object again
```javascript
  const myPromise = new Promise((resolve, reject) => reject("500 - server error"));
  console.log(myPromise);
```

#### Step 4: Let's add a .then() handler to log the result
```javascript
  const myPromise = new Promise((resolve) => resolve(5));
  myPromise.then(result => console.log(result));
```

#### Step 5: Let's modify our promise to randomly resolve or reject. Create a promise that simulates an API call with a 50% chance of success.
```javascript
  const randomPromise = new Promise((resolve, reject) => {
  const success = Math.random() > 0.5;
  if (success) {
    resolve("Success!");
  } else {
    reject(new Error("Something went wrong"));
  }
});
```
#### Step 6: Now handle both the success and failure cases with .then() and .catch().
**Common Mistake: "Developers often forget to handle errors. How would you properly handle both success and failure?"**

```javascript
  randomPromise
    .then(result => console.log(result))
    .catch(error => console.error(error.message));
```

#### Step 6.1: Use the finnaly method, to see it runs both after resolve and rejected cases
```javascript
  randomPromise
    .then(result => console.log(result))
    .catch(error => console.error(error.message))
    .finally(finalResult => console.log(finalResult));
```

#### Step 7: Create a function called 'wait' that takes a time in milliseconds and returns a promise that resolves after that time.
```javascript
  function wait(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
```

#### Step 8: Use your wait function to create a sequence: log 'Starting', wait 2 seconds, log 'Middle', wait 1 more second, then log 'End'.
**Common mistake: Explain what happens when you forgot to return the 2nd promise.**
Explanation: Without the return, the second .then() won't wait for wait() to complete. Instead, it will run immediately with secondResponse as undefined because the previous .then() implicitly returns undefined.

```javascript
  console.log("Starting");
  wait(2000)
    .then(() => {
      console.log("Middle");
      return wait(1000);
    })
    .then(() => {
      console.log("End");
    });
```

#### Step 9: Now let's convert this sequence to use async/await. Create an async function called 'sequence' that does the same thing.
```javascript
  async function sequence() {
    console.log("Starting");
    await wait(2000);

    console.log("Middle");

    await wait(1000);
    console.log("End");
  }
  
  sequence();
```

#### Step 10: Create a function called 'fetchUserData' that returns a promise which resolves after 1 second with an object: { id: 1, name: 'John', age: 30 }.
```javascript
  function fetchUserData() {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve({ id: 1, name: 'John', age: 30 });
      }, 1000);
    });
  }
```

#### Step 11: Now create a function called 'fetchUserPosts' that takes a userId and returns a promise that resolves after 1 second with an array of post objects.
```javascript
  function fetchUserPosts(userId) {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve([
          { id: 1, title: "First post", userId },
          { id: 2, title: "Second post", userId }
        ]);
      }, 1000);
    });
  }
```

#### Step 12: Write a function using promise chains to fetch a user and then their posts, returning an object with both user and posts.
```javascript
  function getUserWithPosts() {
    return fetchUserData()
      .then(user => {
        return fetchUserPosts(user.id)
          .then(posts => {
            return { user, posts };
          });
      });
  }
```
#### Step 13: Now convert that function to use async/await instead of promise chains.
```javascript
  async function getUserWithPostsAsync() {
    const user = await fetchUserData();
    const posts = await fetchUserPosts(user.id);
    return { user, posts };
  }
```
#### Step 14: Create a function that runs three API calls in parallel using Promise.all
```javascript
  function simulateAPI() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        const success = Math.random() > 0.5;
        if (success) {
          resolve({ data: "API response data" });
        } else {
          reject(new Error("API request failed"));
        }
      }, 1000);
    });
  }

  async function parallelAPICalls() {
    try {
      const results = await Promise.all([
        simulateAPI(),
        simulateAPI(),
        simulateAPI()
      ]);
      
      console.log("All API calls succeeded:", results);
      return results;
    } catch (error) {
      console.error("At least one API call failed:", error.message);
      return [];
    }
  }
```

#### Step 15: What's the difference between these two approaches? When would you use each?
**Explanation: Sequential runs one after another (taking 3x the time but failing only at the point of error). Parallel starts all promises at once (faster, but if any promise rejects, the entire Promise.all rejects immediately).**

```javascript
  // Approach 1: Sequential
  async function sequential() {
    const result1 = await simulateAPI();
    const result2 = await simulateAPI();
    const result3 = await simulateAPI();
    return [result1, result2, result3];
  }
  
  // Approach 2: Parallel with Promise.all
  async function parallel() {
    const results = await Promise.all([
      simulateAPI(),
      simulateAPI(),
      simulateAPI()
    ]);
    return results;
  }
```

#### Step 16: How would you handle a situation where you want to run multiple promises in parallel but don't want to fail if one rejects?
```javascript
  async function resilientParallel() {
    const promises = [
      simulateAPI().catch(err => ({ error: err.message })),
      simulateAPI().catch(err => ({ error: err.message })),
      simulateAPI().catch(err => ({ error: err.message }))
    ];
    
    const results = await Promise.all(promises);
    return results;
  }
```
#### Step 17: Many developers try to use forEach with promises. Write code to fetch all users using forEach.
**Explanation: This will log an empty array immediately. The forEach loop doesn't wait for the promises to resolve - it just starts all the fetch operations and immediately returns the empty users array. The .then() callbacks will run later and push to the array, but by then the function has already returned.**

```javascript
  function fetchUsersWithForEach() {
    const users = [];
    
    userIds.forEach(id => {
      fetchUser(id).then(user => {
        users.push(user);
        console.log(`Fetched user ${user.id}`);
      });
    });
    
    return users;
  }
  
  // Let's test it
  const users = fetchUsersWithForEach();
  console.log("Users:", users);
```

#### Step 18: Now let's fix it using Promise.all with map. How would you properly fetch all users in parallel and wait for all results?
**Explanation: map creates a new array of promises, and Promise.all waits for all promises in the array to resolve. It then resolves with an array of all the resolved values in the same order. Unlike forEach, we're returning the Promise.all result, making the entire operation asynchronous.**

```javascript
  function fetchUsersWithPromiseAll() {
    return Promise.all(userIds.map(id => fetchUser(id)));
  }
  
  // Testing it
  fetchUsersWithPromiseAll()
    .then(users => {
      console.log("All users fetched:", users);
    })
    .catch(error => {
      console.error("Failed to fetch users:", error.message);
    });
```
#### Step 19: Async await - forEach vs. for of loop
**Explanation: Using async callbacks within forEach doesn't make forEach itself wait. Each callback becomes async, but forEach completes immediately without waiting for those async callbacks. The "Users after loop" message will still show an empty array, and the function will return before any users are fetched.**

```javascript
  async function fetchUsersWithAsyncForEach() {
    const users = [];
    
    userIds.forEach(async id => {
      const user = await fetchUser(id);
      users.push(user);
      console.log(`Fetched user ${user.id}`);
    });
    
    console.log("Users after loop:", users);
    return users;
  }
  
  // Testing it
  const asyncUsers = await fetchUsersWithAsyncForEach();
  console.log("Final users:", asyncUsers);
```

**Explanation: The sequential approach fetches one user at a time, waiting for each to complete before starting the next. This takes about userIds.length × 1000ms (5 seconds). The Promise.all approach starts all fetches simultaneously and waits for all to complete, taking around 1000ms total regardless of array size.**
```javascript
  async function fetchUsersSequentially() {
    const users = [];
    
    for (const id of userIds) {
      const user = await fetchUser(id);
      users.push(user);
      console.log(`Fetched user ${user.id}`);
    }
    
    console.log("All users fetched sequentially:", users);
    return users;
  }
```
