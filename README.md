RavenCore - is a bot that can be programmed in `JavaScript`. Just add the bot to your server, add a command, and program it.

---

Terminology:
- index - the initial file that executes the command
- action - a file where you write code
- member - a server member
- guild - the server itself and everything related to it
- channel - the channel where the message was sent or any other channel on the server
- owner - the server owner

## ACTION
For one command, you can use only 1 action, `index` cannot be changed or deleted.
You can also add other actions with their own names (they can be deleted).
In them, you can move part of the code to break it into chunks and not clutter `index`.
Use `$import("actionName")` to import code from another action, but in that action there must be `return someData`.
An action is an asynchronous function, so you can use `await` and `return` at the top level.

---

## VARIABLES:

Full text of the command `string`
```js
$content // '!foo 1 2 3'
```

The current command being called `string`
```js
$cmd // 'foo'
```

Parameters that follow the command `array`
```js
$args // ['1', '2', '3']
```

User data `object`
```js
$member
```

Channel data `object`
```js
$channel
```

Server data `object`
```js
$guild
```

Current message data `object`
```js
$message
```



## FUNCTIONS:

### Sending a message

To send a regular message, use
```js
$send.message("hello")
```

To reply
```js
$send.reply("hello")
```
or you can simply use
```js
return "hello"
```
If you return something from the action, it will reply. If it's falsy, nothing will be sent.
You can also send it together with embeds and components
```js
$send.reply("hello", [[btn]], { embeds: [embed] })
// or
return [ "hello", [[btn]], { embeds: [embed] } ]
```
Using $send.message() or $send.reply() you can wait for the message to be sent, and then do something with it, for example, you can edit or delete it
```js
let msg = await $send.reply("hello")
await $timeout(2) // wait 2 seconds
msg.$edit("bye")
```
*you can use await at the top level, because the action is an asynchronous function
This code will send a reply "hello", and after 2 seconds, change it to "bye"
Available methods are `msg.$edit()`, `msg.$delete()` and `msg.$reply()`



### $arguments
Command arguments, i.e. additional values after !cmd arg1 arg2 ... argN
This is essentially the same as $args but more convenient, for example a command !sum 2 3
```js
let a1 = $arguments.get(1)
let a2 = $arguments.get(2)

a1 + a2 // 5
```
We got 5 because the function $arguments.get(N) converts to a number if possible, otherwise it will be a string
There are some other additional functions, if the command is !test 123 hello 555 foo
```js
$arguments.range(2, 3) // ["hello", "555"]
$arguments.before(2)  // ["123", "hello"]
$arguments.after(2)  // ["hello", "555", "foo"]
```

### $timeout
Simply pauses the code for a given time, from 1s to 60s
```js
$send.message("hello")
await $timeout(5) 
$send.message("5 seconds have passed")
```

### $button
You can create a button like this
```js
let btn = $button({
    text: "Say hello",
    action: ({ $reply }) => $reply("hello")
})

$send.message("test", [[btn]])
```
After that, you will see the message "test" with a button "Say hello", if you click on it, the bot will reply "hello"
`[[btn]]` Buttons are written in a 2-dimensional array, because you can make multiple rows

```js
[
    [btn1, btn2, btn3],
    [btn4, btn5, btn6],
]
```
As you saw, the button has a parameter `action`, this is the code that will be executed when the button is clicked, you can pass a function or a string to it.
- `action: "actionName"` If you pass a string, this is an automatic import from another action, the same as `action: $import("actionName")`, so in `actionName` you need to return a function
```js
// actionName
return ({ $reply }) => $reply("the button is pressed")
```
- Or you can simply pass a function
```js
action: ({ $reply }) => $reply("the button is pressed")
```


### $import
Imports data from another file and optionally passes parameters
!!! Warning. When importing a file, all the code in it will be executed.
It is recommended to use only functions and variables to avoid issues

```js
// index
let { foo } = $import("file123", { num: 100 })

foo // 200
```

```js
// file123
return {
    foo: $params.num * 2
}
```



### $random
Get a random number or array element
```js
$random(10) // From 0 to 10
$random(5, 15) // From 5 to 15
$random([1, 2, 3, 4, 5]) // From an array
$random(['a', 'b', 'c'], [20, 50, 30]) // With probability in %
```

### $range
Create a range, i.e. an array, convenient to use in for of
```js
$range(5) // [1, 2, 3, 4, 5]
$range(2, 6) // [2, 3, 4, 5, 6]
$range(1, 10, 2) // [1, 3, 5, 7, 9]
```





## STORAGE

The storage is a simple key-value database {key: value}

Get data about the current user
```js
await $store.member('money').get() // 900
```

Get global data relative to the server
```js
await $store.global('money').get() // 300
```

Changing data (**The same for member and global below**)
```js
await $store.global('money').set(500) // update the value (if the field does not exist, it will be created)
await $store.global('money').set(null) // delete the value (if the field does not exist, it will return false)
```

### Number change operation
```js
// add to the current value
await $store.global('money').inc(50) // If it was 500, it will become 550

// subtract from the current value
await $store.global('money').dec(50) // If it was 500, it will become 450
```

### Array modification
```js
// add a new value to the 'roles' array
await $store.global('roles').push('admin')

// remove the value 'admin' from the 'roles' array
await $store.global('roles').delete('admin')

// replace 'admin' with 'user' in the 'roles' array
await $store.global('roles').replace('admin', 'user')

// check if 'user' is in the 'roles' array, returns true or false
await $store.global('roles').has('user')

```


### !!! mention is under development, so it is not available yet

Get data about the mentioned user by index
```js
await $store.mention(0, 'money').get() // 800
await $store.mention(1, 'money').get() // 500
```

Update data of the mentioned user
```js
await $store.mention(0, 'money').set(200) // true if updated successfully
await $store.mention(1, 'money').set(400) // false if updated successfully
```

Get data of each mentioned user according to the array (read-only)
```js
await $store.mention(['money', 'money']).get() // [200, 400]
```

