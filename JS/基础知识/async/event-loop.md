### how JS execute

- from the top to the end, one line to next line
- if one line of code throw error, the execution will stop
- syn go first, then async

### event loop

- 同步代碼，一行一行放在`call stack`執行
- 遇到異步，先記錄下，等待時機
- 時機到了，移到`callback queue`
- 如果`call stack`為空， `event loop` start
- 輪詢`callback queue`，如有則移到`call stack`
- 繼續輪詢查找

![event lop](../assets/event%20loop.png)
