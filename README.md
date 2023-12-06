
### Basics of useEffect 

```javascript
useEffect(() => {
  document.title = `You clicked ${count} times`;
});
```

1. API Calls

```jsx
import React, { useState, useEffect } from 'react';

function FetchDataComponent() {
    const [data, setData] = useState(null);

    useEffect(() => {
        fetch('https://api.example.com/data')
            .then(response => response.json())
            .then(data => setData(data))
            .catch(error => console.error('Error fetching data:', error));
    }, []); // Empty dependency array means this runs once after the initial render

    return (
        <div>
            {data ? <div>Data: {JSON.stringify(data)}</div> : <div>Loading...</div>}
        </div>
    );
}
```

2. Manipulating the DOM

```jsx
import React, { useEffect } from 'react';

function DocumentTitleComponent() {
    useEffect(() => {
        document.title = 'New Page Title';
        return () => {
            document.title = 'Original Title'; // Cleanup function to reset the title
        };
    }, []);

    return <div>Check the document title!</div>;
}
```

3. Setting up Subscriptions

```jsx
import React, { useState, useEffect } from 'react';

function SubscriptionComponent() {
    const [eventData, setEventData] = useState(null);

    useEffect(() => {
        function handleEvent(data) {
            setEventData(data);
        }

        EventSystem.subscribe(handleEvent); // Subscribe to an event
        return () => {
            EventSystem.unsubscribe(handleEvent); // Cleanup the subscription
        };
    }, []);

    return <div>Event Data: {eventData}</div>;
}
```

4. Timers

```jsx
import React, { useState, useEffect } from 'react';

function TimerComponent() {
    const [count, setCount] = useState(0);

    useEffect(() => {
        const intervalId = setInterval(() => {
            setCount(prevCount => prevCount + 1);
        }, 1000);

        return () => clearInterval(intervalId); // Cleanup the interval
    }, []);

    return <div>Counter: {count}</div>;
}
```

### Dependency Array 

1. Basic

```javascript
useEffect(() => {
  console.log(`Count changed to ${count}`);
}, [count]);
```

2. Empty array

```javascript
useEffect(() => {
  console.log('Component mounted');

  return () => {
    console.log('Component will unmount');
  };
}, []);
```

### Cleaning Up Effects 

```javascript
useEffect(() => {
  const handleResize = () => {
    // ...handle the resize event
  };

  window.addEventListener('resize', handleResize);

  // Cleanup function to remove the event listener
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

### Common Pitfalls 

```js
const InfiniteCount = () => {
    const [count, setCount] = useState(0);

    useEffect(() => {
        // Increment the count, causing the component to re-render and the effect to run again
        const timer = setTimeout(() => setCount(count + 1), 100);

        // Clear the timeout if the component unmounts
        return () => clearTimeout(timer);
    }, [count]); // Dependency array includes 'count', causing the effect to rerun when 'count' changes

    return (
        <div>
            <p>Count: {count}</p>
            <p>This count will increase rapidly, demonstrating an infinite loop in useEffect.</p>
        </div>
    );
}
```

### Advanced Use Cases 

#### Data fetching

```javascript
const ExampleComponent = () => {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        const fetchData = async () => {
            try {
                const result = await axios('https://api.example.com/data');
                setData(result.data);
            } catch (err) {
                setError(err);
            } finally {
                setLoading(false);
            }
        };

        fetchData();
    }, []); // Empty dependency array ensures this effect runs only once after the component mounts

    if (loading) return <p>Loading...</p>;
    if (error) return <p>Error: {error.message}</p>;

    return (
        <div>
            <h1>Data Fetched</h1>
            <pre>{JSON.stringify(data, null, 2)}</pre>
        </div>
    );
}

export default ExampleComponent;

```

#### useEffect and Context API

```javascript
import React, { useContext, useEffect } from 'react';
import { MyContext } from './MyContext';

const MyComponent = () => {
    const { contextValue, setContextValue } = useContext(MyContext);

    useEffect(() => {
        // Perform a side effect here, possibly using contextValue
        console.log('Context value has changed:', contextValue);

        // Optionally, you can update the context value as well
        // setContextValue(newValue);

        // This effect will re-run whenever contextValue changes
    }, [contextValue]); // Dependency array includes contextValue

    return (
        <div>
            <p>Current Context Value: {contextValue}</p>
            {/* Render other components or UI elements here */}
        </div>
    );
}

export default MyComponent;
```


##### Debouncing

```javascript
import React, { useState, useEffect } from 'react';

