rand bit[7:0] dat;若对其约束为 dist{[1:5]:=1,[3:10]:=0,4:=3};，3.4.5的概率分布如何？
  答：dist中，属于概率相加，即3占1/8,4占4/8,5占(1+3)/8

randc在constraint中的限制？
  答 ：randc 型的随机变量可以有constraint，但是不能是solve-before,并且不能用于dist，其次，VCS不支持32bit以上的randc变量，最后需要说明，对象类型不能是randc。

越界读取数组和越界写入数组，会出现什么情况？
  答：越界读取数组的值时，将返回数组（适用于所有数组类型，包括定宽数组，动态数组，关联数组和队列，也适用于地址中含有X或Z的情况）元素类型的默认值（四态X，双态0）。越界写入数组的时候，虽然不会报语法错误，但是这个越界的地址不会被写入，即再去读取这个地址的时候，仍然是越界读取的情况。另外，如果数组的某个地址没有被赋值，去读取这个地址时，仍然是越界读取的情况。

随机化非随机变量，会出现什么情况？ 即 在class中，有变量 int a ; constraint a_c {a>=0;}，在类实例调用xx.randomize(a)。
  答：a不会被赋予随机值，但是会检查a是否会满足constraint，如不满足，则会报错。

如何在class中随机化自己的成员变量？
  答：如class中有 int a,b ；直接调用std::randomize(a)即可，注意a不是rand或randc型。同理			  可以使用sucess=randomize(a,b) with{a>0;b>5};

在调用randomize函数时，随机变量是先被随机化赋值，还是先被与constrain作检查？
  答：如：
class Exm ;
    rand int a；
    constraint a_c {a==0;} 
endclass
    Exm exm=new();
    exm.constraint_mode(0);//关闭约束
    exm.randomize(a)with{a==1;};//a 的值将为1
    exm.constraint_mode(1);//打开约束
    exm.randomize(a);//如果是先检查再随机赋值，则此处应求解失败
endclass

上例不会报错。即：先求解约束的的随机值，然后在检查判断

当task或function的型参是class句柄时，ref型与input型的区别是？
  答：由于传入的是句柄（指向对象的指针），即使没有ref修饰符，如果在task或function中对class的对象的成员做了修改，对task或function外部传入的class对象是也可见的。但是有一点差别就是：没有ref修饰符时，形参传递是句柄，这是无法被修改的（即不能在task或function中重新new一次）。在有ref修饰符时，形参传递的是句柄的地址，这是可以被修改的。

关于对象的复制
  答：
Animal dog1 dog2；
dog1=new();
dog2=new dog1;
//dog1中的值被简单复制到dog2，当Animal中有其他对象的句柄时，则在复制是，是句柄被复制了，也就是dog2中的句柄也指向了dog1中的句柄；
//因此，常常需要手动编写copy函数，以保证复制的不是句柄，而是完整的对象内容；

$display与$strobe的区别
$strobe命令会在当前时间部结束时完成；而$display是只要仿真器看到就会立即执行。一般来说，用$display()系统任务来显示当前变量的值，用$strobe()系统任务来显示用非阻塞赋值的变量值。
$display与$write的区别是：$display会在每次显示信息后自动换行，$write不会换行。
reg var_a;
initial begin
	var_a='h1;
	$display("d var_a=%0h",var_a);
	var_a='h0;
	$strobe("s var_a=%0h",var_a);
	#0;$display("#0d var_a=%0h",var_a);
end
OUTPUT:
///////////////////
d var_a=1
#0d var_a=0
s var_a=0
//////////////////

testbech中initial 块与uvm的执行顺序？
  答：initial 块先于uvm启动，并且，uvm在#0之后启动。

重写(Override)与重载(overloading)？
  答：sv支持方法重写Override，即参数个数和类型，以及返回值的类型必须与父类相同。

在内嵌约束中，受约束的变量条件与当前类的变量名重名，应该如何区分？
  答：
class Gen_addr;
  rand bit[31:0]addr;
endclass

class Addr;
Gen_addr t=new();
bit[31:0] addr=128;
bit[31:0] addr_top=8;
assert(t.randomzie()with{ addr==addr ;});//with中的作用域是被随机化的class，即addr是t中的addr;故这里addr被约束成t.addr==t.addr
assert(t.randomzie()with{ addr==this.addr; });//这里this指向t，故addr被约束成t.addr==t.addr
assert(t.randomzie()with{ addr==local::addr; });//这里，addr被约束成t.addr==local::addr，即addr==128
assert(t.randomzie()with{ addr==addr_top; });//这里t中没有addr_top，故会在当前class中继续寻找这个变量，addr被约束成t.addr==addr_top，即addr==8
