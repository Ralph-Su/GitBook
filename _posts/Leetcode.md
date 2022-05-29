# 数学

## **7. 整数反转**

```scala
object Solution {
  def reverse(x: Int): Int = {
    var (myX, rev, pop) = (x, 0, 0)
    while (myX != 0) {
      pop = myX % 10
      myX = myX / 10
      if (rev > Integer.MAX_VALUE / 10 || (rev == Integer.MAX_VALUE && pop > 7)) 
          return 0
      if (rev < Integer.MIN_VALUE / 10 || (rev == Integer.MIN_VALUE && pop < -8)) 
          return 0
      rev = rev * 10 + pop
    }
    rev
  }
}
```

# 二分

## **35. 搜索插入位置**

题解：有序则二分。

```scala
object Solution {
  def searchInsert(nums: Array[Int], target: Int): Int = {
    var (left, right) = (0, nums.length - 1)
    target match {
      case _ if target > nums(right) => return right + 1
      case _ if target <= nums(left) => return left
      case _ => {
        while (left <= right) {
          var mid = left + ((right - left) >> 1)
          if (target <= nums(mid) && target > nums(mid - 1))
            return mid
          else if (target < nums(mid))
            right = mid - 1
          else
            left = mid + 1
        }
      }
    }
    0
  }
}
```

## **剑指 Offer 53 - I. 在排序数组中查找数字 I**

题解：有序则二分。次数算法是在搜索的数字以后需要再往前和往后查找是否有相同元素。

```scala
def search(nums: Array[Int], target: Int): Int = {
    if (nums.length == 0) return 0
    var (left, right) = (0, nums.length - 1)
    var count = 0
    while (left <= right) {
      var mid = left + ((right - left) >> 1)
      nums(mid) match {
        case x if x == target => {
          count += 1
          if (mid > left) {
            var i = mid - 1 // 向左搜索
            while (i >= left && nums(i) == target) {
              count += 1
              i -= 1
            }
          }
          if (mid < right) {
            var j = mid + 1 // 向右搜索
            while (j <= right && nums(j) == target) {
              count += 1
              j += 1
            }
          }
          return count
        }
        case x if x > target => right = mid - 1
        case x => left = mid + 1
      }
    }
    count
  }
```

> **位运算**
>
> 当left和right足够大的时候相加会溢出。更好的写法是mid=left+(right-left)/2，计算机的除法实现是复杂的，我们可以用右移一位的方式来实现除以2，那么性能最优的写法是mid=left+((right - left) >> 1)。

## **面试题 10.05. 稀疏数组搜索**

题解：有序则二分。当遇到空字符串的时候mid往后移动，直到mid等于right。如果移动后的mid等于right,那么把right直接压缩到移动前的mid，再结束当前循环，因为后面都是空字符串。需要注意scala里breakable和break的用法。

```scala
object Solution {  
    import scala.util.control.Breaks.{break, breakable}  
    def findString(words: Array[String], s: String): Int = {    
        var (left, right) = (0, words.length - 1)
    while (left <= right) {      
        breakable {        
            var mid = left + ((right - left) >> 2)        
            var tmp = mid        
            while (mid <= right && words(mid).equals("")) {
                mid = mid + 1        
            }
        if (mid == right + 1) {          
            right = tmp - 1          
            break        
        }
        s.compareTo(words(mid)) match {          
            case x if x == 0 => return mid          
            case x if x > 0 => left = mid + 1          
            case _ => right = mid - 1        
        }      
        }    
    }    
        -1  
    }
}
```

# 集合

## **剑指 Offer 57. 和为s的两个数字**

题解：熟悉Set的特性：由哈希表实现的无序、无重合的集合。

```scala
def twoSum(nums: Array[Int], target: Int): Array[Int] = {
  var set = mutable.Set[Int]()
  for (i <- nums)
    if (set.contains(target - i)) return Array(i, target - i) else set += i
  Array(-1, -1)
}
```

# 映射

## **1. 两数之和**

题解：熟悉Map的特性，Map可以理解为*k*ey: Set + value: Array的数据结构。