const DebouncedComponent = () => {
    const [inputValue, setInputValue] = useState('');
    const [debouncedValue, setDebouncedValue] = useState('');

    useEffect(() => {
        const handler = setTimeout(() => {
            setDebouncedValue(inputValue);
        }, 1000); // Debounce time of 1000ms

        return () => {
            clearTimeout(handler);
        };
    }, [inputValue]); // Effect depends on inputValue

    return (
        <div>
            <input
                type="text"
                value={inputValue}
                onChange={(e) => setInputValue(e.target.value)}
            />
            <p>Debounced Value: {debouncedValue}</p>
        </div>
    );
};

export default DebouncedComponent;
```

##### Throttling

```javascript
import React, { useState, useEffect } from 'react';

const ThrottledComponent = () => {
    const [inputValue, setInputValue] = useState('');
    const [throttledValue, setThrottledValue] = useState('');

    useEffect(() => {
        const handler = setTimeout(() => {
            setThrottledValue(inputValue);
        }, 1000); // Throttle time of 1000ms

        return () => {
            clearTimeout(handler);
        };
    }, [inputValue]); // Effect depends on inputValue

    return (
        <div>
            <input
                type="text"
                value={inputValue}
                onChange={(e) => setInputValue(e.target.value)}
            />
            <p>Throttled Value: {throttledValue}</p>
        </div>
    );
};

export default ThrottledComponent;
```

#### Integration with External Libraries using useEffect

##### Integrating with D3.js

```javascript
import React, { useEffect, useRef } from 'react';
import * as d3 from 'd3';

const D3Component = ({ data }) => {
    const d3Container = useRef(null);

    useEffect(() => {
        if (data && d3Container.current) {
            const svg = d3.select(d3Container.current);

            // Use D3 to manipulate the SVG element
            // Example: creating a simple bar chart
            svg.selectAll('rect')
                .data(data)
                .enter()
                .append('rect')
                .attr('width', 20)
                .attr('height', d => d * 10)
                .attr('x', (d, i) => i * 25);
        }
    }, [data]); // Effect depends on the data

    return <svg ref={d3Container} />;
};

export default D3Component;
```

##### Integrating with jQuery Plugins

```javascript
import React, { useEffect, useRef } from 'react';
import $ from 'jquery';
import 'some-jquery-plugin';

const JqueryComponent = () => {
    const jqueryContainer = useRef(null);

    useEffect(() => {
        // Initialize the jQuery plugin
        $(jqueryContainer.current).someJqueryPlugin();

        // Cleanup function to remove the plugin
        return () => {
            $(jqueryContainer.current).someJqueryPlugin('destroy');
        };
    }, []); // Empty dependency array ensures the effect runs only once

    return <div ref={jqueryContainer}></div>;
};

export default JqueryComponent;
```

### Best Practices 

### 1. Keeping Effects Focused on a Single Task

#### Before
```javascript
useEffect(() => {
    fetchData();
    document.title = `New Data Fetched`;
    // Combining data fetching and setting document title in one effect
}, [fetchData]);
```

#### After
```javascript
useEffect(() => {
    fetchData();
    // Solely focused on fetching data
}, [fetchData]);

useEffect(() => {
    document.title = `New Data Fetched`;
    // Separate effect for updating document title
}, []);
```

### 2. Using Multiple `useEffect` Hooks for Unrelated Logic

#### Before
```javascript
useEffect(() => {
    fetchData();
    subscribeToNewsletter();
    // Unrelated tasks: fetching data and subscribing to newsletter
}, [fetchData]);
```

#### After
```javascript
useEffect(() => {
    fetchData();
    // Dedicated to fetching data
}, [fetchData]);

useEffect(() => {
    subscribeToNewsletter();
    // Separate effect for newsletter subscription
}, []);
```

### 3. Leveraging Custom Hooks for Reusable Effects

#### Before
```javascript
useEffect(() => {
    const handleResize = () => {
        // Handle resize logic
    };
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
}, []);
```

#### After
```javascript
function useWindowResize(handler) {
    useEffect(() => {
        window.addEventListener('resize', handler);
        return () => window.removeEventListener('resize', handler);
    }, [handler]);
}

// Usage
useWindowResize(() => {
    // Resize logic here
});
```

### 4. Always Including Dependency Arrays

#### Before
```javascript
useEffect(() => {
    fetchData();
    // Missing dependency array
});
```

#### After
```javascript
useEffect(() => {
    fetchData();
    // Dependency array included
}, [fetchData]);
```






