Here’s a complete **React component** that:

1. Displays buttons to **filter by `type`**
2. Includes a **search bar** to filter by `name`, `id`, or `type`
3. Shows all filtered data in a **grid/table layout**
4. Uses **Tailwind CSS** for styling

---

### ✅ `FilterTable.js`

```jsx
import React, { useState, useMemo } from 'react';

const dataList = [
  { id: 1, name: 'Alpha', type: 'A' },
  { id: 2, name: 'Beta', type: 'B' },
  { id: 3, name: 'Gamma', type: 'A' },
  { id: 4, name: 'Delta', type: 'C' },
  { id: 5, name: 'Epsilon', type: 'B' },
  { id: 6, name: 'Zeta', type: 'C' },
];

export default function FilterTable() {
  const [selectedType, setSelectedType] = useState('All');
  const [searchText, setSearchText] = useState('');

  const types = useMemo(() => ['All', ...new Set(dataList.map(item => item.type))], []);

  const filteredData = useMemo(() => {
    return dataList.filter(item => {
      const matchesType = selectedType === 'All' || item.type === selectedType;
      const matchesSearch =
        item.name.toLowerCase().includes(searchText.toLowerCase()) ||
        item.type.toLowerCase().includes(searchText.toLowerCase()) ||
        item.id.toString().includes(searchText);
      return matchesType && matchesSearch;
    });
  }, [selectedType, searchText]);

  return (
    <div className="p-6 max-w-4xl mx-auto">
      <h2 className="text-2xl font-bold mb-4">Filterable Data Table</h2>

      {/* Type Filter Buttons */}
      <div className="flex flex-wrap gap-2 mb-4">
        {types.map(type => (
          <button
            key={type}
            onClick={() => setSelectedType(type)}
            className={`px-4 py-2 rounded-full text-sm font-medium transition 
              ${selectedType === type ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-800 hover:bg-blue-100'}`}
          >
            {type}
          </button>
        ))}
      </div>

      {/* Search Bar */}
      <div className="mb-4">
        <input
          type="text"
          value={searchText}
          onChange={e => setSearchText(e.target.value)}
          placeholder="Search by name, ID, or type..."
          className="w-full px-4 py-2 border rounded-md shadow-sm focus:outline-none focus:ring focus:ring-blue-300"
        />
      </div>

      {/* Data Table */}
      <div className="overflow-x-auto rounded-lg shadow-md">
        <table className="min-w-full bg-white divide-y divide-gray-200 text-sm text-left">
          <thead className="bg-gray-100 text-gray-700 font-semibold">
            <tr>
              <th className="px-6 py-3">ID</th>
              <th className="px-6 py-3">Name</th>
              <th className="px-6 py-3">Type</th>
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-100">
            {filteredData.length > 0 ? (
              filteredData.map(item => (
                <tr key={item.id} className="hover:bg-gray-50 transition">
                  <td className="px-6 py-3">{item.id}</td>
                  <td className="px-6 py-3">{item.name}</td>
                  <td className="px-6 py-3">{item.type}</td>
                </tr>
              ))
            ) : (
              <tr>
                <td colSpan="3" className="px-6 py-3 text-center text-gray-500">
                  No data found.
                </td>
              </tr>
            )}
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

---

### ✅ Usage:

Add this component to your app like this:

```jsx
import FilterTable from './components/FilterTable';

function App() {
  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <FilterTable />
    </div>
  );
}
```

---

### 🎨 Requirements:

* Tailwind CSS must be set up in your React project.
* If you're using **Create React App**, make sure Tailwind is properly configured via PostCSS.

Would you like me to convert this to **TypeScript**, add **pagination**, or turn it into a reusable component with props?




#2




----



Great! Here's how you can fetch data from an API in React, store it in **Context API** using `useContext`, and then use that data in the component like your filter/search UI.

---

## ✅ Step-by-Step Overview

1. **Create a Context** to hold data.
2. **Fetch API data** in a Context Provider.
3. **Wrap your App in the Provider**.
4. **Use `useContext` in your component** to access and use the data.

---

### 📁 Folder Structure (Recommended)

```
/src
  /context
    DataContext.js
  /components
    FilterTable.js
  App.js
```

---

## 📦 1. Create Context — `DataContext.js`

```jsx
import React, { createContext, useState, useEffect } from 'react';

export const DataContext = createContext();

export const DataProvider = ({ children }) => {
  const [data, setData] = useState([]);

  useEffect(() => {
    // Fetch data from your API
    const fetchData = async () => {
      try {
        const res = await fetch('https://api.example.com/data'); // Replace with your API
        const json = await res.json();
        setData(json);
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    };

    fetchData();
  }, []);

  return (
    <DataContext.Provider value={{ data }}>
      {children}
    </DataContext.Provider>
  );
};
```

