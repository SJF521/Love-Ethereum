## PBFT

该部分主要是对于存在拜占庭的系统,系统如果要达成共识,总节点数与拜占庭节点数之间需要满足的关系.

### 相关定义

* 拜占庭

  一个可能呈现任意行为的节点被称为拜占庭.任意行为意味着“所有能想象到的事情”,比如根本不发送任何消息,像不同的邻居发送不同且错误的消息,以及谎报自己的输入值.
  
  * 拜占庭协议
  
  在一个存在拜占庭节点的系统中达成共识被称为拜占庭协议.如果一个算法可以在存在f个拜占庭节点的情况下正常工作,则该算法为f可适用.
  
  * 共识
  
  系统中有n个节点,其中最多有f个节点可能崩溃,也就是说有n-f个节点是好的.节点i从一个输入值vi开始.所有节点要从全部输入值中最终选择一个值(决策值),并且满足下面的条件:
  
  __一致性__:所有好节点的决策值必须相同
  
  __可终止性__:所有好节点在有限的时间内终止决策过程
  
  __有效性__:选择出的决策值必须是其中一个节点的输入值
  
  
  对于存在拜占庭的系统,显然我们需要的是 __正确输入有效性__,即决策值必须是其中一个好节点的输入值. 然而实现 __正确输入有效性__并不那么容易.
  因为一个拜占庭节点可以遵守协议,但是谎报他的输入值,此时无法将其与一个好节点进行区分.
  
  __全部相同有效性__:如果所有好节点起始时具有相同的输入值v,则决策值必须也是v.

  
  ### 拜占庭协定(f=1)
  
  0:在一个分布式系统中所有节点都运行着同一份代码,该代码需要一个输入指令x.此时系统中所有节点需要在输入指令上达成共识,保证系统所有“好节点”都使用相同的输入指令.

 1: 节点u提议代码的输入指令值为x

          第一轮
        
        2:向所有其他节点发送tuple(u,x)
          
          节点u告诉其他节点,它提议输入指令为x
        
        3:从其他所有节点接受tuple(v,y)
        
         节点u接受其他节点的提议
         
        4:将所有接收到的消息tuple(v,y)存储在集合Su
        
         节点u汇总其他所有节点的提议
        
        
          第二轮
  
       5:向其他节点发送Su
       
         节点u将其所汇总的来自其他所有节点的提议Su发送给其他节点,注意Su中并不包含节点u的提议,其只是节点u收集到的所有提议!
         
       6:从其他节点接受Sv
      
       7:T = 所有出现在至少两个Sv中的tuple(v,y),包括自己的Su
       
       8:设tuple(v,y) 是T中y值最小的元组
       
       9:选择y为决策值
       
### 拜占庭协定(f=1)中如果节点数大于4,所有的好节点将得到同一个集合T

#### 假设一共有四个节点u v w x,其中w为拜占庭节点:

        节点u 收到的其他节点提议,即Su为 tuple(v,1)、 tuole(w,3), tuple(x,3);
        节点v 收到的其他节点提议,即Sv为 tuple(u,1)、 tuple(w,2), tuple(x,3);
        节点w 收到的其他节点提议,即Sw为 tuple(u,2)、 tuple(v,2), tuple(x,2);----w为拜占庭节点
        节点x 收到的其他节点提议,即Sx为 tuple(u,1)、 tuple(v,1), tuple(w,1);

        此时T为{tuple(v,1),tuple(x,3),tuple(u,1)}

        一个好节点将至少两次看到每一个正确的输入值.
        
        以节点u为例:
        
        第一次是对应该输入值的源节点得到即tuple(u,1)、tuple(v,1)、tuple(x,3),另一次是从第三个好节点那里得到,例如只有结合v和x才能得到tuple(u,1)、tuple(v,1)、tuple(x,3).
        因此所有正确的值都在集合T中.如果此时唯一的那个拜占庭节点将一个相同的值发送给至少两个好节点,所有好节点将至少看到该值两次,并且将它加入到集合T中.
        
        因此此时每个节点都会拥有一个相同的集合T,所有节点只需要选择集合T中最小的提议值即可达成共识.
        
        对于节点数大于4的情况,自然也会成立.
        
####  当只有三个节点u、v、w,其中w为拜占庭节点:
        
        节点u 收到其他节点的提议:tuple(v,1),tuple(w,2)
        节点v 收到其它节点的提议:tuple(u,2),tuple(w,3)
        节点w 收到其他节点的提议:tuple(v,0),tuple(u,3) -- w为拜占庭节点
        
        此时节点u收到的从节点v、w收到的关于v的提议值不同,此时是无法确定到底w和v哪个是拜占庭节点.同理v也无法确定u和w到底哪个是拜占庭节点.三个节点无法达成共识.
        
        => 若一个有n个节点的网络中存在f >=  n / 3个拜占庭节点,则该网络不能达成共识.
        
           我们可以选择极端的情况考虑,假设 f = 1,则此时n最大只能为3.这种情况上面已经举例证明了.f为1时,n最小应该为4,此时 1 < 4/3 .
