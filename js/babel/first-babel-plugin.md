# First Babel Plugin 
Change the name of default import and all its uses

## Original Code
```jsx
import Button from "./Button.js";

function App(){
    return(
        <div className="App">
            <Button color="red"/>
            <Button color="green">
        </div>
    )
}
```

## Target Code
```jsx
import Boop from "./Button.js";

function App(){
    return(
        <div className="App">
            <Boop color="red"/>
            <Boop color="green">
        </div>
    )
}
```

## Boilerplate For Plugin
Create a function
```js
function babelPlugin(babel){
    return {
        visitor: {},
    };
}
```

## Babel Plugin
```js
function babelPlugin(babel){
    const {types: t} = babel;
    return {
        name: "ast-transform", // not required
        visitor: {
            ImportDefaultSpecifier(path){
                if(path.node.local.name !== "Boop"){
                    return;
                }
                path.node.local.name = "Boop";
            },
            JSXElement(path){
                if(path.node.openingElement.name.name !== "Button"){
                    return;
                }
                path.node.openingElement.name.name = "Boop";
            }
        },
    };
}
```

## A Better Way
What if the Button was used as `<Button></Button>`? The above plugin will fail. The new plugin -
```js
function babelPlugin(babel){
    const {types: t} = babel;
    return {
        name: "ast-transform", // not required
        visitor: {
            ImportDefaultSpecifier(path){
                if(path.node.local.name !== "Button"){
                    return;
                }
                const uses = path.scope.getBinding("Button").referencePaths;
                path.node.local.name = "Boop";
                uses.forEach(refPath => {
                    if(t.isJSXIdentifier(refPath) || t.isIdentifier(refPath)){
                        refPath.node.name = "Boop";
                        return;
                    }
                })
            },
        },
    };
}
```

## Things to know
* What is `path`? - Path is an object that contains the node and comes with other information. And to go to the node do `path.node`.

## References
1. :tv: [Build Your Own Babel Plugin (with Laurie Barth) — Learn With Jason](https://www.youtube.com/watch?v=aK6n0pYcOe8)

## Useful Links
1. [@babel/types](https://babeljs.io/docs/en/babel-types)
