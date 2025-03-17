---
layout: post
title: "Some useful Vanilla JS functions"
description: "A blog post that describes some useful Vanilla JavaScript functions for use when developing websites"
---

# Some useful Vanilla JS functions

<section markdown="1">

I quite often find myself writing the same few utility functions when building a Vanilla JavaScript website and so I thought I'd share them with the world.

My normal project setup involves:

- using `// @ts-check` at the start of my JavaScript source files to get code hints and type checking from the editor;
- adding <a href="https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html" target="_blank" referrer-policy="none">JSDoc</a> comments to functions so the editor can check type safety;
- a static HTML file that imports my JavaScript source file and includes one or more elements that are required by the JavaScript file.

</section>

<section markdown="1">

## Finding elements on the page

The first utility function gets an element by ID and checks its type:

```js
/**
 * Fetch an element from the page
 * @template {HTMLElement} T
 * @param {string} id ID of the element to be found.
 * @param {new () => T} type the expected type of the element.
 * @returns {T} the required element.
 */
const getElementAs = (id, type) => {
  const element = document.getElementById(id);
  if (!(element instanceof type)) {
    throw new Error(`Expected element ${id} to have type ${type}: ${element}`);
  }
  return element;
};
```

If you don't care what the type is, only that it exists, you can pass `HTMLElement`. Here's an example that finds a form by ID and ensures it has the correct type:

```js
const myForm = getElementAs("my-form", HTMLFormElement);

myForm.addEventListener("submit", (event) => {
  event.preventDefault();

  // Since the editor now knows that myForm is an HTMLFormElement
  // it can be used to create FormData without any warnings.
  const formData = new FormData(myForm);
});
```
</section>

<section markdown="1">

## Creating new elements

I often find myself needing to construct new HTML elements in JavaScript to dynamically update the page contents. This can be a somewhat tedious process involving:

- using `document.createElement('div')` to create elements;
- setting / adding various properties on the element returned (e.g. appending to the `classList`, setting `dataset` properties etc);
- using `parent.appendElement(someElement)` calls to add the new elements to the page.

This can get pretty verbose and it's hard to keep track of which elements are being created and which parents they are being added to. Here's an example that creates a `div` and a `span` and sets some class names etc:

```js
const parent = getElementAs("my-container", HTMLElement);
const cardDiv = document.createElement("div");
parent.appendChild(cardDiv);

cardDiv.classList.append("card");
cardDiv.dataset.suit = "spades";
cardDiv.dataset.value = "9";

const cardValueSpan = document.createElement("span");
cardValueSpan.textContent = "9";
cardDiv.appendChild(cardValueSpan);
```

Instead, I use a factory function like this to do all of the creation steps in a single function call:

```js
/**
 * Creates a new element of type `type` and appends it as a child to element `parent`.
 *
 * @template {keyof HTMLElementTagNameMap} T
 * @param {HTMLElement} parent the parent HTML element.
 * @param {T} type the type of HTML Element to be created.
 * @param {Partial<Omit<HTMLElementTagNameMap[T], 'classList'>> & {
 *   classList?: string[]
 * }=} properties any properties to be set on the element.
 * @returns {HTMLElementTagNameMap[T]} the newly created element
 */
const appendNewChild = (parent, type, properties = {}) => {
  // Create the new element
  const newElement = document.createElement(type);

  // Set the properties of the new element
  for (const [property, value] of Object.entries(properties ?? {})) {
    // classList is a special case since you can't (officially) set it directly
    if (property === "classList" && Array.isArray(value)) {
      for (const className of value) {
        newElement.classList.add(className);
      }
      // dataset is another special case...
    } else if (property === "dataset" && typeof value === "object" && value) {
      for (const [datasetKey, datasetValue] of Object.entries(value)) {
        newElement.dataset[datasetKey] = datasetValue;
      }
    } else {
      // More "normal" properties can just be set directly
      newElement[property] = value;
    }
  }

  // Append the child element to the parent
  parent.appendChild(newElement);
  return newElement;
};
```

Here's the same example using this factory function – I find this much easier to read as it's more declarative (but everyone's tastes are different!). Because the editor can infer the type of element you are creating you can get editor hints for the available properties and the values are type checked as you write the code:

```js
const parent = getElementAs("my-container", HTMLElement);

const cardDiv = appendNewChild("div", parent, {
  classList: ["card"],
  dataset: {
    suit: "spades",
    value: "9",
  },
});

appendNewChild("span", cardDiv, {
  textContent: "9",
});
```

I hope that you find these helper functions useful. I'll probably come back and add some more / update them as I come across new things I find handy...

</section>