```scala
object Solution {
    def twoSum(nums: Array[Int], target: Int): Array[Int] = {
        if (nums.length == 2) return Array(0,1)
        import scala.collection.mutable.Map
        var map :Map[Int,Int] = Map()
         for (i <- nums.indices) {
             if (map.contains(target - nums(i))) {
                 return Array(map(target - nums(i)),i)
             }
             map += (nums(i)->i)
         }
         Array(0,0)
    }
}
```

# 数组

## **56. 合并区间**

```scala
object Solution {   
    import scala.collection.mutable.ArrayBuffer   
    def merge(intervals: Array[Array[Int]]): Array[Array[Int]] = {    
        if (intervals.length <= 1) return intervals    
        var result = new ArrayBuffer[Array[Int]]() // 初始化结果    
        var initIntervals = intervals.sortBy(_.head) // 对数组按照第一个元素进行排序    
        var target = initIntervals.head
    for (element <- initIntervals) {      
        if (element.head > target.last) {        
            // 两个数组不相交        
            result.append(Array(target.head, target.last))        
            target = element      
        } else {        
            // 两个数组相交        
            target = Array(target.head, Math.max(element.last, target.last))      
        }    
    }    
        result.append(target)    
        result.toArray  
    }
}
```

## **485. 最大连续 1 的个数**

题解：遍历所有元素，用maxLength记录当前最大连续个数，lastMaxLength记录上次最大的连续个数，遍历到1则maxLength + 1，遍历到0则判断当前maxLength和lastMaxLength的大小，取较大的一个。遍历完成以后，再次对maxLength和lastMaxLength比较，返回较大的一个。

```scala
def findMaxConsecutiveOnes(nums: Array[Int]): Int = {
        var maxLength = 0
        var lastMaxLength = 0
        for (i <- nums.indices) {
            if(nums(i) == 1) {
                maxLength += 1
            }    
            else {
                lastMaxLength = Math.max(lastMaxLength,maxLength)   
                maxLength = 0
            }
        }
        Math.max(lastMaxLength,maxLength)
}
```

## **648. 单词替换**

题解：将句子拆分到集合里，遍历每一个单词检查是不是以列表中的词根开头，如果是就替换掉。

```scala
def replaceWords(dictionary: List[String], sentence: String): String = {
  var arr: Array[String] = sentence.split(" ")
  var newSentence: StringBuilder = new StringBuilder()
  for (str <- arr) {
    var curRoot: String = ""
    for (root <- dictionary) {
      if (str.startsWith(root)) {
        root match {
          case _ if curRoot == "" => curRoot = root
          case _ if root.length < curRoot.length => curRoot = root
          case _ =>
        }
      }
    }
    if (curRoot != "") newSentence.append(curRoot + " ") else newSentence.append(str + " ")
  }
  newSentence.toString().trim
}
```

## **剑指 Offer 59 - I. 滑动窗口的最大值**

题解：暴力法先遍历每个元素 0 → (k-1)，再遍历窗口找出最大的元素。

```scala
// 暴力法
def maxSlidingWindow(nums: Array[Int], k: Int): Array[Int] = {
  if (nums.length == 0) return Array()
  val step = k - 1
  val result = ArrayBuffer[Int]()
  for (i <- 0 until nums.length - step) {
    var curArray = ArrayBuffer[Int]()
    for (j <- 0 to step)
      curArray.append(nums(i + j))
    var maxValue = curArray.toArray.max
    result.append(maxValue)
  }
  for (item <- result.toArray)
    println(item)
  result.toArray
}
```

# 栈

## 20.有效的括号

题解：当符号等于左括号直接入栈，若栈顶与当前元素匹配就出栈，最后判断栈是否为空。用ArrayBuffer来实现也可，但是性能不如栈。

```scala
object Solution {
    def isValid(s: String): Boolean = {
      import scala.collection.mutable.Stack
      import scala.collection.mutable.Map
      if (s.length == 1) return false
      var stack = Stack[Char](s(0))
      var strMap = Map('}'->'{',']'->'[',')' -> '(')  
       for (i <- 1 until s.length) {
           if (stack.isEmpty || s(i) == '[' || s(i) == '(' || s(i) == '{')
               stack.push(s(i))
           else
               if (stack.top == strMap(s(i)))  stack.pop() else  stack.push(s(i))
       }
       return stack.isEmpty
    }
}
```

