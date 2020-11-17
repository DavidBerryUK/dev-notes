 # Vuex
## Introduction

Vuex is a statement management library that manages the data used though out a Vue Single Page Application. 

All data is stored in one central place and all components can access it.

This document also covers the use of **mapActions** and **mapGetters** which assist with accessing Vuex in a more succinct fashion.

# Overview of Vuex Service
Defines and stores data for the application
```javascript
//
// Extract from Vuex 
//
export default new Vuex.Store({
    state: {},
    mutations: {},
    actions: {},
    modules: {},
    getters: {}
});
 ```

## State
Defines and stores data for the application. These can be items simples as strings, or complex objects.
```javascript
//
// Extract from Vuex 
//
state: {
    message: 'Hello World',
    buttonCounter: 0,
    accountName: 'John Smith',
    personList : [PersonModel],
    personCurrent: PersonModel,
    lookUpListGender:[GenderModel],
},
 ``` 

## Mutations (synchronous)
Changes the state of the data, either adding, updating or deleting.
 
Note that the current state is passed into the method, along with the payload parameter. This ensures that the state is always updated using the current state value. The payload payload parameter is passed in by the calling component. In the incrementButtonCount example, this is the number to increment the button count by.

methods in the mutations object are accessed by using the commit command.

**Note that it is good practice not to access Mutation directly from components in your application, but to use Actions, actions then use the mutations methods.**

```javascript
//
// Extract from Vuex 
//
mutations: { 
    incrementButtonCount(state, payload) {
        state.count+=payload;
    }
}

// access in Vue project using the follow syntax
this.$store.commit('incrementButtonCount',10)
 ```

Create a list of function to update the state

*Operations are synchronous*

## Actions (asynchronous)
this should be used for REST API calls as *Operations are asynchronous*
Actions call methods in the mutations section using the commit method and do not directly manipulate the store.

```javascript
//
// Extract from Vuex 
//
actions: {
    incrementButtonCountAsync(state,payload) {
        // call the method in mutations
        this.$state.dispatch('incrementButtonCount',payload)
    }
}
 ```

Create a list of async function to update the state. 
The state has to be committed.
the actions are collect from code using the dispatch command.

## Modules
Modules breaks down the Vuex into seperate modules to facilitate good code organisation as the application grows. This will be documented in a seperate document as its a larger subject.

```javascript
//
// Extract from Vuex 
//
modules: {
    
}
 ```

## Getters
Obtains data from Vuex to be displayed / read by the application. While the application read directly from the state, forcing access via getters encapsulates any logic and ensure consistency and thus reduction in chance of bugs.

```javascript
//
// Extract from Vuex 
//
getters: {
    accountName(state) {
        return this.$state.accountName;
    },
    accountNameUpper(state) {
        return this.$state.accountName.toUpperCase();
    },
    buttonCount(state) {
        return this.$state.buttonCounter;
    }
}
 ```

# Using Vuex in the Application

## Get data from Vuex
Use computed methods to obtain the current data in the Vuex object

```javascript
//
// extract from Vue Component
//
{
    computed: {
        accountName() {
            return this.$store.getters.getAccountName;
        }
        // alternative syntax
        accountName => this.$store.getters.getAccountName;
        },
        buttonCounter => this.$store.getters.buttonCounter;
    }
    
}
 ```

# Using and accessing Vuex from your Application

 ## Set data in Vuex synchronously
Uses the **mutations** on Vuex
```javascript
//
// extract from Vue Component
//
{
    methods: {

        // call mutations to increment button press by 1
        incrementBy1ButtonPressed() {
            this.$store.commit('incrementButtonCount',1);
        }

        // call mutations to increment button press by 10
        incrementBy10ButtonPressed() {
            this.$store.commit('incrementButtonCount',10);
        }

        
    }
}
 ```

## Set data in  Vuex asynchronously
Uses the **actions** on Vuex and is called using the dispatch method
```javascript 
//
// extract from Vue Component
//
{
    methods: {
        // set data asynchronously
        setAccountNo() {
            this.$store.dispatch("setAccountName",{name : "John Smith"})
        }
        
        // Call action to increment button count
        incrementButtonPress(incrementCount) {
            this.$store.commit('incrementButtonCountAsync',incrementCount)
        }
    }

}
 ```

