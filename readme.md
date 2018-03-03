# compatible-vue-socket.io


本模块模仿了vue-socket.io写了一个兼容ie的vue插件。
由于vue-socket.io采用了Proxy的方法导致市面上所有ie都不兼容，同时也修复了该模块存在的一个问题。
由于不是采用Proxy方法，所以该插件没办法动态添加和删除socket事件，只能在一开始就订阅socket事件。
本模块还修复了mutations的socket事件会把服务器数据转成数组的bug（比如服务器推送数据：'你好'给客户端，moutations会收到['你好'],而actions却会正常接收'你好'）。

## Install

``` bash
npm install compatible-vue-socket.io --save
```

## Usage
#### Configuration
Automatic socket connection from an URL string
``` js
import VueSocketio from 'compatible-vue-socket.io';
Vue.use(VueSocketio, 'http://socketserver.com:1923');
```

Bind custom socket.io-client instance
``` js
Vue.use(VueSocketio, socketio('http://socketserver.com:1923'));
```

Enable Vuex integration
``` js
import store from './yourstore'
Vue.use(VueSocketio, socketio('http://socketserver.com:1923'), store);
```

#### On Vuejs instance usage
``` js
var vm = new Vue({
  sockets:{
    connect: function(){
      console.log('socket connected')
    },
    customEmit: function(val){
      console.log('this method was fired by the socket server. eg: io.emit("customEmit", data)')
    }
  },
  methods: {
    clickButton: function(val){
        // $socket is socket.io-client instance
        this.$socket.emit('emit_method', val);
    }
  }
})
```


#### Vuex Store integration

Socket **mutations** always have `SOCKET_` prefix.

Socket **actions** always have `socket_` prefix and the socket event name is `camelCased` (ex. `SOCKET_USER_MESSAGE` => `socket_userMessage`) 

You can use either one or another or both in your store. Namespaced modules are supported.

``` js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex);

export default new Vuex.Store({
    state: {
        connect: false,
        message: null
    },
    mutations:{
        SOCKET_CONNECT: (state,  status ) => {
            state.connect = true;
        },
        SOCKET_USER_MESSAGE: (state,  message) => {
            state.message = message;
        }
    },
    actions: {
        otherAction: (context, type) => {
            return true;
        },
        socket_userMessage: (context, message) => {
            context.dispatch('newMessage', message);
            context.commit('NEW_MESSAGE_RECEIVED', message);
            if (message.is_important) {
                context.dispatch('alertImportantMessage', message);
            }
            ...
        }
    }
})
```

## Example
[demo](https://github.com/lth707/compatible-vue-socket.io-demo)


