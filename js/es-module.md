## es module 语法

1. export 关键字：具名导出，导出的东西必须有名字。在任何可以导出的东西前面加export不改变这条语句原本的任何含义
2. export default 默认导出：导出一个值，其后跟表达式
3. 每个模块自身就有一个作用域，其内通过var/let/const创建的变量处于模块作用域内
4. 当要导出一个已经存在的变量为具名导出时：export {jQuery}
5. 通过import语法创建的变量相当于const创建的，所以不赋值，不重复创建相同名字的东西
6. 导入具名导出：import {chunk, compact} from './lodash.js'
7. 导入默认导出：
```
import confetti from './confetti.js' 
import lodash from './lodash.js'
```
8. 导入某个模块的全部导出：
```
import * as xxx from './xxx.js'
这种语法下，xxx是一个“模块对象”
xxx.named 为相应具名导出
xxx.default 为它的默认导出
```
9. 动态导入：
```
import还可以做为一个函数，接收一个模块路径，动态导入相应的模块
动态导入由于是一个普通的函数调用，所以它可以放在代码的任何位置，如if，for，或函数里
var lodashMod  = await import('https://esm.sh/lodash')
上面的语法得到的对象跟下面语法得到的lodashMod对象是相同的
import * as lodashMod from 'https://esm.sh/lodash'
```