# **链表**

## **2. 两数相加**

题解：遍历两个链表，逐位相加，定义变量carry记录是否需要进位。相加计算公式：sum = x + y + carry，进位制carry=sum / 10, 相加后当前位的结果为sum%10。当两个链表都遍历完成后，需要判断carry是否为1，若为1则还需继续进位。

```scala
/**
 * Definition for singly-linked list.
 * class ListNode(_x: Int = 0, _next: ListNode = null) {
 *   var next: ListNode = _next
 *   var x: Int = _x
 * }
 */
object Solution {
    def addTwoNumbers(l1: ListNode, l2: ListNode): ListNode = {
        var dummaryHead = ListNode(0)
        var curr = dummaryHead 
        var (left,right) = (l1,l2)
        var carry = 0
        while (left != null || right != null) {
            var x = if (left != null) left.x else 0
            var y = if (right != null) right.x else 0
            var sum = x + y + carry
            carry = sum / 10
            curr.next = ListNode(sum % 10)
            curr = curr.next
            if (left != null)
                left = left.next
            if (right!= null)
                right = right.next    
        }
        if (carry == 1) curr.next = ListNode(1)
        dummaryHead.next
    }
}
```

> **小技巧**
>
> 对于链表问题，返回结果为头结点时，通常需要先初始化一个预先指针 pre，该指针的下一个节点指向真正的头结点head。使用预先指针的目的在于链表初始化时无可用节点值，而且链表构造过程需要指针移动，进而会导致头指针丢失，无法返回结果。

## **19. 删除链表的倒数第 N 个结点**

题解：先对链表进行遍历得到链表长度count，然后对倒数第N+1个节点执行：node.next = node.next.next。有种情况例外就是删除N=count，那么只需对头结点做操作即可。

```scala
/** * Definition for singly-linked list. * class ListNode(_x: Int = 0, _next: ListNode = null) { *   var next: ListNode = _next *   var x: Int = _x * } */
object Solution {    
    def removeNthFromEnd(head: ListNode, n: Int): ListNode = {        import scala.collection.mutable.ArrayBuffer        var countHead = head        var comHead = head        var count = 0        while (countHead != null) {            count += 1            countHead = countHead.next        }
        var target = count - n - 1        var arrayBuffer = ArrayBuffer[ListNode](comHead)        if (target >= 0) {            for (i <- 0 until count -1) {            if (i == target)  {              arrayBuffer.append(comHead.next.next)              comHead = comHead.next.next            }            else {              arrayBuffer.append(comHead.next)              comHead = comHead.next            }            }        } else {            comHead = comHead.next            return comHead        }
        var result = ListNode()        var dummary = result        for (node <- arrayBuffer) {           result.next = node           result = result.next        }        dummary.next    }}
```

‌

## **21.合并两个有序链表**

题解：最简单的办法，新建一个链表，每次从两个链表里捞最小的，但是时间和空间复杂度比较差，比较好的办法是直接操作俩个链表。

```scala
/**
 * Definition for singly-linked list.
 * class ListNode(_x: Int = 0, _next: ListNode = null) {
 *   var next: ListNode = _next
 *   var x: Int = _x
 * }
 */
object Solution {
    def mergeTwoLists(l1: ListNode, l2: ListNode): ListNode = {
        if (l1 == null && l2 == null) return l1
        var (list1,list2)= (l1,l2)
        var newList = new ListNode()
        var cur = newList
        while (list1 != null && list2 != null) {
             if (list1.x <= list2.x) {
                 cur.next = list1
                 list1 = list1.next
             }else {
                 cur.next = list2
                 list2 = list2.next
             }
             cur = cur.next 
        }
        if (list1 == null) cur.next = list2 else cur.next = list1
        return newList.next
    }
}
```

## **剑指 Offer 24. 反转链表**

题解 ： 将链表所有node存进栈里，然后每次弹出栈顶元素追加到新的链表中。构造新的链表时，额外定义一个指针指向头结点。空间复杂度比较差，超出内存限制警告。第二种办法是局部反转：定义前后两个指针，每次局部反转后，指针向后移，直到前面的指针指向的node为空。

