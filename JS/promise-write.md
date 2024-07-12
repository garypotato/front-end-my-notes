```js
class MyPromise {
	state = 'pending';
	value = undefined;
	reason = undefined;

	resolveCallbacks = []; // pending状态下，存储成功的回调
	rejectCallbacks = []; //pending状态下，存储失败的回调

	constructor(fn) {
		const resolveHandler = (value) => {
			if (this.state === 'pending') {
				this.state = 'fulfilled';
				this.value = value;
				this.resolveCallbacks.forEach((fn) => fn(this.value));
			}
		};

		const rejectHandler = (reason) => {
			if (this.state === 'pending') {
				this.state = 'rejected';
				this.reason = reason;
				this.rejectCallbacks.forEach((fn) => fn(this.reason));
			}
		};

		try {
			fn(resolveHandler, rejectHandler);
		} catch (err) {
			rejectHandler(err);
		}
	}

	then(fn1, fn2) {
		fn1 = typeof fn1 === 'function' ? fn1 : (v) => v;
		fn2 = typeof fn2 === 'function' ? fn2 : (e) => e;

		if (this.state === 'pending') {
			const p1 = new MyPromise((resolve, reject) => {
				this.resolveCallbacks.push(() => {
					try {
						const newValue = fn1(this.value);
						resolve(newValue);
					} catch (err) {
						reject(err);
					}
				});

				this.rejectCallbacks.push(() => {
					try {
						const newReason = fn2(this.reason);
						reject(newReason);
					} catch (err) {
						reject(err);
					}
				});
			});

			return p1;
		}

		if (this.state === 'fulfilled') {
			const p1 = new MyPromise((resolve, reject) => {
				try {
					const newValue = fn1(this.value);
					resolve(newValue);
				} catch (err) {
					reject(err);
				}
			});

			return p1;
		}

		if (this.state === 'rejected') {
			const p1 = new MyPromise((resolve, reject) => {
				try {
					const newReason = fn2(this.reason);
					resolve(newReason);
				} catch (err) {
					reject(err);
				}
			});

			return p1;
		}
	}

	catch(fn) {
		return this.then(null, fn);
	}
}

MyPromise.resolve = function (value) {
	return new MyPromise((resolve, reject) => resolve(value));
};
MyPromise.reject = function (reason) {
	return new MyPromise((resolve, reject) => reject(reason));
};
MyPromise.all = function(promiseList = []){
    const p1 = new MyPromise((resolve,reject)=> {
        const result = []
        const length = promiseList.length
        let resolvedCount = 0

        promiseList.forEach(p=>{
            p.then(data =>{
                result.push(data)

                //resolvedCount必须在then里面做++
                //不能用index
                resolvedCount++
                if(resolvedCount === length){
                    //已经遍历到了最后一个promise
                    resolve(result)
                }
            }).catch(err) => {
                reject(err)
            }
        })
    })
    return p1
}
MyPromise.race=function(promiseList = []){
    let resolved = false
    const p1 = new Promise((resolve,reject) =>{
        promiseList.forEach(p =>{
            p.then(data =>{
                if(!resolved){
                    resolve(data)
                    resolved=true
                }
            }).catch(err =>{
                reject(Err)
            })
        })
    })
    return p1
}
```
