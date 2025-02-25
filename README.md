# 60-Minute Coding Exercise: Promise vs. Async Await

## Exercise 0: Fetching Tasks with Promise Handling
```javascript
// First, implement with Promise chains
const fetchTasks = () => {
  return fetch('https://jsonplaceholder.typicode.com/todos?_limit=10')
    .then(response => response.json())
    .then(data => data)
    .catch(error => {
      console.error("Error fetching tasks:", error);
      return [];
    });
};
```

// Then refactor to async/await
```javascript
const fetchTasksAsync = async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=10');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Error fetching tasks:", error);
    return [];
  }
};
```

## Step 0: Let's start by creating a new JavaScript file.

## Step 1: Create a basic promise.
### Step 1.1: Log out the promise object

## Step 2: Now, resolve the Promise with the number 5.
```javascript
const promise = new Promise((resolve) => resolve(5));
```

### Step 2.1: Log out the promise object again

## Step 3: Now, reject the Promise with an error ("500 - server error").
### Step 3.1: Log out the promise object again
