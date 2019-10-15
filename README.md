# Toolpic
 Toolpic software of Fridays For Future Germany.

# Structure

The `Rendering` engine and the `Components` object are part of the module namespace object in the `main.js`.

### Example
```javascript
import * as Toolpic from "./dist/main.js"

console.log(Toolpic.Renderer);
console.log(Toolpic.Components);

```

# Renderer

```javascript
// Import Renderer
import Renderer from "./dist/main.js"

const render = new Renderer(template, 0);
```

To initialize a `Renderer`, pass the template description object (same as content of `template.json`) and the index of the format document you want to render.

## Events

### load

```javascript
render.once("load", function() {
  // Rendering function ready to use
});

```

## Data Receiver

To get or set the data of the rendering instance, use the `data` proxy.

### Get Data

**Important**: If you *get* a property of the `data` proxy, you get an value description object with a lot of more informations as the connected *component*.

```javascript
const myPropertyDescriptor = render.data["myProperty"];

console.log(myPropertyDescriptor);

/*
{
  type: 'Component type',
  description: 'Your Description',
  required: [Array],
  properties: [Array],
  value: <Value>
}
*/
```

You can just *get* properties that are defined within the *template description object*!

### Set Data


```javascript
render.data["myProperty"] = "my String";
```

## Render Context

Within a rendering instance, there exists always a `context` element which is a normal `<svg>` node. This is the element, the magic happens within. You can append it or do what you like to do with it ;-)

```javascript
render.once("load", function() {
  // Append the context svg element to the body
  document.body.append(render.context);
});
```


## Template Description Object

The first argument is a *template description object*, which is just an object literal that describes the template generally. It contains all *format documents* (e.g. *facebook* & *twitter*) and the dynamic properties that will be required during rendering.

It looks like this:

```json
{
  "name": "Date 2",
  "preview": "data/templates/date-2/preview.jpg",
  "type": "jpg",
  "documents": [

  ],
  "fonts": [

  ],
  "fields": [

  ]
}
```

* `name`: String that describes the name of the described template
* `preview`: URL to a preview image that previews the template
* `type`: Primary export format. Mostly `jpg` but in case of a template with alpha `png`
* `documents`: Array that contains objects which describe each format that can be rendered (*Twitter*, *Facebook & Instagram*)
* `fonts`: Array that contains object which describe all required fonts for this template
* `fields`: Array that contains components that are linked to properties of the template

### Field Object

A *field object* contains the general information about the field as the required component class such as `Line`, `Selection` or `Checklist`

#### Structure

```json
{
  "type": "TypeClassName",
  "description": "Description",
  "key": "propName",
  "default": null,
  "properties": {

  }
}
```

* `type`: Class name of component that shall be connected to (Needs to be valid class name that is exported by `Components`). E.g. `Line`, `Number`, `Checklist`
* `description`: Graphically description text
* `key`: Related property name within the rendering instance's data controller
* `default`: Default value for the rendering instance's data controller
* `properties`: Object containing properties that are specific for this `type` of component

**The** `type` **property has to be a valid class name of a component class exported to** `Components` **of the module namespace object!**

#### Example

```json
{
  "type": "Number",
  "description": "Position",
  "key": "pos",
  "default": 0,
  "properties": {
    "value": 0,
    "max": 1,
    "min": -1,
    "step": 0.05,
    "kind": "slider"
  }
}
```

**To understand all the others components and their properties, have a look at any** `template.json` **file! :)**

## Format Document Index

The second argument is a *format description index*, that is just an Integer that describes which format document (described in the *template description object*) will be used.



## Component

A component is always an instance of the `SuperCompontent` class.

### Example

```javascript
const styleSource = 'dist/Components/Line/style.css';

class LineComponent extends SuperComponent {
  // Keyname and dataset to connect with
  constructor(keyName, rendererInstance) {
    super(styleSource, keyName, rendererInstance);

    const self = this;

    // Create input field by passing parameters trough assign()
    const input = Object.assign(document.createElement("input"), {
      type: "text",
      // Default/Start value is the current value of the dataset (mostly the default one)
      value: self.value.value
    });
    // Listen to input event of input field
    input.addEventListener("input", function() {
      // Set dataset
      self.value = this.value;
    });
    // Append input to root element of shadow
    this.root.append(input);

    // If this key changed on rendering instance
    this.on("update", function() {
      // Update input element
      input.value = self.value.value;
    });


    //return this.container;
  }
}

```

## Initialization

To initialize a *component*, pass a *keyName* and a related *Rendering instance* to the class constructor. This connects the *Rendering instance* to the createc component.

```javascript
// MyComponent is a constructor for a random component

const myComponent = new MyComponent('keyname', rendererInstance);
```

## Data

To *set* & *get* data, use the `value` property of each component.

### [Setter] value

```javascript
// myComponent is the instance of a component

myComponent.value = `Foo bar`;
```

### [Getter] value

```javascript
// myComponent is the instance of a component

const val = myComponent.value;

// This is not the direct value but an value description object from the Rendering instance
console.log(val); // Prints 'Foo bar'

// 'Real' value as typed in
console.log(val.value);
```

### Event update

If the connected property of a component changes within the Rendering instance, the `update` event fires.

```javascript
// myComponent is the instance of a component

myComponent.on("update", function() {
  // Log new value
  console.log(myComponent.value.value);
});
```

## Sounds complicated?

Just have a look at this example of the default `Line` component:


```javascript
import SuperComponent from '../SuperComponent.js'

const styleSource = 'dist/Components/Line/style.css';

class LineComponent extends SuperComponent {
  // Keyname and dataset to connect with
  constructor(keyName, rendererInstance) {
    super(styleSource, keyName, rendererInstance);

    const self = this;

    // Create input field by passing parameters trough assign()
    const input = Object.assign(document.createElement("input"), {
      type: "text",
      // Default/Start value is the current value of the dataset (mostly the default one)
      value: self.value.value
    });
    // Listen to input event of input field
    input.addEventListener("input", function() {
      // Set dataset
      self.value = this.value;
    });
    // Append input to root element of shadow
    this.root.append(input);

    // If this key changed on rendering instance
    this.on("update", function() {
      // Update input element
      input.value = self.value.value;
    });

  }
}


export default LineComponent;
```
