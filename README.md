# Watch.js 1.2.0 [Download](https://raw.github.com/melanke/Watch.JS/master/src/watch.js)

## About

`watch` is a small library that brings a lot of possibilities. You may know the "Observer" design pattern involves executing some function when an observed object changes. Other libraries exist that do this, but with `watch` you will not have to change the way you develop. Take a look at the examples to see how simple it is to add `watch` to your code.

## Installation

		component install timoxley/watch

## Usage

```js
var watch = require('watch').watch
var unwatch = require('watch').unwatch

var obj = {}
watch(obj, function(){
	alert("obj changed!");
});

obj.name = 'test'

```

## Observe the changes of one object attribute

```javascript

//defining our object however we like
var ex1 = {
	attr1: "initial value of attr1",
	attr2: "initial value of attr2"
};

//defining a 'watcher' for an attribute
watch(ex1, "attr1", function(){
	alert("attr1 changed!");
});

//when changing the attribute its watcher will be invoked
ex1.attr1 = "other value";
```

## Observe the changes of more than one object attribute

```javascript
//defining our object however we like
var ex2 = {
    attr1: 0,
    attr2: 0,
    attr3: 0
};

//defining a 'watcher' for the attributes
watch(ex2, ["attr2", "attr3"], function(){
    alert("attr2 or attr3 changes!");
});

//when changing one of the attributes its watcher will be invoked
ex2.attr2 = 50;
```

## Observe the changes of all attributes of the object

```javascript
//defining our object however we like
var ex3 = {
    attr1: 0,
    attr2: "initial value of attr2",
    attr3: ["a", 3, null]
};

//defining a 'watcher' for the object

watch(ex3, function(){
    alert("some attribute of ex3 changes!");
});

//when changing one of the attributes of the object the watcher will be invoked
ex3.attr3.push("new value");
```

## Remove a Watcher

```javascript
var obj = {
    phrase: "hey",
    name: "buddy",
    alert: function(){
        alert(obj.phrase + " " + obj.name);
    },
    alert2: function(){
        alert(obj.name + ", " + obj.phrase);
    }
}

watch(obj, "name", obj.alert);
watch(obj, "name", obj.alert2);

obj.name = "johnny";

unwatch(obj, "name", obj.alert);

obj.name = "phil";
```

## More information about the change

```javascript
//defining our object no matter which way we want
var ex1 = {
    attr1: "initial value of attr1",
    attr2: "initial value of attr2"
};

//defining a 'watcher' for an attribute
watch(ex1, "attr1", function(prop, action, newvalue, oldvalue){
    alert(prop+" - action: "+action+" - new: "+newvalue+", old: "+oldvalue+"... and the context: "+JSON.stringify(this));
});

//when changing the attribute its watcher will be invoked
ex1.attr1 = "other value";
```

## Don't worry about the Infinite Loop

If you don't want to call a second watcher in the current scope just set WatchJS.noMore to true and it will be reset to false when this watcher finishes.

```javascript
//defining our object however we like
var ex1 = {
    attr1: "inicial value of attr1",
    attr2: "initial value of attr2"
};

//defining a 'watcher' for an attribute
watch(ex1, "attr1", function(){
    WatchJS.noMore = true; //prevent invoking watcher in this scope
    ex1.attr2 = ex1.attr1 + " + 1";
});

//defining other 'watcher' for another attribute
watch(ex1, "attr2", function(){
    alert("attr2 changes");
});


ex1.attr1 = "other value to 1"; //attr1 will be changed but will not invoke the attr2`s watcher
```

## How deep you wanna go? Provide a level of children

```javascript
//defining our object no matter which way we want
var ex = {
    //level 0
    l1a: "bla bla",
    l1b: {
        //level 1 or less
        l2a: "lo lo",
        l2b: {
            //level 2 or less
            deeper: "so deep"
        }
    }
};

watch(ex, function(){
    alert("ex changes at lvl 2 or less");
}, 1);

watch(ex, function(){
    alert("ex changes at lvl 3 or less");
}, 2);


ex.l1b.l2b.deeper = "other value";


ex.l1b.l2b = "other value";
```

## Chill out, no surprises, only expected attributes will be considered

After declaring a watcher for some object, when you add new attributes to this object and/or change it, the watcher will not be invoked. If you want the new attributes to be observed you need to specify the name of this new attributes.

```javascript
//defining our object however we like
var ex6 = {
    attr1: 0,
    attr2: 1
};

//defining a 'watcher' for the object
watch(ex6, function(){
    alert("some attribute of ex6 changes!")
});

ex6.attr3 = null; //no watcher will be invoked
ex6.attr3 = "value"; //no watcher will be invoked
```

An example how to define a watcher of an undefined attribute

```javascript
//defining our object however we like
var ex6 = {
    attr1: 0,
    attr2: 1
};

//defining a 'watcher' for the specific attribute
watch(ex6, "attr3", function(){
    alert("attr3 changes")
});

ex6.attr3 = "value"; //watcher will be invoked
```

## Invoke the watcher anytime you want

```javascript
//defining our object however we like
var ex7 = {
    attr1: 0,
    attr2: 1
};

//defining a 'watcher' for the object
watch(ex7, function(){
    alert("some attribute of ex6 changes!")
});

callWatchers(ex7, "attr1"); //invoke the watcher
```

## Compatible with JQuery

```javascript
$(function(){

    var obj = {cont: 0};

    watch(obj, "cont", function(){
        alert("obj.cont = "+obj.cont);
    });

    $("#button").click(function(){
        obj.cont++;
    });
});
```
## Different ways to build Classes/Objects and use Watch.JS

```javascript
//open the browser log to view the messages

var Apple = function(type) {
    var _thisApple = this;
    this.type = type;
    this.color = "red";

    this.getInfo = function() {
        return this.color + ' ' + this.type + ' apple';
    };

    watch(this, function(){
        console.log("although we are using Watch.js the apple structure remains the same");
        for(var i in _thisApple){
            console.log(i+": "+_thisApple[i]);
        }
    });
};


var apple = new Apple("macintosh");
apple.type = "other";


var Banana = function(type) {
    var _thisBanana = this;
    this.type = type;
    this.color = "yellow";

    watch(this, function(){
        console.log("although we are using Watch.js the banana structure remains the same");
        for(var i in _thisBanana){
            console.log(i+": "+_thisBanana[i]);
        }
    });
};

Banana.prototype.getInfo = function() {
    return this.color + ' ' + this.type + ' banana';
};

var banana = new Banana("Cavendish");
banana.type = "other";

var orange = {
    type: "pocan",
    color: "orange",
    getInfo: function () {
        return this.color + ' ' + this.type + ' apple';
    }
};

watch(orange, function(){
    console.log("although we are using Watch.js the orange structure remains the same");

    for(var i in orange){
        console.log(i+": "+orange[i]);
    }
});

orange.type = "other";
```

##Compatible with all browsers
But still with some bugs with older browsers

## License

MIT