```scala
/** * Definition for singly-linked list. * class ListNode(var _x: Int = 0) { *   var next: ListNode = null *   var x: Int = _x * } */
object Solution {    
    def reverseList(head: ListNode): ListNode = {          
        if (head == null) return head          
        var pre = head          
        var cur : ListNode = null          
        while (pre != null) {              
            var tmp = pre.next              // 局部反转              
            pre.next = cur              // 指针往前移              
            cur = pre              
            pre = tmp          
        }          
        cur    
    }
}


/** * Definition for singly-linked list. * class ListNode(var _x: Int = 0) { *   var next: ListNode = null *   var x: Int = _x * } */object Solution {    
    def reverseList(head: ListNode): ListNode = {          
        if (head == null) return head          
        import scala.collection.mutable.Stack          
        var curHead = head          
        var newHead : ListNode = null          
        var stack = new Stack[ListNode]()
        while (curHead != null) {              
            stack.push(curHead)              
            curHead = curHead.next          
        }                  
        newHead = stack.pop()          
        var dummy = newHead                     
        while (!stack.isEmpty) {             
            newHead.next = stack.pop()              
            newHead = newHead.next          
        }         
        dummy    
    }
```

## **92. 反转链表 II**

题解**：**将需要反转的部分存入栈中，先入后出。

```scala
/**
 * Definition for singly-linked list.
 * class ListNode(_x: Int = 0, _next: ListNode = null) {
 *   var next: ListNode = _next
 *   var x: Int = _x
 * }
 */
object Solution {
    import scala.collection.mutable
   def reverseBetween(head: ListNode, m: Int, n: Int): ListNode = {
     var myHead = head
    var index = 1
    val stack = new mutable.Stack[Int]()
    // 将需要反转的部分存入栈中
    while (myHead != null) {
      if (index >= m && index <= n) {
        stack.push(myHead.x)
      }
      myHead = myHead.next
      index += 1
    }
    myHead = head
    index = 1
    // 将需要反转的部分重新复制
    while (myHead != null) {
      if (index >= m && index <= n) {
        myHead.x = stack.pop()
      }
      myHead = myHead.next
      index += 1
    }
    head
  }
}
```

# **递归**

## **653. 两数之和 IV - 输入 BST**

题解：前序递归所有节点，每访问一个节点，判断Set集中不是存在一个元素a满足：k - a = root.value,如果存在返回true，若不存在将root.vaue添加到集合中，继续左右节点的搜索。

```scala
/**
 * Definition for a binary tree node.
    * class TreeNode(_value: Int = 0, _left: TreeNode = null, _right: TreeNode = null) {
    *   var value: Int = _value
    *   var left: TreeNode = _left
    *   var right: TreeNode = _right
    * }
 */
object Solution {
  import scala.collection.mutable.Set
  def findTarget(root: TreeNode, k: Int): Boolean = {
    return findIt(root, k, Set())
  }
  def findIt(root: TreeNode, k: Int, set: Set[Int]): Boolean = {
    if (root == null) return false // 判断base case
    if (set.contains(k - root.value)) return true else set.+=(root.value)
    findIt(root.left, k, set) || findIt(root.right, k, set)
  }
     
}
```



## **55. 跳跃游戏**

题解：从后往前判断，初始步长为step=1，假设可以到达用变量标识isCan=1。 如果倒数第二个点的值大于等于步长1，那么就把倒数第二个点看做最后一个点，继续递归。如果倒数第二个点小于步长，那么倒数第三个点就需要跳两步，递归时步长要+1，标识isCan=0。当把所有的元素都递归完以后，判断isCan是否等于1即可。 注意： 很常见的情况是[1,2,0,0]这种，倒数第二个点将step置为2，当递归到倒数第三个点的时候要记得将step重新置为1。

```scala
object Solution {  
    def canJump(nums: Array[Int]): Boolean = {        
    if (nums.length == 1) return true         
    var tail = nums.length - 2        
    var step = 1        
    var isCan = 1        
    if(helper(nums,tail,step,isCan) == 1)  true else false    
    }
    def helper(nums:Array[Int],tail:Int,_step: Int, _isCan :Int): Int = {        
        if (tail == -1) return _isCan        
        var isCan = _isCan        
        var step = _step        
        if (nums(tail) >= step) {            
            isCan = 1            
            step = 1            
            helper(nums, tail - 1,step ,isCan)        
        } else {            
            isCan = 0            
            helper(nums, tail - 1, step + 1, isCan)        
        }    
    }    
}
```