# Map Actions Vuex Helper
Enables clear and shorter syntax for communicating with Vuex
```javascript 
//
// extract from Vue Component
//
import {mapActions, mapGetters} from "vuex";

export default
{
    methods: {
        ...mapActions(
            [
               'setAccountName' 
            ]
        )
        setAccountNo() {
            // this is replaced by 
            this.$store.dispatch("setAccountName",{name : "John Smith"})
            // this shorter version, still async
            this.setAccountName({name : "John Smith"})
        }
    }
    computed : {
        // this is replaced by
        accountName() {
            return this.$store.getters.getAccountName
        }
        // this...
        ...mapGetters({accountName:'getAccountName'})        
    }

}
 ```
# Top Vuex tips

## Use ES6 Arrow functions
Use es6 arrow functions to simplify and reduce the noise in the code, Especially suited to simple function. More complex methods may still benefit from the long format. Though if the method is too long, is the code in the correct place?

```javascript
//
// BEFORE
//
mutations: {
    setCount(state,payload) {
        state.counter = payload;
    }
},
getters: {
    getCounter(state) {
        return state.counter;
    }
}
```
```javascript
//
// AFTER
//
mutations: {
    setCount: (state,payload) => (state.counter = payload)
}
getters: {
    getCounter: (state) => state.counter
}
``` 

## Use Computed properties rather than manually refreshing local state
```javascript
//
// BEFORE
//
export default {
    mounted() {
        this.counter = this.$store.getters.getCounter;
    },
    date: (()=> ({counter: 0})),
    methods: {
        increments:() {
            this.$store.commit('setCounter', this.counter+1);
            this.counter = this.$store.getters.getCounter;
        }
    }

```
```javascript
//
// AFTER
//
export default {
    
    methods: {
        increments:() {             
            // note that this method has changed, you should not
            // set the value directly e.g. counter = counter + 1
            // but amend the mutation to add 1 to the counter value
            // held by the state, as the local counter value may 
            // out of date
            this.$store.commit('setCounter');    
        }
    },
    computed: {
        counter() {
            return this.$store.getters.getCounter;
        }
    }
}
``` 
## Use constants for Mutation Types
As applications grow, the number of mutations grows there is increasing error of mistyping names. 
Fundamentally we want to remove any type of 'magic string' from the application.

Also placing mutations in a single constant file allows collaborators to get at a 
glance view of what mutations are possible in the entire application.

```javascript
mutation-types.js

export const SET_COUNTER = "SET_COUNTER";
export const GET_COUNTER = "GET_COUNTER";
```

```javascript
index.js

import {GET_COUNTER, SET_COUNTER} from './mutation-types'

export default new Vuex.Store({
    state:{
        counter:0
    },
    mutations: {
        // old
        //setCounter: state => state.counter++;
        [SET_COUNTER]: state => state.counter++;
    },
    getters: {
        // old
        //getCounter: state => state.counter;
        [GET_COUNTER]: state => state.counter;
    }
})
```
```javascript
home.vue

import {GET_COUNTER, SET_COUNTER} from './mutation-types'
export default {
    methods: {
        increment:() {
            // old magic string
            //this.$store.commit("set_counter");
            this.$store.commit(SET_COUNTER);
        }
    },
    computed: {
        counter() {
            // old 
            // return this.$store.getters.getCounter
            return this.$store.getters[GET_COUNTER];
        }
    }
}

```
## Use constants for Mutation Types with Vuex Helpers
This uses the example above and introduces Vuex helpers to cut down 
on code and potential errors with the use of strongly defined mutation names.
```javascript
home.vue

import {GET_COUNTER, SET_COUNTER} from './mutation-types'
import {mapGetters, mapMutations} from 'vuex'

export default {
    methods: {
        ...mapMutations:({
            increment:SET_COUNTER
        })        
    },
    computed: {
        counter() {          
            ...mapGetters({
                counter:GET_COUNTER
            })            
        }
    }
}

```


# YouTube Tutorials
 
## learn Vuex in 10 minutes - plain java script
this is a basic increment counter button 

https://www.youtube.com/watch?v=LW9yIR4GoVU

## Dad Jokes - plain java script
Uses a REST Api to fetch async data

https://www.youtube.com/watch?v=oxUyIzDbZts

## break down into modules - plain java script
https://www.youtube.com/watch?v=OL1jNfln5OE

## Vuex Top Tips
https://www.youtube.com/watch?v=_6titBG_Xp0

## Vue Vuex Typescript, preview of Vue3
https://www.youtube.com/watch?v=V9MmoBAezD8