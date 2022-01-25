锁
nodejs的多线程同步锁
主线程操作
```js
let Lock = require('./lock');
let { Worker } = require('worker_threads');
const sharedBuffer = new SharedArrayBuffer(1 * Int32Array.BYTES_PER_ELEMENT);
const sharedArray = new Int32Array(sharedBuffer);
for (let i = 0; i < require('os').cpus().length; i++) {
    let worker = new Worker('./worker-thread.js', {
        workerData: sharedBuffer
    });
}
Lock.initialize(sharedArray, 0);  //初始化数据
const lock = new Lock(sharedArray, 0); //得到实例对象

lock.lock();  //获取锁
// 3s后释放锁
setTimeout(() => {
    lock.unlock(); // (B) // 释放锁
}, 3000)
```

子线程
```js
let Lock = require('./lock');
const fs = require('fs')
let { parentPort, workerData, threadId } = require('worker_threads');
const sharedArray = new Int32Array(workerData);
const lock = new Lock(sharedArray, 0);

console.log('Waiting for lock...'); // (A)
// 获取锁
lock.lock(); // (B) blocks!

console.log('Unlocked:' + threadId); // (C)
lock.unlock() //释放
```
