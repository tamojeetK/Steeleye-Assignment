# Steeleye-Assignment


**1. Explain what the simple `List` component does.**



The simple `List` component is a **React** component that renders a list of items as an unordered list (`<ul>`) element. As I can see, each item is a `SingleListItem` component that displays some text and has a background color that depends on whether it is selected or not. The `List` component also keeps track of the selected index and updates it when the user clicks on an item. The `List` component uses memoization to avoid unnecessary re-rendering of its children components.

---

**2. What problems / warnings are there with code?**




Some of the problems / warnings with the code in my opinion, are:

- The `useState` hook in the **`WrappedListComponent`** is used incorrectly.  The first element of the array returned by **`useState`** should be the state variable and the second element should be the function to update the state. In this case, it should be **`[selectedIndex, setSelectedIndex]`** instead of **`[setSelectedIndex, selectedIndex]`**
- The `SingleListItem` component does not have a key prop, which is required when rendering a list of elements in *React*. This will cause a warning and may affect the performance and correctness of the rendering.
- The `isSelected` prop of the `SingleListItem` component is a *boolean* value that indicates whether the item is selected or not. However, the code passes the `selectedIndex` value, which is a number or null. This will cause a type mismatch warning and may result in incorrect behavior of the component.
- The items prop of the `WrappedListComponent` component has an incorrect type definition. It should be `PropTypes.arrayOf` instead of `PropTypes.array`.
- The `WrappedListComponent` component does not handle the case when the items prop is *null* or *undefined*. This will cause an error when trying to map over the items array.

---
**3. Please fix, optimize, and/or modify the component as much as you think is necessary.**


One possible way that the component can be fixed, optimized, and/or modified is:

- Swapping the order of the `setSelectedIndex` and `selectedIndex` variables in the `useState` hook.
- Adding a key prop to the `SingleListItem` component that is equal to the index or some other unique identifier of the item.
- Changing the `isSelected` prop of the `SingleListItem` component to be a function that compares the index with the `selectedIndex` value and returns a *boolean*.
- Changing the type definition of the items prop of the `WrappedListComponent` component to `PropTypes.arrayOf(PropTypes.shape({…}))`.
- Adding a conditional rendering to the `WrappedListComponent` component that returns *null* or some other placeholder element if the items prop is *null* or *undefined*.

If we modify the code, it would look like this:

```jsx
import React, { useState, useEffect, memo } from 'react';
import PropTypes from 'prop-types';

// Single List Item
const WrappedSingleListItem = ({
  index,
  isSelected,
  onClickHandler,
  text,
}) => {
  return (
    <li
      key={index} // added key prop
      style={{ backgroundColor: isSelected(index) ? 'green' : 'red'}} // changed isSelected prop to be a function
      onClick={onClickHandler(index)}
    >
      {text}
    </li>
  );
};

WrappedSingleListItem.propTypes = {
  index: PropTypes.number,
  isSelected: PropTypes.func.isRequired, // changed type of isSelected prop to be a function
  onClickHandler: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

const SingleListItem = memo(WrappedSingleListItem);

// List Component
const WrappedListComponent = ({
  items,
}) => {
  const [selectedIndex, setSelectedIndex] = useState(); // swapped the order of variables

  useEffect(() => {
    setSelectedIndex(null);
  }, [items]);

  const handleClick = index => {
    setSelectedIndex(index);
  };

  // added conditional rendering
  if (!items) {
    return null;
  }

  return (
    <ul style={{ textAlign: 'left' }}>
      {items.map((item, index) => (
        <SingleListItem
          onClickHandler={() => handleClick(index)}
          text={item.text}
          index={index}
          isSelected={(i) => i === selectedIndex} // changed isSelected prop to be a function
        />
      ))}
    </ul>
  )
};

WrappedListComponent.propTypes = {
  items: PropTypes.arrayOf(PropTypes.shape({ // changed type definition of items prop
    text: PropTypes.string.isRequired,
  })),
};

WrappedListComponent.defaultProps = {
  items: null,
};

const List = memo(WrappedListComponent);

export default List;he response is derived from the web page context.
```