# DFS

## **200. 岛屿数量**

题解：岛屿系列题目都可以用DFS来解决。

二叉树DFS两要素：判断Base Case , 访问左右子树

def dfs(root: TreeNode = null) : Unit = {

​    if (root == null) return  //  判断base case

   dfs(root.left) //  访问左右子树

   dfs(root.right)

}

这题是网格，可以把它想象成一个“四叉树”(妙啊~)，那么它的base case就是越界，如果遍历到的格子不在网格范围内，那就直接return，它有4个邻接点(i-1,j)、(i+1,j)、(i,j+1)、(i,j-1)

判断位置是否在网格中：

def inArea(grid : Array[Array[Char]] ,i : Int  , j :Int) : boolean = {

​    return   i >=0  && i <  grid.length && j >=0 && j < grid(0).length

}

构造网格DFS：

dfs dfs(grid : Array[Array[Char]] , i : Int = 0 , j : Int = 0)  : Unit = {

​    if(!inArea(grid,i,i))  return‌

​    if(grid(i)(j) != “1”)  return  // 当前网格是海洋或者已经遍历过

   grid(i)(j) = ”0“

   dfs(grid,i-1,j)

   dfs(grid,i+1,j)

   dfs(grid,i,j-1)

   dfs(grid,i,j+1)

}

需要注意网格和二叉树不同，二叉树的每个节点都只有一个父节点，但网格不同，因此我们防止某个网格被反复遍历。办法是将遍历过的节点用常量的标识。

上面是这道题的基本框架，下面实现这道题：

```scala
object Solution {
     import scala.collection.mutable.ArrayBuffer
     def inArea(grid: Array[Array[Char]], i: Int, j: Int): Boolean =  {
       return i >= 0 && i < grid.length && j >= 0 && j < grid(0).length
     }
     
    def dfs(grid: Array[Array[Char]], i: Int, j: Int): Unit = {
        if (!inArea(grid,i,j)) return
        if (grid(i)(j) != '1') return
        grid(i)(j) =  '0'
        dfs(grid, i + 1, j)
        dfs(grid, i - 1, j)
        dfs(grid, i, j - 1)
        dfs(grid, i, j + 1)
    }
     
 
    def numIslands(grid: Array[Array[Char]]): Int = {
        var count : Int = 0
        for (i <- 0 until grid.length) {
          for (j <- 0 until grid(0).length) {
              if (grid(i)(j) == '1') {
                 dfs(grid,i,j) // 一次岛搜索结束
                 count += 1
              }   
          }
        }
        count
    }
    
}
```

# 贪心

## **122. 买卖股票的最佳时机 II**

题解：贪心的思路很简单，局部最优，只需判断当前和昨天的股价即可。

```scala
object Solution {
  def maxProfit(prices: Array[Int]): Int = {
    var len = prices.length
    if (len < 2) return 0
    var sum = 0
    for (i <- 1 until len)
      sum += Math.max(prices(i) - prices(i - 1), 0)
    sum
  }
}
```

## **665. 非递减数列**

题解：假设有序列[...,a,b,c,...],当存在nums[i] > nums[i + 1]的时候，有两个办法让它递增：

nums[i] = nums[i+1] nums[i+1] = nums[i] 尽可能使用方法1，因为使用方法2可能会破坏后面的序列递增，如[4,2,3]。但方法1在某些情况下不能使用，如[4,5,2]，因为将nums[1] = 2后它还是递减的，这时候就只能使用方法2。

上面的描述太抽象，自己都绕晕了，简单地说就是加入第二个元素比第一个小，最好的方式是把第一个元素的值弄小，而不是放大第二个值，因为放大第二个值会影响到后面的序列。

但不是所有的情况都可以通过把第一个值弄小的方法解决。下面列举几种情况：

【2，4，3，5】这种情况可以直接把4置为3，因为3大于第一个元素。

【2，6，1，5】这种情况没法把6置为1，因为1小于2，修改之后还是递减。