---

## 🌐 2. Wrap App with Provider — `App.js`

```jsx
import React from 'react';
import { DataProvider } from './context/DataContext';
import FilterTable from './components/FilterTable';

function App() {
  return (
    <DataProvider>
      <div className="min-h-screen bg-gray-50 p-6">
        <FilterTable />
      </div>
    </DataProvider>
  );
}

export default App;
```

---

## 🧠 3. Use Context in Component — `FilterTable.js`

```jsx
import React, { useState, useMemo, useContext } from 'react';
import { DataContext } from '../context/DataContext';

export default function FilterTable() {
  const { data } = useContext(DataContext); // use context data
  const [selectedType, setSelectedType] = useState('All');
  const [searchText, setSearchText] = useState('');

  const types = useMemo(() => ['All', ...new Set(data.map(item => item.type))], [data]);

  const filteredData = useMemo(() => {
    return data.filter(item => {
      const matchesType = selectedType === 'All' || item.type === selectedType;
      const matchesSearch =
        item.name?.toLowerCase().includes(searchText.toLowerCase()) ||
        item.type?.toLowerCase().includes(searchText.toLowerCase()) ||
        item.id?.toString().includes(searchText);
      return matchesType && matchesSearch;
    });
  }, [selectedType, searchText, data]);

  return (
    <div className="p-6 max-w-4xl mx-auto">
      <h2 className="text-2xl font-bold mb-4">Filterable Data Table</h2>

      {/* Type Filter Buttons */}
      <div className="flex flex-wrap gap-2 mb-4">
        {types.map(type => (
          <button
            key={type}
            onClick={() => setSelectedType(type)}
            className={`px-4 py-2 rounded-full text-sm font-medium transition 
              ${selectedType === type ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-800 hover:bg-blue-100'}`}
          >
            {type}
          </button>
        ))}
      </div>

      {/* Search Bar */}
      <div className="mb-4">
        <input
          type="text"
          value={searchText}
          onChange={e => setSearchText(e.target.value)}
          placeholder="Search by name, ID, or type..."
          className="w-full px-4 py-2 border rounded-md shadow-sm focus:outline-none focus:ring focus:ring-blue-300"
        />
      </div>

      {/* Data Table */}
      <div className="overflow-x-auto rounded-lg shadow-md">
        <table className="min-w-full bg-white divide-y divide-gray-200 text-sm text-left">
          <thead className="bg-gray-100 text-gray-700 font-semibold">
            <tr>
              <th className="px-6 py-3">ID</th>
              <th className="px-6 py-3">Name</th>
              <th className="px-6 py-3">Type</th>
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-100">
            {filteredData.length > 0 ? (
              filteredData.map(item => (
                <tr key={item.id} className="hover:bg-gray-50 transition">
                  <td className="px-6 py-3">{item.id}</td>
                  <td className="px-6 py-3">{item.name}</td>
                  <td className="px-6 py-3">{item.type}</td>
                </tr>
              ))
            ) : (
              <tr>
                <td colSpan="3" className="px-6 py-3 text-center text-gray-500">
                  No data found.
                </td>
              </tr>
            )}
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

---

### ✅ Result:

* Data is fetched once and globally available through context.
* `FilterTable` can access and filter it by type or search.
* UI updates instantly as you type or click buttons.

---

Would you like:

* Pagination or loading spinner?
* Editable rows?
* Saving filters to localStorage?

Let me know and I can add it!



---

#3

Yes, you **can use `useCallback()`** to define the `fetchData` function when you want to reuse or pass it to other components without redefining it on every render. But using `useCallback` **alone won't execute** the function—you still need `useEffect` to call it once when the component mounts.

---

## ✅ Example Using `useCallback` with `useEffect`

```jsx
import { useState, useEffect, useCallback } from 'react';

function MyComponent() {
  const [data, setData] = useState([]);

  const fetchData = useCallback(async () => {
    try {
      const res = await fetch('https://api.example.com/data');
      const json = await res.json();
      setData(json);
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }, []); // Only recreated if dependencies change

  useEffect(() => {
    fetchData(); // Call the memoized function
  }, [fetchData]); // Add it as dependency

  return (
    <div>
      {data.map(item => (
        <div key={item.id}>{item.name}</div>
      ))}
    </div>
  );
}
```

---

### 💡 Why Use `useCallback` Here?

Use `useCallback` when:

* You want to pass the function to children without causing unnecessary re-renders.
* You want the function to be memoized for performance or dependency optimization.
* You want to make `useEffect` cleaner by extracting the logic.

---

### ❌ When Not to Use `useCallback`?

* If the function is only used inside a single `useEffect` and nowhere else, you can define it inline (like your original code).

---

Let me know if you want a reusable `useFetch` hook version of this!