【4，2，3】这种情况，比较特殊第二个元素第一个元素小直接将4置为2就可以，因为它根本不会影响到2之前的序列，并且如果将2置为4，那么就影响到2以后的序列的递增了。

总而言之就是要判断nums[i+1]和nums[i-1]的关系，如果nums[i +1] > nums[i -1]那就将nums[i] 置为nums[i+1]，否则就将nums[i+1]置为nums[i]。

```scala
def checkPossibility(nums: Array[Int]): Boolean = {
  var cnt: Int = 0
  for (i <- 0 until nums.length - 1) {
    if (nums(i) > nums(i + 1)) {
      if (i == 0) {
        nums(0) = nums(1)
      }
      else {
        if (nums(i + 1) < nums(i - 1))
          nums(i + 1) = nums(i)
        else
          nums(i) = nums(i + 1)
      }
      cnt += 1
    }
  }
  if (cnt > 1) false else true
}
```

# 动态规划

## **70. 爬楼梯**

题解：动态规划，到第N个楼梯的最后一步有两个跳法，要么跳一阶要么跳两阶，那么到第N阶的方法实际上就是到N-1阶的方法数+到N-2阶的方法数。

推导方程：DP[N] = DP[N-1] + DP[N-2]

```scala
object Solution {
      def climbStairs(n: Int): Int = {
    if (n == 1 || n == 0) return 1
    val dp: Array[Int] = new Array[Int](n + 1)
    dp(0) = 1
    dp(1) = 1
    for (i <- 2 to n) {
      dp(i) = dp(i - 1) + dp(i - 2)
    }
    dp(n)
  }
}
```



## **剑指 Offer 47. 礼物的最大价值**

**题解：**思路：1.贪心策略，每次选取局部最优，这种办法实际上不总是能解决问题，贪心算法是目光短浅的。  2.动态规划，从左上角开始计算每个网格最大的价值，当前格子grid(i)(j)的价值就是它左边或者上面的格子的价值决定的。有下面几种格子： i = 0 j = 0 第一个格子起点，价值就是它自己 i = 0 j >0 第一行的格子，价值就是左边的格子的价值+ 它自己的价值 i > 0 j = 0第一列的格子， 价值就是上面的格子的价值+它自己的价值 上面三种的价值都是固定的，下面这种格子才有最大的价值 i > 0 j >0 中部的格子，最大的价值等于 max(左边格子的价值，上边格子的价值) + 它自己的价值 每一个格子的价值都会迭代计算到最后一个格子，最后一个格子的价值就是最大的价值.

```scala
/* 动态规划 */
def maxValueDP(grid: Array[Array[Int]]): Int = {
  var maxRow = grid.length
  var maxCol = grid(0).length
  if (maxCol == 0 && maxRow == 0) return grid(0)(0)
  for (i <- 0 until maxRow) {
    for (j <- 0 until maxCol) {
      grid(i)(j) match {
        case _ if i == 0 && j == 0 =>
        case _ if i == 0 => grid(i)(j) += grid(i)(j - 1)
        case _ if j == 0 => grid(i)(j) += grid(i - 1)(j)
        case _ => grid(i)(j) += Math.max(grid(i - 1)(j), grid(i)(j - 1))
      }
    }
  }
  grid(maxRow - 1)(maxCol - 1)
}
 
 
/* 贪心算法 */
def maxValue(grid: Array[Array[Int]]): Int = {
  var maxRow = grid.length - 1
  var maxCol = grid(0).length - 1
  var maxSum = grid(0)(0)
  if (maxCol == 0 && maxRow == 0) return maxSum
  var (m, n) = (0, 0)
  while (m <= maxRow && n <= maxCol) {
    if (m == maxRow && n == maxCol) return  maxSum
    maxSum match {
      case _ if m == maxRow && n < maxCol => {
        maxSum += grid(m)(n + 1)
        n += 1
      }
      case _ if n == maxCol && m < maxRow => {
        maxSum += grid(m + 1)(n)
        m += 1
      }
      case _ => {
        if (grid(m + 1)(n) > grid(m)(n + 1)) {
          maxSum += grid(m + 1)(n)
          m += 1
        } else {
          maxSum += grid(m)(n + 1)
          n += 1
        }
      }
    }
  }
  maxSum
}
```