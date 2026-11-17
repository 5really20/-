# 附录C 实验指导与实验报告要点

> 适用：本课 16 学时上机（王爽《汇编语言》第 4 版 实验 1~实验 17）｜配套：正文第 1~18 章｜上机环境：Windows + DOSBox + MASM 6.11（`D:\Netease\masm`）

**这份附录回答的是「实验怎么做、结果怎么观察、错了怎么查」，不重复讲指令本身。** 指令语义看正文对应章节，指令格式查[附录A](附录A-指令系统速查表.md)，写代码常踩的坑查[附录B](附录B-易错点与题型总结.md)。

## C.1 实验总览与考核方式

**本课 64 学时 = 48 讲授 + 16 实验，实验占 25%。** 16 学时通常排成 **6~8 次上机**，每次 2~3 学时，一次上机对应教材里的 2~3 个「实验N」。

| 批次 | 对应教材实验 | 主题 | 建议学时 |
|---|---|---|---|
| 第 1 次 | 实验一、实验二 | 查看 CPU 和内存，用机器指令和汇编指令编程 | 2 |
| 第 2 次 | 实验三 | 编程、编译、连接、跟踪 | 2 |
| 第 3 次 | 实验四、实验五 | `[bx]` 和 `loop`；编写调试多段程序 | 2 |
| 第 4 次 | 实验六、实验七 | 实验六：实践课程中的程序（更灵活的定位内存地址的方法）；实验七：寻址方式在结构化数据访问中的应用 | 3 |
| 第 5 次 | 实验八、实验九 | 分析奇怪的程序；往显存写彩色字符 | 2 |
| 第 6 次 | 实验十、实验十一 | 编写子程序（`show_str` / `divdw` / `dtoc`） | 3 |
| 第 7 次 | 实验十二~实验十五 | 中断例程系列（0 号、7ch、CMOS、int 9） | 3 |
| 第 8 次 | 实验十六、实验十七 | 用子程序入口地址表分派功能 | 2 |

**成绩构成（以本校实际方案为准，这里是常见比例）：** 平时/考勤 10% + 实验报告 20%~30% + 期末笔试 60%~70%。

> **重点**：**汇编的实验报告和别的课不一样——必须贴 DEBUG 的寄存器/内存记录。** 老师要看的是「你有没有真的单步走过」，而不是「代码写得漂不漂亮」。一份只贴源代码和一句「运行成功」的报告，和一份贴了 `-r` 前后对照、`-d` 内存快照、逐步说明标志位怎么变的报告，得分能差一倍。**所以从第一次上机起就要养成「一边 `t` 一边抄寄存器」的习惯。**

## C.2 本机上机环境搭建（Windows + DOSBox + MASM）

本机（湖北大学机房/自用机）现成环境在 `D:\Netease\masm`，目录结构如下（已用 `Test-Path` 逐一验证存在）：

| 路径 | 是什么 |
|---|---|
| `D:\Netease\masm\Masm\DOSBox.exe` | DOSBox 模拟器主程序 |
| `D:\Netease\masm\Masm\bin\MASM.EXE` | 汇编器（MASM 6.11） |
| `D:\Netease\masm\Masm\bin\LINK.EXE` | 连接器 |
| `D:\Netease\masm\Masm\bin\DEBUG.EXE` | 调试器 |
| `D:\Netease\masm\Masm\bin\ML.EXE` | 汇编器（ML，一步到位，支持 `/Fl` 出清单） |
| `D:\Netease\masm\result\` | **你现有作业放的地方**（`work01.asm`、`work02.asm` 及各自的 obj/exe/lst 都在这）——但**它不在 DOSBox 默认挂载点里**，见下面第 ④ 步的坑 1 |

> **★ 关于 `result` 目录的一个重要事实**：`D:\Netease\masm\Masm\DOSBox.conf` 里的 `mount c .` 把 **`C:` 的根映射到 `D:\Netease\masm\Masm`**，而 `result` 是它的**兄弟目录**（`D:\Netease\masm\result`），**不在挂载点内**。所以「进 DOSBox 后 `cd result`」这条路走不通（会报 `Invalid directory`）。要么按下面的**方案 A** 把 `.asm` 放到 `Masm\bin`，要么按**方案 B** 把挂载点往上提一层。

**从零到跑通的六步（如果换一台机器，按这个来）：**

```
   ① 装 DOSBox（本机已有 D:\Netease\masm\Masm\DOSBox.exe）
   ② 确认 MASM.EXE、LINK.EXE、DEBUG.EXE、ML.EXE 在同一个目录（本机是 D:\Netease\masm\Masm\bin）
   ③ 建一个放作业的目录（本机已有 D:\Netease\masm\result）
   ④ 启动 DOSBox，挂载并进入工作目录（两种方案，任选其一）：
        【方案 A｜推荐，不依赖 PATH】把 .asm 和工具放同一个目录
            mount c d:\Netease\masm\Masm
            c:
            cd bin                       ← 工具就在这个目录里
        【方案 B｜继续用 result 目录】挂上层目录，再把工具目录加进 PATH
            mount c d:\Netease\masm
            c:
            set PATH=%PATH%;C:\MASM\BIN
            cd result
   ⑤ 写源程序（★ 在 Windows 里用记事本写好，存进第 ④ 步那个目录，再进 DOSBox 汇编）
   ⑥ 汇编 → 连接 → 运行 → 调试：
         masm 1.asm;         ← 分号表示「后面全部用默认值」，不再逐项追问
         link 1.obj;         ← 同上
         1.exe               ← 直接敲文件名运行
         debug 1.exe         ← 进入调试器
```

> **★ 第 ④ 步最容易踩的三个坑（本机逐条实测过，务必先看）：**
>
> **坑 1：本机 `DOSBox.conf` 的 `mount c .` 挂的是 `D:\Netease\masm\Masm`，`result` 不在里面。**
> `D:\Netease\masm\Masm\DOSBox.conf` 的 `[autoexec]` 段逐行是：
>
> ```
> echo off
> cls
> mount c .
> c:
> cd bin
> CLS
> debug.exe tempfile.exe
> exit
> ```
>
> `mount c .` 里的 `.` 就是 `DOSBox.exe` 所在目录 ⟹ **`C:` 的根 = `D:\Netease\masm\Masm`**。而作业目录 `result` 是它的**兄弟**（`D:\Netease\masm\result`），**不在挂载点里** ⟹ 在 DOSBox 里敲 `cd result` 只会得到 **`Invalid directory`**。所以要么按**方案 A** 把 `.asm` 放到 `Masm\bin`，要么按**方案 B** 把挂载点往上提一层。
>
> **坑 2：双击 `DOSBox.exe` 得不到可交互的命令行，窗口一闪即关。**
> 上面 `[autoexec]` 末尾是 `debug.exe tempfile.exe` 紧跟一条 `exit`，而 `Masm\bin\` 下**根本没有 `TEMPFILE.EXE`**（该目录里只有 `tempfile.asm`、`TEMPFILE.DLL`、`TEMPFILE.LST`、`TEMPFILE.OBJ`）⟹ `debug` 立刻失败，紧接着 `exit` 执行，**窗口一闪就没了**。
>
> **正确启动方式：用同目录下的 `basic.conf`**（它的 `[autoexec]` 只有 `echo off` / `cls` / `mount c .` / `c:`，是干净的交互环境）：
>
> ```
> "D:\Netease\masm\Masm\DOSBox.exe" -conf "D:\Netease\masm\Masm\basic.conf"
> ```
>
> 也可以直接编辑 `DOSBox.conf`，把 `[autoexec]` 改成第 ④ 步你要的挂载方式，并**删掉末尾那两行 `debug.exe tempfile.exe` 和 `exit`**。
>
> **坑 3：`mount c d:\masm` 之后敲 `masm 1.asm;` 报 `Bad command or file name`。**
> 因为工具在 `C:\BIN` 里，而当前目录是 `C:\RESULT`，`PATH` 里没有 `C:\BIN`。**解决办法就是上面的方案 A（放同一目录）或方案 B（`set PATH=%PATH%;C:\MASM\BIN`）。**

> **★ 第 ⑤ 步：DOSBox 里没有编辑器，别指望 `edit`。**
>
> **本机实测：整个 `D:\Netease\masm` 目录树下没有 `EDIT.COM` 也没有 `EDIT.EXE`**（唯一命中的 `Masm\PDUMMY.COM` 是个 5 字节的占位文件）。所以在 DOSBox 里敲 `edit 1.asm` 只会得到 `Bad command or file name`。
>
> **正确做法（本机可行、也是你自己作业 `work01.asm` 的来路）：**
> 1. **在 Windows 里**用记事本（或本机自带的 `D:\Netease\masm\Masm\winasm.exe`、`D:\Netease\masm\Masm\wasm.exe` 这类 Windows 侧工具）写好源程序；
> 2. 存到第 ④ 步挂载进来的那个目录（方案 A = `D:\Netease\masm\Masm\bin\`，方案 B = `D:\Netease\masm\result\`），**文件名写 `1.asm`、「保存类型」选「所有文件」、编码选 ANSI**；
> 3. 再进 DOSBox，`cd` 到那个目录之后汇编。
>
> 如果确实想在 DOSBox 里直接改代码，**必须自备 `EDIT.COM` 并放到与 `MASM.EXE` 同一个目录**（DOSBox 本身不附带任何编辑器）。

> **★ 为什么非要进 DOSBox？不能直接在 Windows 命令行敲 `masm` / `link` / `debug` 吗？**
>
> 因为这套工具链是**混血**的，本机实测各位数如下：
>
> | 文件 | 文件头类型 | 能否在 64 位 Windows 命令行直接运行 |
> |---|---|---|
> | `ML.EXE` | **PE（Windows 可执行）** | **能** —— `ML /c /nologo /Fl 1.asm` 正常出 `.obj` 和 `.lst` |
> | `MASM.EXE` | PE 外壳（MASM 兼容驱动） | **不能独立用** —— 它只是个转调 `ML.EXE` 的驱动，实测报 `error A2901: cannot run ML.EXE` |
> | `LINK.EXE` | **16 位 DOS 程序**（只有 MZ 头） | **不能** —— 报「指定的可执行文件不是此操作系统平台的有效应用程序」 |
> | `DEBUG.EXE` | **16 位 DOS 程序** | **不能**（同上） |
> | `TLINK.EXE` / `LIB.EXE` | 16 位 DOS | 不能 |
>
> **结论：只有汇编（`ML.EXE`）能在 Windows 命令行里跑；连接和调试必须进 DOSBox。** 所以标准流程就是「DOSBox 里一条龙」，别图省事把 `link` 敲在 Windows 命令行里。
>
> **★ 顺带一个好处**：`ML.EXE /Fl` 生成的 `.lst` 清单文件里带**机器码和每条指令的偏移**，在 Windows 侧看 `.lst` 比在 DOSBox 里翻屏舒服得多。**做「算 `jmp` 位移」这类题目时，用 `ML /c /nologo /Fl` 出 `.lst` 对答案最快。**

**关于记事本存盘的两个坑（每年都有人栽）：**

- **扩展名**：记事本默认存成 `1.asm.txt`。存的时候「保存类型」选**所有文件**，文件名写 `1.asm`（带引号写 `"1.asm"` 更保险）。
- **编码**：**必须存成 ANSI（GBK），不能存成「UTF-8 带 BOM」。** MASM 6.11 会把开头的 BOM 三字节当成乱码，第一行直接报语法错。本机用户自己的作业 `work01.asm` 就是 GBK 编码（用 `Get-Content -Encoding Default` 读出来中文注释正常显示，说明它是 GBK 而非 UTF-8）。

**常见报错 → 原因 → 解决（≥10 条）：**

| 报错 / 现象 | 原因 | 解决 |
|---|---|---|
| `fatal error A1000: cannot open file : 1.asm` | 当前目录下没有这个文件 | `dir` 看看；确认 `cd` 到了源程序所在目录 |
| `Unable to open file` | 同上（DOSBox 里大小写敏感度与路径有关） | 文件名全用**小写**，路径别超过 8 层 |
| `Object filename [1.OBJ]:` 停下来等你输入 | `masm` 默认会逐项追问目标文件名 | 在命令末尾加分号 `masm 1.asm;`，或直接**回车**用默认值 |
| `error A2008: syntax error : c` | 标号用了 MASM 保留字（`c`、`proc`、`byte`……） | 改名叫 `tb`、`tbf`（详见[附录B](附录B-易错点与题型总结.md)） |
| `error A2001: immediate operand not allowed` | 写成了 `mov [6], ax`。**MASM 把 `[数字]` 当成常量表达式 `6`**，这条语句等价于 `mov 6, ax`——目的操作数成了立即数，当然不允许 | 加段前缀或改用标号：`mov ds:[6], ax`（`ds:[6]` 才是内存）；或用数据标号 `mov w, ax` |
| `error A2070: invalid instruction operands` | **立即数直接送内存但没写 `ptr`**，如 `mov [bx], 1` | 写成 `mov byte ptr [bx], 1`（要 16 位就 `word ptr`）。**★ 本机实测：默认模式报这条；加 `/Zm`（MASM 5.1 兼容模式）却【不报错】，而是按「字」处理、静默写坏 2 个字节——后者更危险** |
| `error A2048: nondigit in number` | 十六进制漏写 `h`（`123B` 被当成二进制数） | 写成 `123BH`；以 A~F 开头的补前导 0 |
| `error A2075: jump destination too far` | `jmp short` 目标超过 −128~+127 字节 | 改用 `jmp near ptr` 或调整代码布局 |
| `error A2096: segment, group, or segment register expected` | 写了 `jmp 1234:0`（那是 DEBUG 的 `a` 命令语法） | 用 `jmp far ptr 标号`，或 `push 段`+`push 偏移`+`retf` |
| `Severe error A2xxx` | 严重错误，**后面那一串报错多半是连带的** | 只看**第一条**报错，改完重新汇编 |
| `LINK : fatal error L1101: invalid object module` | `.obj` 损坏，或是别的汇编器生成的 | 删掉 `.obj` 重新 `masm` |
| `LINK : warning L4021: no stack segment` | 没定义栈段 | 只写代码段时可以**忽略**这个警告 |
| DOSBox 里中文注释显示成乱码 | DOSBox 无中文环境 | **不影响汇编**（MASM 整行忽略注释）；要好看就注释写英文 |
| 双击 `1.exe` 一闪而过 | 程序里没有正常返回 DOS | 结尾必须有 `mov ax, 4c00h` + `int 21h` |
| `Divide error` 后程序卡死 | `div` 除数为 0 或商溢出 | 除前检查除数、清 `dx`；或先做实验十二装 0 号中断例程 |

## C.3 DEBUG 命令速查与调试套路

**八个必会命令：**

| 命令 | 作用 | 典型用法 |
|---|---|---|
| `r` | 看 / 改寄存器 | `r` 看全部；`r ax` 改 AX；`r ip` 改 IP |
| `d` | 看内存 | `d 1000:0`；`d ds:0`；`d` 接着上次看 |
| `e` | 改内存 | `e 1000:0 12 34 56`；`e 1000:0` 进入逐个改写 |
| `u` | 反汇编 | `u`；`u 1000:0`；`u cs:0` |
| `t` | 单步（**会进入 `int`/`call` 内部**） | `t`；`t 5` 连走 5 步 |
| `p` | 单步（**跨过 `int`/`call`/`loop`**） | `p`——跟踪循环体时用它，否则一个循环要走几百步 |
| `g` | 运行到指定地址 | `g 0012` 跑到偏移 0012；`g` 直接跑完 |
| `a` | 用汇编指令写内存 | `a 1000:0` 然后逐行敲指令 |
| `q` | 退出 DEBUG | `q` |

> **重点**：`t` 和 `p` 的区别是**本课最容易吃亏的地方**。跟踪 `loop` 时用 `t`，每按一次只走一步（`dec cx`、判断、跳转各算一步），一个 100 次的循环要按 300 次；用 `p` 则**一次把整个循环走完**。反过来，想看清循环体里每条指令的效果时，必须用 `t`。

**用 `int 3` 下断点（机器码 `CC`）：**

`int 3` 是一条单字节指令（`CC`），DEBUG 对它有特殊处理，所以**在程序里插 `int 3` 就相当于插了一个断点**——运行到那里会停下来、把控制权交回 DEBUG。本机用户自己的作业 `work01.asm` 就是这么用的：

```asm
    mov ax, x
    add ax, y
    mov es:[result1], ax
    nop
    int 3                ; 第 1 个断点：停在这里看 es:[result1] 和标志位
```

**本机实测的操作流程（重要经验）：**

```
   ① debug 1.exe
   ② 用 g 直接运行到断点（不用一路 t 过去）：
         -g
      程序在 int 3 处停下，DEBUG 重新出现 '-' 提示符
   ③ 这时用 -r 看寄存器、-d 看内存，把要记录的值抄下来
   ④ 想继续单步时，如果按 t / p 发现【跳进了 ROM 区】（IP 变成很小的值、
      反汇编出来是 BIOS 的代码），说明它进了 INT 3 的中断处理程序。
      不要慌，也不要重启——手工把 IP 改回【CC 后面那条指令的地址】：
         -r ip
         IP 0017        ← 输入 CC 之后那一条指令的偏移（例如 0017H / 0024H / 002AH）
         -t             ← 现在就能正常往下单步了
   ⑤ 继续 -g 跑到下一个 int 3，重复 ③④
```

**怎么知道「CC 后面那条指令的地址」？** 在 `-u` 反汇编里找到 `CC` 那一行，**下一行开头的地址**就是。例如：

```
   -u
   0010 B8 34 12        mov ax, [x]     ← 举例
   0013 CC              int 3
   0014 90              nop             ← ★ CC 后面这条的偏移就是 0014
```

那就 `-r ip` 然后输入 `0014`。

## C.4 实验一、实验二：查看 CPU 和内存 + 用户作业范例

**实验一、实验二的任务**：熟悉 `r` / `d` / `e` / `a` / `u` 五个命令；用 `a` 命令在内存里直接写几条机器指令并逐条执行；理解「**段地址 × 16 + 偏移地址 = 物理地址**」在 DEBUG 里到底长什么样。

**必做的观察：同一个物理地址有多种「段:偏移」写法。**

```
   -d 1000:0        ← 看 10000H 开始的内存
   -d 0:10000       ← 看的是【同一块内存】！
   -d 0fff:10       ← 还是同一块（0FFFH × 16 + 10H = 10000H）
```

三次 `d` 出来的字节**完全一样**，这就把「`1000:0` = `0:10000` = `0FFF:10`」从公式变成了眼睛能看到的事实。

> **重点**：这是[第 2 章](第02章-寄存器.md)「物理地址 = 段地址 × 16 + 偏移地址」的现场验证，也是[第 5 章](第05章-BX和loop指令.md)里 `0:200` 与 `0020:0` 等价的根据。

### 例 C.1（题型：标志位观察）——用户上机作业 `work01.asm`

这是本机 `D:\Netease\masm\result\work01.asm` 里用户本人做过的作业，正好就是**实验一、二的典型形态**：用 `int 3` 下两个断点，分别观察加法和减法后的结果与标志位。

```asm
data segment
    x dw 1234H
    y dw 62A0H
data ends

extra segment
    result1 dw ?
    result2 dw ?
extra ends

code segment
    assume cs:code, ds:data, es:extra
start:
    mov ax, data
    mov ds, ax              ; 把 data 段的段地址装进 DS（assume 只是声明，不会自动装载！）
    mov ax, extra
    mov es, ax              ; 把 extra 段的段地址装进 ES

    mov ax, x               ; AX = 1234H
    add ax, y               ; AX = 1234H + 62A0H = 74D4H
    mov es:[result1], ax    ; ★ 必须写 es: 前缀，否则会写到 DS 段去
    nop
    int 3                   ; 第 1 个断点：查看 result1 的值和标志位 SF/ZF/CF/OF

    mov ax, x               ; AX = 1234H（重新装入，因为上面 AX 已被改）
    sub ax, y               ; AX = 1234H − 62A0H = AF94H
    mov es:[result2], ax
    nop
    int 3                   ; 第 2 个断点：查看 result2 的值和标志位 SF/ZF/CF/OF

    mov ah, 4ch             ; AH = 4CH：DOS 的「带返回码结束程序」功能号
    int 21h                 ; 返回 DOS
code ends
end start
```

**逐段讲解（三个必须讲透的点）：**

1. **`extra segment` + `assume ... es:extra` + `mov ax, extra` / `mov es, ax` 三步缺一不可。** `assume` 是**伪指令**，只在汇编期告诉汇编器「`extra` 段该用 ES 定位」，**不会在运行期往 ES 里装任何东西**——装载必须靠自己写 `mov`。
2. **`mov es:[result1], ax` 里的段超越前缀 `es:` 为什么必须写。** 内存操作数的默认段是 `DS`（只有用 `BP` 寻址时默认才是 `SS`）。`result1` 在 `extra` 段里，不写 `es:` 就会写进 `DS` 指向的 `data` 段——**而且不会报错**，属于静默错误。
3. **`mov ah, 4ch` / `int 21h`**：`4CH` 是「带返回码结束程序」。注意这里只写 `mov ah, 4ch` 而不是 `mov ax, 4c00h`，因为 `AL` 的值此时无关紧要（返回码取 `AL`，不关心就算了）。

**预期结果（逐步算出来的，可以自己再验一遍）：**

| 断点 | 指令 | 结果（存入内存） | SF | ZF | CF | OF |
|---|---|---|---|---|---|---|
| 第 1 个 | `add ax, y` | `1234H + 62A0H = 74D4H` | 0 | 0 | 0 | 0 |
| 第 2 个 | `sub ax, y` | `1234H − 62A0H = AF94H` | 1 | 0 | 1 | 0 |

**四个标志位为什么是这个值：**

- **加法**：`0x1234 = 4660`、`0x62A0 = 25248`，和 = `29908 = 0x74D4`。最高位是 0（`74H` 的 bit7 = 0）⟹ **SF = 0**；结果非 0 ⟹ **ZF = 0**；没超出 16 位 ⟹ **CF = 0**；两个正数相加仍是正数、结果 `29908 < 32767` ⟹ **OF = 0**。
- **减法**：`4660 − 25248 = −20588`，补码表示 = `−20588 + 65536 = 44948 = 0xAF94H`。最高位是 1（`AFH` 的 bit7 = 1）⟹ **SF = 1**；结果非 0 ⟹ **ZF = 0**；**无符号视角** `4660 < 25248`，需要借位 ⟹ **CF = 1**；**有符号视角**两个操作数同号（`0x1234` = +4660、`0x62A0` = +25248，都小于 32767），同号相减的结果必然落在 16 位有符号范围内 ⟹ **OF = 0**。

> **★★★ 这张表最好的教学点：`AF94H` 这同一个位模式，无符号读作 44948，有符号读作 −20588。SF 只看最高位，CF 只反映无符号借位，OF 只反映有符号溢出——三个标志位各管各的，互不干扰。** 这正是[第 11 章](第11章-标志寄存器.md)「`ja/jb` 看 CF、`jg/jl` 看 SF/OF」的物理根据。

**DEBUG 跟踪步骤（照抄即可）：**

```
   C:\> debug work01.exe
   -u                      ← 先反汇编，找到两个 CC 的地址
   -g                      ← 跑到第 1 个 int 3
   -r                      ← 抄下 AX、DS、ES、IP 和标志位那一行（NV UP EI PL NZ NA PO NC 之类）
   -d es:0                 ← 看附加段：result1 处应是 D4 74（低字节在前！）
   -g                      ← 跑到第 2 个 int 3
   -d es:0                 ← result2 处应是 94 AF
   -q                      ← 退出
```

**注意 `-d es:0` 出来的字节顺序是 `D4 74`**：内存里**低字节在前**（小端序），`74D4H` 存成 `D4 74`。DEBUG 的 `d` 是按字节显示、`r` 是按字显示，两者看起来「反着」，这是[第 3 章](第03章-寄存器（内存访问）.md)的内容。

**易错点：**

- `assume` 写完就以为 `DS`/`ES` 已经装好了 → **必须自己 `mov`**。
- 忘了 `es:` 前缀 → 数据写进 `DS` 段，`-d es:0` 看还是 `?` 的初值。
- 用 `-d es:0` 却按字读成 `74D4` → 内存是**小端序**，字节显示为 `D4 74`。
- 把 `int 3` 当成「程序结束」 → 它是**断点**，之后还要 `g` 继续。

## C.5 实验三：编程、编译、连接、跟踪

**任务**：把源程序走完 `masm` → `link` → `debug` 全流程，跟踪执行，观察 `CS:IP` 的初始值、`DS` 与 `CS` 的关系、以及 `1.exe` 开头那 256 字节的 PSP。

```asm
assume cs:codesg
codesg segment
start:
    mov ax, 2000h           ; AX = 2000H
    mov ds, ax              ; DS = 2000H
    mov bx, 0               ; BX = 0000H（偏移）
    mov cx, 5               ; CX = 0005H（循环次数）

s:  mov byte ptr ds:[bx], 0ffh   ; ★ 必须写 ds: 前缀，裸 [0] 会被当成常量
    inc bx                  ; 偏移 +1
    loop s                  ; CX 减 1，不为 0 就跳回 s

    mov ax, 4c00h           ; 正常返回 DOS
    int 21h
codesg ends
end start
```

**运行结果**：`2000:0000 ~ 2000:0004` 这 5 个字节都被写成 `FFH`（用 `-d 2000:0` 可以看到 `FF FF FF FF FF`）。

**跟踪时最该记住的三件事：**

1. **`-r` 出来的 `CS:IP` 不是 `0000:0000`**，而是连接器填的程序入口（就是 `start` 标号处的偏移）。`-u cs:0` 能看到代码段开头的内容。
2. **`DS` 比 `CS` 小 `10H`**。原因：`.exe` 被装入内存时，前面有 **256 字节 = 10H 个节**的 **PSP（Program Segment Prefix，程序段前缀）**，DOS 把 `DS` 和 `ES` 都指向 PSP 的段地址；而 `CS` 指向代码段，代码段紧跟在 PSP 之后，所以 `CS = DS + 10H`，反过来就是 `DS = CS − 10H`。
3. **`1.exe` 的前 256 字节是 PSP，不是你的代码。** 用 `-d cs:-100`（即 `d DS:0`）能看到 PSP：开头两个字节是 `CD 20`（一条 `int 20h` 指令，用于老式返回），偏移 `2CH` 处是环境块段地址，偏移 `80H` 处是命令行参数长度。

> **重点**：`masm 1.asm;` 和 `link 1.obj;` 末尾那个**分号**表示「其余选项全用默认值」，不加分号 MASM 会逐项追问 `Object filename [1.OBJ]:` 之类，一路回车也行，但加分号更省事。详见 [C.2](#c2-本机上机环境搭建windows--dosbox--masm)。

## C.6 实验四：`[bx]` 和 `loop` 的使用

**任务**：用 `loop` 向内存 `0:200 ~ 0:2FF` 依次写入 `0 ~ 63`；体会 `mov ax, [bx]` 与 `mov ax, [0]` 在 MASM 里的**不同含义**。

```asm
assume cs:code
code segment
start:
    mov ax, 0
    mov ds, ax              ; DS = 0（注意：不能写 mov ds, 0，必须经通用寄存器中转）
    mov bx, 200h            ; ★ 用 BX 承载偏移，[bx] 才是真正的「内存寻址」
    mov cx, 64              ; 循环 64 次
    mov dl, 0               ; 要写入的值，从 0 开始

s:  mov ds:[bx], dl         ; 把 DL 写入 DS:BX
    inc bx                  ; 偏移 +1
    inc dl                  ; 值 +1
    loop s                  ; CX = CX − 1，若 CX ≠ 0 则跳回 s

    mov ax, 4c00h
    int 21h
code ends
end start
```

**运行结果**：`0:200` 处依次是 `00 01 02 … 3FH`（即 0~63），一共 64 字节。

**`mov ax, [bx]` 和 `mov ax, [0]` 的区别（本实验最大的坑）：**

| 写法 | MASM 汇编成 | 实际含义 |
|---|---|---|
| `mov ax, [bx]` | `8B 07` | 取 `DS:BX` 处的内容（**真寻址**） |
| `mov ax, [0]` | `B8 00 00` | 等于 `mov ax, 0`（**把 0 当立即数！静默出错**） |
| `mov ax, ds:[0]` | `A1 00 00` | 取 `DS:0` 处的内容（**加段前缀才有效**）。★ 目的寄存器是 `AX` 时 MASM 会选 3 字节的 `A1 moffs16` 短形式；`8B 06 xx xx` 只在目的**不是 `AX`** 时出现（如 `mov bx, ds:[0]` = `8B 1E 0000`） |

> **重点**：**MASM 里 `[数字]` 会被当成常量表达式，不是内存地址。** 这是从王爽书（大量用 DEBUG 的 `a` 命令，那里 `[0]` 就是内存）切到 MASM 写源程序时最容易踩的坑。**要访问固定地址，要么写 `ds:[0]`，要么先把地址放进 `bx`/`si`/`di` 再用 `[bx]`。** 详细展开见[第 3 章](第03章-寄存器（内存访问）.md) 3.2 节的警告块和[附录B](附录B-易错点与题型总结.md)。

**易错点：**

- 写成 `mov ds, 0` → 汇编器报错，**段寄存器不能用立即数赋值**，必须 `mov ax, 0` / `mov ds, ax`。
- 循环里忘了 `inc bx` → 64 次全写到同一个字节上。
- 把 `0:200~0:2FF` 误认为「中断向量表之外」→ 它其实**在表内**（向量表占 `0:000~0:3FF`），只是 DOS 只用前 512 字节，后 512 字节空着。见[第 5 章](第05章-BX和loop指令.md)。

## C.7 实验五：编写、调试具有多个段的程序

**任务**：写一个含**数据段、栈段、代码段**三段的程序，用 `end start` 指定入口，观察 `CS`、`SS`、`DS` 的装载和 `SS:SP` 的初值。

```asm
assume cs:code, ds:data, ss:stack

data segment
    dw 0123h, 0456h, 0789h, 0abch, 0defh, 0fedh, 0cbah, 0987h
data ends

stack segment
    dw 0,0,0,0,0,0,0,0      ; ★ 栈段必须【显式预留】空间，否则 push 会覆盖段外数据
stack ends

code segment
start:
    mov ax, stack
    mov ss, ax
    mov sp, 16              ; ★ 栈空时 SP 指向栈底（最高地址），此处 = 栈段长度 16

    mov ax, data
    mov ds, ax
    mov bx, 0
    mov cx, 8

s1: push ds:[bx]            ; ① 把 data 段的 8 个字依次入栈
    add bx, 2
    loop s1

    mov bx, 0
    mov cx, 8

s2: pop ds:[bx]             ; ② 再依次出栈，写回 data 段
    add bx, 2
    loop s2

    mov ax, 4c00h
    int 21h
code ends
end start
```

**运行结果**：`data` 段的 8 个字被**逆序**了——原来是 `0123 0456 0789 0ABC 0DEF 0FED 0CBA 0987`，执行后变成 `0987 0CBA 0FED 0DEF 0ABC 0789 0456 0123`。

**为什么能逆序**：入栈顺序是 `1→8`，栈顶是第 8 个字；出栈顺序自然是 `8→1`，而写回的偏移是递增的 `0,2,4,…,14`，所以第一个被写回偏移 0 的是第 8 个字——正好逆序。

**跟踪时观察什么：**

| 观察点 | 怎么看 | 应该看到什么 |
|---|---|---|
| 三个段寄存器 | `-r` | `CS`、`DS`、`SS` 是三个**不同**的值（各段的段地址由 LINK 分配） |
| 栈段初值 | `-r` | 执行完 `mov sp, 16` 后 `SS:SP` 指向栈段的最高地址 |
| 入栈过程 | `-t` 几次后 `-r`、`-d ss:0` | `SP` 每次 **减 2**（栈向低地址生长） |
| 出栈过程 | `-t` 几次后 `-r` | `SP` 每次 **加 2** |
| 栈内布局 | `-d ss:0` | 最后入栈的字在**最低**地址，最先入栈的在**最高**地址 |

**易错点：**

- **栈段只写 `stack segment` / `stack ends` 而不预留空间** → `push` 直接覆盖后面的内存，程序行为不可预测。
- **`end` 后面忘了写 `start`** → 入口变成代码段第一个字节，`mov ax, stack` 之前可能先执行了数据。
- 忘了 `mov ax, stack` / `mov ss, ax` → `SS` 还指向 DOS 的某个段，`push` 会破坏系统数据（**可能当场死机**）。
- 改 `SS` 之后没紧跟 `mov sp, ...` → 8086 会在改 `SS` 的下一条指令后自动抑制中断，但 `SP` 仍是旧值，栈顶错位。

## C.8 实验六：实践课程中的程序（更灵活的定位内存地址的方法）

**教材原题（王爽《汇编语言》第 4 版，实验六「实践课程中的程序」）**：

> 编程，将 `datasg` 段中每个单词的**前 5 个字母改成大写字母**。
>
> ```asm
> assume cs:codesg, ds:datasg
> datasg segment
>     db '1. file         '      ; 每项 16 字节：编号 + 空格 + 单词 + 补齐空格
>     db '2. edit         '
>     db '3. search       '
>     db '4. view         '
>     db '5. options      '
>     db '6. help         '
> datasg ends
> codesg segment
> start:
>     ...
> codesg ends
> end start
> ```

**这道题的三个关键判断（想清楚再动手）：**

| 问题 | 答案 | 理由 |
|---|---|---|
| 单词从每项的第几个字节开始？ | **第 3 个**（偏移 `+3`） | `'1'`、`'.'`、`' '` 占掉前 3 个字节 |
| 每个单词几个字母？ | **前 5 个**（本题只改这 5 个） | 题面明确「前 5 个字母」 |
| 怎么用一层循环走完 6 项？ | **行偏移用 `bx`（步长 16）、列偏移用 `si`（0~4）** | 正需要 `[bx+si+idata]`——**这就是本题的教学目的** |

**解题骨架（`idata = 3`，`bx` 走行、`si` 走列）：**

```asm
assume cs:codesg, ds:datasg
datasg segment
    db '1. file         '
    db '2. edit         '
    db '3. search       '
    db '4. view         '
    db '5. options      '
    db '6. help         '
datasg ends
codesg segment
start:
    mov ax, datasg
    mov ds, ax
    mov bx, 0                   ; bx = 行偏移，每项 16 字节
    mov cx, 6                   ; 共 6 项
s0: mov si, 0                   ; si = 列偏移，从单词首字母开始
    mov dx, cx                  ; ★ 保存外层循环次数（loop 要用 cx）
    mov cx, 5                   ; 内层：每项改 5 个字母
s1: mov al, ds:[bx+si+3]        ; ★ 核心：相对基址变址寻址，idata = 3
    and al, 11011111b           ; ★ 小写 → 大写（把 bit5 清 0）
    mov ds:[bx+si+3], al
    inc si
    loop s1
    mov cx, dx                  ; ★ 恢复外层循环次数
    add bx, 16                  ; ★ 跳到下一项（每项 16 字节）
    loop s0

    mov ax, 4c00h
    int 21h
codesg ends
end start
```

**运行结果**：屏幕无输出（程序只改内存）。用 `debug 1.exe` 进去，`g` 到程序结束，再 `d ds:0` 看数据段，应看到：

```
-d ds:0
0B2A:0000  31 2E 20 46 49 4C 45 20-20 20 20 20 20 20 20 20   1. FILE
0B2A:0010  32 2E 20 45 44 49 54 20-20 20 20 20 20 20 20 20   2. EDIT
0B2A:0020  33 2E 20 53 45 41 52 43-68 20 20 20 20 20 20 20   3. SEARCh
0B2A:0030  34 2E 20 56 49 45 57 20-20 20 20 20 20 20 20 20   4. VIEW
0B2A:0040  35 2E 20 4F 50 54 49 4F-6E 73 20 20 20 20 20 20   5. OPTIOns
0B2A:0050  36 2E 20 48 45 4C 50 20-20 20 20 20 20 20 20 20   6. HELP
```

> **★ 注意第 3、5 行：`search` 变成了 `SEARCh`、`options` 变成了 `OPTIOns`——后面还剩字母没改完，这是对的。**
> 题目要求改的是「**前 5 个字母**」，不是「整个单词」。`search` 有 6 个字母、`options` 有 7 个，所以第 6、7 个字母（`h`、`ns`）保持小写。**内层循环的 `mov cx, 5` 就是这个「5」——如果写成 `mov cx, 6` 或更大，就会把空格也 `and` 一遍（空格 `20H` 的 bit5 本来就是 1，`and 11011111B` 会把它变成 `00H`，显示出来是一片乱码）。**
>
> **★ 顺便验证一下边界**：本程序内层只跑 5 次，`si` 最大到 4，所以 `[bx+si+3]` 最大偏移 = `bx + 7`，永远落在单词内部，**不会碰到每项末尾的补齐空格**——这就是「先算清数据布局再写循环」的价值。

**★ 对照一下 `file` 变成 `FILE` 的机器层面**：`'f'` = `66H` = `01100110B`，`and 11011111B` 之后 = `01000110B` = `46H` = `'F'`——**只把 bit5（`20H` 那一位）清成 0**。反过来 `or al, 00100000B` 就是把 bit5 置 1、小写变大写（大写 `'F'` = `46H` → `or 20H` → `66H` = `'f'`）。**ASCII 里大写字母和小写字母只差 `20H` 这一个二进制位，这就是大小写转换的全部原理。**

**本节的其余部分（三种寻址方式对照与两层循环模板）：**

**任务**：练习 `[bx+idata]`、`[bx+si]`、`[bx+si+idata]` 三种寻址方式；用 `and` / `or` 做大小写转换；**用两层循环改写单层循环**，并把外层循环的 `CX` 用 `push`/`pop` 保护起来。

**三种寻址方式做同一件事的对照（都能访问到 `data` 段偏移 16 处的字节）：**

| 写法 | 名称 | 这里 `bx` / `si` 的含义 |
|---|---|---|
| `ds:[16]` | 直接寻址 | 不用寄存器，写死偏移（必须带 `ds:`） |
| `ds:[bx+8]` | 寄存器相对寻址（`[bx+idata]`） | `bx = 8`，加固定位移 8 |
| `ds:[bx+si]` | 基址变址寻址 | `bx = 8`、`si = 8`，两个寄存器相加 |
| `ds:[bx+si+4]` | 相对基址变址寻址 | `bx = 8`、`si = 4`、位移 4 |

```asm
assume cs:code, ds:data
data segment
    a db 1,2,3,4,5,6,7,8        ; 偏移 0 ~ 7
    b db 8 dup (0)              ; 偏移 8 ~ 15
data ends
code segment
start:
    mov ax, data
    mov ds, ax

    mov bx, 0                   ; ① [bx+idata]：源在 a、目标在 b，两者偏移差 8
    mov cx, 8                   ;    一个循环同时管两个数组
s1: mov al, ds:[bx]             ;    读 a[bx]
    mov ds:[bx+8], al           ;    写 b[bx]，idata = 8
    inc bx
    loop s1

    mov bx, 8                   ; ② [bx+si]：bx 指向行、si 指向列
    mov si, 0
    mov al, ds:[bx+si]          ;    = ds:[8+0] = b[0] = 1

    mov bx, 0                   ; ③ [bx+si+idata]：行 + 列 + 固定位移
    mov si, 4
    mov al, ds:[bx+si+8]        ;    = ds:[0+4+8] = b[4] = 5

    mov ax, 4c00h
    int 21h
code ends
end start
```

**两层循环 + 大小写转换（本实验的核心考点）：**

```asm
assume cs:code, ds:data
data segment
    db 'hello, world!   '       ; 每个字符串恰好 16 字节
    db 'assembly lang   '
    db 'masm 6.11 rocks  '
data ends
code segment
start:
    mov ax, data
    mov ds, ax

    mov bx, 0                   ; 字符串首偏移
    mov cx, 3                   ; 外层：3 个字符串

outer:
    push cx                     ; ★★★ 保存外层计数！不保存就会被内层 loop 清零
    mov si, 0                   ; 串内下标
    mov cx, 16                  ; 内层：每个串 16 字节

inner:
    mov al, ds:[bx+si]          ; 取一个字符
    cmp al, 'a'
    jb  next                    ; 小于 'a' → 不是小写字母
    cmp al, 'z'
    ja  next                    ; 大于 'z' → 不是小写字母
    and al, 11011111b           ; ★ 第 5 位清零 → 小写变大写
    mov ds:[bx+si], al

next:
    inc si
    loop inner                  ; 内层循环（会改 CX！）

    pop cx                      ; ★ 恢复外层计数
    add bx, 16                  ; 换到下一个字符串
    loop outer

    mov ax, 4c00h
    int 21h
code ends
end start
```

**运行结果**：三个字符串全部变成大写：`HELLO, WORLD!`、`ASSEMBLY LANG`、`MASM 6.11 ROCKS`。

> **重点**：**`loop` 指令只用 `CX` 一个计数器，所以嵌套循环必须自己保护外层计数。** 两种做法：① `push cx` / `pop cx`（本程序，最常用）；② 在数据段开一个字单元暂存。**忘了保护是嵌套循环最经典的 bug**——内层循环跑完 `CX` 变成 0，外层 `loop` 直接结束，程序「只处理了第一个字符串就退出」。

**大小写转换的位运算原理**（详见[第 7 章](第07章-更灵活的定位内存地址的方法.md)）：

| 操作 | 指令 | 说明 |
|---|---|---|
| 小写 → 大写 | `and al, 11011111b` | 第 5 位（值 `20H`）**清零** |
| 大写 → 小写 | `or al, 00100000b` | 第 5 位**置 1** |
| 大小写互换 | `xor al, 00100000b` | 第 5 位**取反** |

**易错点：**

- **内层 `loop` 之前不 `push cx`** → 外层只跑一次。
- `push cx` 之后忘了 `pop cx` → 栈不平衡，最后 `ret`/程序返回时飞掉。
- 转换前不做范围判断 → 空格、标点、数字也会被 `and` 改掉（例如 `'!'` = `21H`，`and 11011111b` 后变 `01H`，成了不可见字符）。
- 三个字符串长度不相等 → `bx += 16` 会错位。

## C.9 实验七：寻址方式在结构化数据访问中的应用

**任务**：`data` 段里放着一张「21 年的公司数据表」——年份（字符串，每项 4 字节）、总收入（`dd`，每项 4 字节）、雇员数（`dw`，每项 2 字节）。要求算出**每年的人均收入**（总收入 ÷ 雇员数，取整），按「年份 总收入 雇员数 人均收入」的格式存进 `table` 段。

**这张表的地址规律（本实验的全部难点就在这里）：**

| 数据 | 在 `data` 段的位置 | 每项长度 | 第 i 项（i 从 0 起）的地址 |
|---|---|---|---|
| 年份 | 偏移 0 开始 | 4 字节 | `0 + i × 4` |
| 总收入 | 偏移 84 开始（21 × 4） | 4 字节 | `84 + i × 4` |
| 雇员数 | 偏移 168 开始（84 + 84） | 2 字节 | `168 + i × 2` |

```asm
assume cs:code, ds:data, es:table

data segment
    ; ① 21 个年份字符串，每项 4 字节，共 84 字节
    db '1975','1976','1977','1978','1979','1980','1981','1982','1983'
    db '1984','1985','1986','1987','1988','1989','1990','1991','1992'
    db '1993','1994','1995'
    ; ② 21 年的总收入，每项 4 字节（dd），共 84 字节
    dd 16,22,382,1356,2390,8000,16000,24486,50065,97479,140417,197514
    dd 345980,590827,803530,1183000,1843000,2759000,3753000,4649000,5937000
    ; ③ 21 年的雇员人数，每项 2 字节（dw），共 42 字节
    dw 3,7,9,13,28,38,130,220,476,778,1001,1442,2258,2793,4037,5635,8226
    dw 11542,14430,15257,17800
data ends

table segment
    db 21 dup ('year summ ne ?? ')   ; 每行 16 字节：4+1+4+1+2+1+2+1
table ends

code segment
start:
    mov ax, data
    mov ds, ax
    mov ax, table
    mov es, ax

    mov bx, 0               ; data 段：年份 / 总收入的项偏移（每项 4 字节）
    mov di, 0               ; data 段：雇员数的项偏移（每项 2 字节）
    mov bp, 0               ; table 段：行偏移（每行 16 字节）
    mov si, 21              ; ★ 用 SI 当循环计数，把 CX 留给 div 的除数

row:
    ; ① 复制年份（4 字节）→ table 行内偏移 0
    mov ax, ds:[bx]
    mov es:[bp], ax
    mov ax, ds:[bx+2]
    mov es:[bp+2], ax

    ; ② 复制总收入（4 字节）→ table 行内偏移 5；同时留在 DX:AX 里准备做除法
    mov ax, ds:[bx+84]      ; 低 16 位
    mov dx, ds:[bx+86]      ; 高 16 位
    mov es:[bp+5], ax
    mov es:[bp+7], dx

    ; ③ 复制雇员数（2 字节）→ table 行内偏移 10，并放进 CX 作除数
    mov cx, ds:[di+168]
    mov es:[bp+10], cx

    ; ④ 人均收入 = DX:AX ÷ CX → 商在 AX，存到 table 行内偏移 13
    div cx
    mov es:[bp+13], ax

    add bx, 4               ; 下一年的年份 / 收入
    add di, 2               ; 下一年的雇员数
    add bp, 16              ; table 的下一行
    dec si
    jnz row

    mov ax, 4c00h
    int 21h
code ends
end start
```

**运行结果**（`table` 段前几行，用 `-d` 看）：

| 年份 | 总收入 | 雇员数 | 人均收入 |
|---|---|---|---|
| 1975 | 16 | 3 | 5 |
| 1976 | 22 | 7 | 3 |
| 1977 | 382 | 9 | 42 |
| 1978 | 1356 | 13 | 104 |
| 1979 | 2390 | 28 | 85 |
| 1995 | 5937000 | 17800 | 333 |

**「`bx` 指向行、`si`/`di` 指向列」的结构化访问范式：**

```
   data 段（三块数据，各自连续）
   ┌──────────────┐  ← bx（year/summ，步长 4）
   │ year   x 21  │
   ├──────────────┤  ← bx + 84
   │ summ   x 21  │
   ├──────────────┤  ← di + 168（ne，步长 2）
   │ ne     x 21  │
   └──────────────┘

   table 段（21 行，每行 16 字节；行基址 bp = 行号 × 16）
   ┌────────────────┐ ← bp = 0            第 0 行
   │ year summ ne ? │
   ├────────────────┤ ← bp = 16           第 1 行
   │ year summ ne ? │
   ├────────────────┤
   │      ...       │                     第 2 ~ 19 行
   ├────────────────┤ ← bp = 320          第 20 行
   │ year summ ne ? │
   └────────────────┘
```

**`table` 段一行的内部偏移（`db 21 dup ('year summ ne ?? ')`，共 16 字节）：**

| 行内偏移 | 0~3 | 4 | 5~8 | 9 | 10~11 | 12 | 13~14 | 15 |
|---|---|---|---|---|---|---|---|---|
| 内容 | `year` | 空格 | `summ` | 空格 | `ne` | 空格 | `??`（留给 `avg`） | 空格 |

> **注意**：`summ` 从**行内偏移 5** 开始（不是 4），因为 `year` 后面跟了一个空格；`ne` 从 10 开始、`avg` 从 13 开始。**这些数字是 `dup` 字符串里数出来的，不是猜的**——所以 `t[i].summ` 写成 `es:[bp+5]`。

**和 C 语言 `struct` 对照：**

| C 语言 | 汇编里对应的做法 |
|---|---|
| `struct { char year[4]; long summ; short ne; short avg; } t[21];` | `table` 段 `db 21 dup ('year summ ne ?? ')` |
| `t[i].summ` | `es:[bp+5]`，其中 `bp = i × 16` |
| `d.year[i]` | `ds:[bx]`，其中 `bx = i × 4` |
| `d.ne[i]` | `ds:[di+168]`，其中 `di = i × 2` |

> **重点**：**结构体成员的偏移是编译期固定的常量**（这里是 5、10、13），所以访问成员就是「行基址 + 固定位移」；而数组元素的偏移是「下标 × 元素长度」。这就是 `[bx+si+idata]` 这种寻址方式的现实意义——**`idata` 对应结构体成员偏移，`si`（乘上步长）对应数组下标**。第 18 章的 32 位寻址 `[基址+变址×比例+位移]` 是同一件事的升级版。

### 例 C.2（题型：无符号比较 + 三分支）——用户上机作业 `work02.asm`

这是本机 `D:\Netease\masm\result\work02.asm` 里用户本人做过的作业：统计一个字节数组里**大于 `42H`** 和**小于 `42H`** 的元素各有多少个。

```asm
data segment
    N = 32                          ; ★ 等值伪指令定义的符号常量（不是变量）
    array db N/4 dup (30h,42h,45h,0)  ; ★ dup：重复 N/4 = 8 次，每次 4 字节 → 共 32 字节
    up   db ?
    down db ?
data ends

code segment
    assume cs:code, ds:data
start:
    mov ax, data
    mov ds, ax

    mov dx, 0                       ; DH = 大于 42H 的个数，DL = 小于 42H 的个数
    mov si, offset array            ; SI 指向数组首字节
    mov cx, N                       ; 循环 32 次

scan:
    mov al, [si]                    ; 取一个字节
    cmp al, 42h                     ; 与 42H 比较（只设标志位，不改 AL）
    ja  greater                     ; ★ 无符号「大于」→ 跳 greater
    jb  less                        ; ★ 无符号「小于」→ 跳 less
    jmp next                        ; ★★ 相等时必须显式跳过两个计数分支！

greater:
    inc dh                          ; 大于：DH + 1
    jmp next

less:
    inc dl                          ; 小于：DL + 1

next:
    inc si                          ; 指向下一个字节
    loop scan

    mov up, dh                      ; 把结果写回内存
    mov down, dl
    int 3                           ; 断点：在这里用 -d ds:32 看 up / down

    mov ah, 4ch
    int 21h
code ends
end start
```

**逐段讲解：**

1. **`array db N/4 dup (30h,42h,45h,0)`** 展开后是 `30h,42h,45h,00h` 重复 8 次，共 32 字节。`N/4` 是**汇编期常量表达式**（`32/4 = 8`），`dup` 的重复次数必须是常量。
2. **`N = 32` 是等值伪指令**（`equ` 的简写形式），汇编期把 `N` 替换成 `32`，**不占任何内存**——它和 `dw 32` 完全是两回事。
3. **`cmp al, 42h` 后必须用 `ja`/`jb`，不能用 `jg`/`jl`。** `30h`/`42h`/`45h` 是 ASCII 码，属于**无符号**数据。`ja`/`jb` 看 `CF`（无符号借位），`jg`/`jl` 看 `SF`/`OF`（有符号溢出）。虽然本例里 `30h`~`45h` 都小于 `80H`，两者结果碰巧一致，但**一旦数据超过 `7FH`（例如 `0A0H`），`jg`/`jl` 就会给出完全错误的结果**。
4. **三分支写法：`ja` 跳走、`jb` 跳走、剩下的就是「相等」，必须显式 `jmp next`。** 不写这句，相等的情况会**直接落到 `greater:` 标签**上去，DH 被多加了 1——这是三分支最经典的陷阱。
5. **为什么用 `dh`/`dl` 计数而不是直接 `inc up`？** 两种都行：`inc up` 每次都要访问内存，`inc dh` 只在寄存器里操作，更快；最后一次性 `mov up, dh` 写回。**寄存器计数 + 最后写回**是循环统计题的标准写法。

**正确答案（逐步推演）：**

`array` 展开成 8 组，每组 `30h, 42h, 45h, 00h`：

| 字节 | 与 `42H` 比较 | 走哪个分支 | 计数变化 |
|---|---|---|---|
| `30h` | `30h < 42h` | `jb less` | `DL += 1` |
| `42h` | 相等 | `jmp next`（**两个都不加**） | 不变 |
| `45h` | `45h > 42h` | `ja greater` | `DH += 1` |
| `00h` | `00h < 42h` | `jb less` | `DL += 1` |

每组贡献 1 个「大」、2 个「小」，8 组共 **`DH = 8`、`DL = 16`**。所以：

**`up = 8 = 08H`，`down = 16 = 10H`。**

**DEBUG 跟踪步骤：**

```
   -u                  ← 找到 int 3 的地址
   -g                  ← 跑到 int 3
   -r                  ← 看 DH / DL：应为 DH = 08，DL = 10
   -d ds:32            ← 看 up / down 两个字节：应为 08 10
   -q
```

> **注意**：`up` 和 `down` 在 `data` 段里的偏移是 **32**（前面 32 字节的 `array` 占满了），所以 `-d ds:32` 看的就是它们。

**易错点：**

- 用了 `jg`/`jl` → 数据一旦超过 `7FH` 结果就错（本例碰巧对，**但考试会考反例**）。
- 三分支忘了 `jmp next` → 相等的情况被算进「大于」。
- 忘了 `mov dx, 0` → `DH`/`DL` 是随机值，计数从垃圾数开始。
- `mov cx, N` 写成 `mov cx, [N]` → `N` 是常量不是变量，`[N]` 会被当成 `[32]` 即常量 32（碰巧也对，但语义完全不同）。
- `mov si, offset array` 之后又写 `mov al, [si]` 而忘了 `mov ds, ax` → `DS` 没指向 `data` 段。

## C.10 实验八：分析一个奇怪的程序

**任务**：教材里有一段「自修改代码」——它把一条指令的字节搬到别的地方再执行。要求逐字节解释**它为什么能跑通**、`jmp` 的目标是怎么算出来的。

```asm
assume cs:codesg
codesg segment
        mov ax, 4c00h           ; 偏移 0~2：B8 00 4C
        int 21h                 ; 偏移 3~4：CD 21

start:  mov ax, 0               ; 偏移 5~7
      s: nop                    ; 偏移 8      ← 后面会被改写
        nop                     ; 偏移 9
        mov di, offset s        ; 偏移 10~12：DI = 8
        mov si, offset s2       ; 偏移 13~15：SI = 32
        mov ax, cs:[si]         ; 偏移 16~18：读 s2 处的【两个字】= EB F6
        mov cs:[di], ax         ; 偏移 19~21：写到 s 处 → s 变成 EB F6
     s0: jmp short s            ; 偏移 22~23：跳回 s
      s1: mov ax, 0             ; 偏移 24~26
        int 21h                 ; 偏移 27~28
        mov ax, 0               ; 偏移 29~31
      s2: jmp short s1          ; 偏移 32~33：EB F6
        nop                     ; 偏移 34
codesg ends
end start
```

**逐字节分析（这就是本实验的答案）：**

1. **`end start` 把入口定在偏移 5**，所以程序**不是**从偏移 0 开始执行，而是从 `mov ax, 0` 开始。
2. `mov ax, cs:[si]` 读的是 `s2` 处的**两个字**——即 `jmp short s1` 这条指令的机器码 **`EB F6`**。为什么是 `F6`？`jmp short s1` 的位移 = `标号 s1 的偏移 − jmp 指令后第一个字节的偏移` = `24 − 34 = −10 = F6H`。
3. `mov cs:[di], ax` 把 `EB F6` **写到了偏移 8、9**，也就是原来的两个 `nop` 上——**代码在运行中改写了自己**。
4. `s0: jmp short s` 跳到偏移 8。此时偏移 8 已经是 `EB F6`，CPU 把它当成 `jmp short` 执行：位移是 `F6H = −10`，而这条指令「后第一个字节」的偏移是 `8 + 2 = 10`，所以目标 = `10 + (−10) = 0`。
5. **偏移 0 处正是 `mov ax, 4c00h` 和 `int 21h`**——于是程序正常返回 DOS。

> **★★★ 关键洞察：`EB F6` 这两个字节在 `s2` 处和 `s` 处的【含义不同】，因为相对转移的位移是相对于「当前指令位置」算的。** 在 `s2`（偏移 32）处，`F6 = −10` 指向偏移 24；搬到 `s`（偏移 8）处，同一个 `F6` 就指向偏移 0。**机器码里的位移是「相对量」，不是「绝对地址」——这就是这个「奇怪的程序」的全部秘密。**

**运行结果**：程序一闪而过、正常返回 DOS，**屏幕上什么都不显示**。如果不用 DEBUG 单步，根本看不出它做了什么。

**DEBUG 跟踪步骤（必须做，否则体会不到）：**

```
   -u                  ← 先反汇编，把上面的偏移对照表抄下来
   -t 7                ← 单步 7 次，走到 mov cs:[di], ax 之前
   -d cs:8 l2          ← 看偏移 8、9：此时是 90 90（两个 nop）
   -t 1                ← 执行 mov cs:[di], ax
   -d cs:8 l2          ← ★ 再看：已经变成 EB F6！
   -t 2                ← 执行 jmp short s、再执行改写后的 jmp short
   -r                  ← ★ 看 IP：应该已经是 0000（代码段开头）
```

**易错点：**

- 以为程序从偏移 0 开始执行 → `end start` 已经指定了入口是偏移 5。
- 把 `jmp short s1` 的位移算成「`s1` 的偏移 − `s2` 的偏移」= `24 − 32 = −8` → **错**。位移的基准是「**jmp 指令后第一个字节**的偏移」，即 `24 − 34 = −10`。
- 以为改写后的 `jmp` 还跳回 `s1` → 位移是相对量，位置变了目标就变了。

## C.11 实验九：根据材料编程

**任务**：往显存 `B8000H ~ BFFFFH` 写数据，在屏幕**中间**显示三行不同颜色的 `welcome to masm!`。

**显存的两个基本规则（本实验的全部基础）：**

```
   显存 B8000H 起，屏幕 80 列 × 25 行，每个字符占【两个字节】：
      ┌──────┬──────┬──────┬──────┬─────
      │ 字符 │ 属性 │ 字符 │ 属性 │ ...
      └──────┴──────┴──────┴──────┴─────
       偶地址   奇地址   偶地址  奇地址
   屏幕偏移 = 行号 × 160 + 列号 × 2      （行、列都从 0 开始数）
```

**属性字节的 8 位含义：**

| 位 | 含义 |
|---|---|
| bit7 | 闪烁（1 = 闪烁） |
| bit6~4 | 背景色（8 种） |
| bit3 | 高亮（前景加亮） |
| bit2~0 | 前景色（8 种） |

**常用属性值**：`02H` 黑底绿字、`24H` 绿底红字、`71H` 蓝底白字（高亮）、`07H` 黑底白字（DOS 默认）、`70H` 反白。

```asm
assume cs:code, ds:data, ss:stack

data segment
    db 'welcome to masm!'       ; 16 个字符
    db 02h, 24h, 71h            ; 三行各自的属性：黑底绿字 / 绿底红字 / 蓝底白字
data ends

stack segment
    db 16 dup (0)
stack ends

code segment
start:
    mov ax, data
    mov ds, ax
    mov ax, stack
    mov ss, ax
    mov sp, 16

    mov ax, 0b800h
    mov es, ax                  ; ES = 显存段

    mov si, 0                   ; 行号 0/1/2（同时是属性字节的下标）
    mov di, 0720h               ; 起始偏移 = 11 × 160 + 32 × 2 = 1760 + 64 = 0720H

row:
    mov bx, 0                   ; 串内字符下标
    mov cx, 16                  ; 内层：16 个字符
col:
    mov al, ds:[bx]             ; 取字符
    mov ah, ds:[si+16]          ; ★ 取【本行的】属性字节（属性存在字符串后面 16 字节处）
    mov es:[di], ax             ; ★ 一次写一个字：低字节=字符（偶地址），高字节=属性（奇地址）
    inc bx
    add di, 2                   ; 移到下一个字符位置
    loop col

    add di, 160-32              ; 换行：本行只写了 32 字节，补满 160
    inc si
    cmp si, 3
    jb  row

    mov ax, 4c00h
    int 21h
code ends
end start
```

**运行结果**：屏幕第 12、13、14 行的正中（第 32 列起）各显示一行 `welcome to masm!`，三行颜色不同。

**三个必须讲透的点：**

1. **为什么 `mov es:[di], ax` 一次就能写好「字符 + 属性」？** 因为 `AX` 的低字节 `AL` = 字符、高字节 `AH` = 属性，而内存里**低字节存在低地址**（偶地址 = 字符位置）、**高字节存在高地址**（奇地址 = 属性位置）——**小端序正好和显存的布局吻合**。这是 8086 汇编里最优雅的一处巧合。
2. **起始偏移 `0720H` 怎么来的？** 要在「屏幕中间」显示：行取第 12 行（0 起数，即 11），列取第 32 列（80 列放 16 个字符，居中 ⟹ `(80 − 16) / 2 = 32`）。偏移 = `11 × 160 + 32 × 2 = 1760 + 64 = 1824 = 0720H`。
3. **换行时为什么 `add di, 160-32`？** 内层循环每个字符 `di += 2`，16 个字符共加 32；一行占 160 字节，所以还要补 `160 − 32 = 128`。

**易错点：**

- 忘了 `mov ax, 0b800h` / `mov es, ax` → 数据写进了 `DS` 段，屏幕上什么都没有。
- 字符和属性写反（偶地址写属性）→ 屏幕上出现奇怪的花屏。
- 换行时 `add di, 160` → 每行都会往右错 32 列，三行不在同一列上。
- 属性字节用十进制写 → `71` 和 `71H` 差别巨大（`71H = 113`，是「蓝底白字高亮」；十进制 `71 = 47H` 是「红底白字」）。
- 内层 `loop` 前不保护外层计数 → 只显示一行。

## C.12 实验十、实验十一：编写子程序

**这三个子程序是本课「子程序」部分的全部考点**，也是期末上机/手写代码题的高频素材。三个都要会默写。

### C.12.1 `show_str` —— 在指定位置用指定颜色显示字符串

**功能**：`dh` = 行号，`dl` = 列号，`cl` = 颜色属性，`ds:si` 指向以 `0` 结尾的字符串。

```asm
; ------------------------------------------------------------
; show_str：在 dh 行 dl 列，用 cl 属性，显示 ds:si 指向的 0 结尾字符串
; 参数：dh = 行号（0~24），dl = 列号（0~79），cl = 颜色属性，ds:si = 字符串
; 返回：无
; ------------------------------------------------------------
show_str:
    push ax
    push bx
    push cx
    push dx
    push es
    push si
    push di                 ; ★ 子程序用到的寄存器【全部】保护

    mov ax, 0b800h
    mov es, ax

    ; ---- ① 算出目标偏移 → DI ----
    mov al, dh              ; 行号
    mov ah, 0
    mov bl, 160
    mul bl                  ; AX = 行号 × 160
    mov di, ax
    mov al, dl              ; 列号
    mov ah, 0
    mov bl, 2
    mul bl                  ; AX = 列号 × 2
    add di, ax              ; DI = 行 × 160 + 列 × 2

    ; ---- ② 逐字符写入 ----
    mov al, cl              ; AL 暂存颜色属性（后面 CL 要用来装字符）
    mov ch, 0               ; ★ CH 清零，好让 jcxz 只看 CL
s:
    mov cl, ds:[si]         ; 取一个字符
    jcxz ok                 ; ★ CX = 0（即字符为 0）→ 字符串结束
    mov es:[di], cl         ; 偶地址：字符
    mov es:[di+1], al       ; 奇地址：属性
    inc si
    add di, 2
    jmp short s
ok:
    pop di
    pop si
    pop es
    pop dx
    pop cx
    pop bx
    pop ax
    ret
```

**调用示例（和上面的子程序放在同一个程序里）：**

```asm
    mov dh, 12              ; 第 12 行
    mov dl, 32              ; 第 32 列
    mov cl, 02h             ; 黑底绿字
    mov si, offset buf      ; ★ 注意标号别叫 str（MASM 保留字，报 A2085）
    call show_str
```

> **★ 标号千万别叫 `str`！** 本机 MASM 6.11 实测：`str db 32 dup (0)` / `mov si, offset str` 一律报
> `error A2085: instruction or register not accepted in current CPU mode`——
> **不管加不加冒号（`str:`）、不管大小写（`STR`）、不管放在数据段还是代码段，全都报同一条**；改名成 `buf` 立刻通过。
> **这条错误信息看起来像「CPU 型号不对」，其实只是名字撞了保留字**（与第 17 章的 `enter` 是同一类坑，报错文字完全一样）。**安全做法：字符串缓冲区一律叫 `buf`。**

**关键技巧：`mov ch, 0` + `jcxz ok`。** 字符串以 `0` 结尾，要判断「字符是不是 0」。把 `CH` 清零后，`CX` 的值就等于 `CL`（字符），于是 `jcxz`（`CX` 为 0 则跳转）正好实现「遇到 0 就结束」。**这是王爽书里最漂亮的一个小技巧**，比 `cmp cl, 0` / `je ok` 省一条指令。

**易错点：**

- 忘了 `mov ch, 0` → `jcxz` 看的是整个 `CX`，`CH` 是随机值时永远不跳，字符串一直往下写。
- 忘了保护寄存器 → 调用者放在 `DX` 里的行号/列号被破坏（`show_str` 内部用 `mul bl`，见下一条）。
- **`mul bl` 是 8 位乘法：`AX = AL × BL`，只影响 `AX` 和 `CF`/`OF`，根本不碰 `DX`。** 只有 16 位的 `mul bx` 才写 `DX:AX`。所以这里真正被破坏的是 **`AX`**（`AL` 原本是属性值，`mul` 之后 `AX` 变成乘积）——**别把 8 位乘法和 16 位乘法记混**（对照[附录A](附录A-指令系统速查表.md) 的 `mul` 位宽表）。
- `mov es:[di], cl` / `mov es:[di+1], al` 写成 `mov es:[di], cx` → 属性会跟着 `CH` 一起写错。

### C.12.2 `divdw` —— 解决除法溢出

**问题**：`div` 指令要求「被除数的高半部 < 除数」，否则触发 `divide error`（除法溢出中断）。例如 `1000000 ÷ 10`，用 `mov ax, 1000000 的低 16 位` / `mov dx, 0` 会溢出。

**功能**：`dx` = 被除数高 16 位，`ax` = 被除数低 16 位，`cx` = 除数；返回 `dx` = 商高 16 位，`ax` = 商低 16 位，`cx` = 余数。

```asm
; ------------------------------------------------------------
; divdw：不会溢出的 32 位 ÷ 16 位除法
; 参数：dx = 被除数高 16 位，ax = 被除数低 16 位，cx = 除数
; 返回：dx = 商的高 16 位，ax = 商的低 16 位，cx = 余数
; ------------------------------------------------------------
divdw:
    push bx                 ; BX 要用来暂存中间结果
    push ax                 ; ★ 先保存被除数的低 16 位

    mov ax, dx              ; AX = 被除数的高 16 位
    mov dx, 0               ; DX = 0 → DX:AX = 高 16 位（一定不会溢出）
    div cx                  ; AX = int(H/N) = 商的高 16 位
                            ; DX = rem(H/N) = 余数 → ★ 它成为第二次除法的高 16 位
    mov bx, ax              ; BX = 结果的高 16 位

    pop ax                  ; ★ AX = 被除数低 16 位，此时 DX:AX = rem(H/N):L
    div cx                  ; AX = 商的低 16 位，DX = 余数

    mov cx, dx              ; CX = 余数（按要求返回）
    mov dx, bx              ; DX = 商的高 16 位
    pop bx
    ret
```

**验证 `1000000 ÷ 10`**：`1000000 = 0F4240H`，所以 `dx = 000FH`、`ax = 4240H`、`cx = 10`。

| 步骤 | `DX:AX` | ÷ `CX` | 商 | 余数 |
|---|---|---|---|---|
| 第 1 次（只除高 16 位） | `0000:000F` = 15 | 10 | `AX = 1` | `DX = 5` |
| 第 2 次 | `0005:4240H` = 344640 | 10 | `AX = 34464 = 86A0H` | `DX = 0` |

最终 `DX:AX = 0001:86A0H = 65536 + 34464 = 100000` ✓，余数 `CX = 0` ✓。

> **重点：这个算法的本质是「把 32 位除法拆成两次 16 位除法」——先算高 16 位的商和余数，再把「余数 : 低 16 位」当作第二次的被除数。** 因为第一次的商一定小于 `2¹⁶`（高 16 位 < 除数时商 < 1，加上……严格说：`H/N` 的商 ≤ `H` < `2¹⁶`），第二次的被除数高 16 位 `rem(H/N) < N` 一定满足 `div` 的要求，所以**两次除法都绝不会溢出**。

**易错点：**

- `push ax` / `pop ax` 顺序写错 → 第二次除法的被除数错了。
- 第一次除法前忘了 `mov dx, 0` → `DX:AX` 变成了「高 16 位 : 高 16 位」，商完全错。
- 忘了 `push bx` / `pop bx` → 破坏调用者的 `BX`。
- 直接 `div cx` 而不拆 → 大数时触发 `divide error`，程序卡死。

### C.12.3 `dtoc` —— 把 dword 转成十进制字符串

**功能**：`dx` = 高 16 位，`ax` = 低 16 位，`ds:si` = 字符串缓冲区首地址；返回以 `0` 结尾的十进制字符串。

```asm
; ------------------------------------------------------------
; dtoc：把 dword 型数据转成十进制字符串（内部调用 divdw）
; 参数：dx = 高 16 位，ax = 低 16 位，ds:si = 缓冲区首地址
; 返回：ds:si 处是以 0 结尾的十进制字符串
; ------------------------------------------------------------
dtoc:
    push ax
    push bx
    push cx
    push dx
    push si
    push di

    mov di, 0               ; DI = 已经产生的字符个数
s1:
    mov cx, 10              ; 除数 = 10
    call divdw              ; DX:AX ÷ 10 → 商在 DX:AX，余数在 CX
    push cx                 ; ★ 余数入栈暂存（倒序产生、正序输出）
    inc di

    mov cx, ax
    or  cx, dx              ; 商的高、低 16 位「或」起来
    jnz s1                  ; ★ 商不为 0 → 继续除

s2:
    pop cx                  ; 弹出余数（最后压入的最先弹出 = 最高位）
    add cl, 30h             ; ★ 数字 0~9 加 30H 就变成 ASCII 字符 '0'~'9'
    mov ds:[si], cl
    inc si
    dec di
    jnz s2

    mov byte ptr ds:[si], 0 ; 字符串以 0 结尾
    pop di
    pop si
    pop dx
    pop cx
    pop bx
    pop ax
    ret
```

**调用示例：**

```asm
    mov dx, 000fh           ; 被除数高 16 位
    mov ax, 4240h           ; 被除数低 16 位 → 合起来 = 1000000
    mov si, offset buf
    call dtoc               ; buf 里得到 '1','0','0','0','0','0','0',0
```

**两个核心思想：**

1. **除 10 取余，余数倒序产生、正序输出** —— 所以用**栈**暂存余数（`push cx`），最后再依次 `pop` 出来，天然就是正序。
2. **数字 → ASCII 只要加 `30H`**：`0 + 30H = 30H = '0'`，`9 + 30H = 39H = '9'`。见[第 14 章](第14章-端口.md) BCD 那节。

**易错点：**

- 不用栈而直接写进缓冲区 → 得到的是**倒过来**的数字。
- `mov cx, ax` / `or cx, dx` 之后忘了 `CX` 已经被破坏 → 其实这里 `CX` 已经没用了（余数已入栈），是安全的。
- 忘了 `mov byte ptr ds:[si], 0` → 字符串没有结尾，`show_str` 会一直写下去。
- 缓冲区太小 → 覆盖后面的数据。`dword` 最大 `4294967295`（10 位），缓冲区至少要 11 字节。

## C.13 实验十二~实验十七：中断例程系列

这六个实验的共同套路是**五步法**（完整模板见[附录B](附录B-易错点与题型总结.md) 套路 4），区别只在「例程体干什么」。

### C.13.1 实验十二：编写 0 号中断的处理程序

**任务**：改写 0 号中断（除法错误）的处理程序，让它显示 `divide error!` 然后返回 DOS。

```asm
assume cs:code
code segment
start:
    ; ---- ① 把 do0 例程搬到 0:200 ----
    mov ax, cs
    mov ds, ax
    mov si, offset do0
    mov ax, 0
    mov es, ax
    mov di, 200h
    mov cx, offset do0end - offset do0   ; ★ 用两个标号相减算长度
    cld                                  ; ★ DF = 0，正向复制
    rep movsb

    ; ---- ② 改中断向量表第 0 项 → 0:200 ----
    mov word ptr es:[0*4], 200h          ; 偏移部分
    mov word ptr es:[0*4+2], 0           ; 段部分

    mov ax, 4c00h
    int 21h

; ================= 0 号中断处理程序 =================
do0:
    jmp short do0start                   ; ★ 跳过下面的字符串（占 2 字节）
    db "divide error!", 0                ; ★ 提示信息，正好落在 0:202
do0start:
    mov ax, cs                           ; 运行时 CS = 0（因为向量表里填的就是 0）
    mov ds, ax                           ; 让 DS 也指向 0 段
    mov si, 202h                         ; 字符串在 0:202
    mov ax, 0b800h
    mov es, ax
    mov di, 12*160+36*2                  ; 屏幕第 13 行中间
    mov cx, 13                           ; "divide error!" 共 13 个字符
s:
    mov al, ds:[si]
    mov es:[di], al
    inc si
    add di, 2
    loop s

    mov ax, 4c00h                        ; 显示完直接返回 DOS
    int 21h
do0end:
code ends
end start
```

**关键步骤：**

| 步骤 | 做什么 | 为什么 |
|---|---|---|
| ① | `rep movsb` 把例程搬到 `0:200` | 中断发生时 CPU 会去 `0:200` 找代码，例程必须**常驻**在那里 |
| ② | 改 `0:0` 和 `0:2` 两个字 | 中断向量表每项 4 字节：前 2 字节偏移、后 2 字节段 |
| ③ | `jmp short do0start` 跳过字符串 | 否则 CPU 会把 `"divide error!"` 当成指令执行 |
| ④ | 例程里用 `mov ax, cs` / `mov ds, ax` | 例程运行时 `CS = 0`，字符串也在 `0` 段，用它取 `DS` 最省事 |
| ⑤ | 结尾用 `int 21h` 返回 DOS | 除法错误后原程序已经不可恢复，只能退出 |

**怎么验证**：装好之后，在 DEBUG 里手工制造一个除法错误：

```
   -a 100
   xxxx:0100 mov ax, 1
   xxxx:0103 mov bl, 0
   xxxx:0105 div bl          ← 除数 0 → 触发 0 号中断
   xxxx:0107
   -g 105                    ← 运行到 div
```

应该看到屏幕上出现 `divide error!`，然后回到 DOS 提示符。

**易错点：**

- **`db "divide error!", 0` 放在 `do0:` 之后却没写 `jmp short` 跳过** → 直接执行字符串，程序飞掉。
- `mov cx, offset do0end - offset do0` 写成常量 → 例程长度变了就漏搬。
- **忘了 `cld`** → 若之前某处执行过 `std`，`rep movsb` 会反向复制。
- 装完之后**没有真正触发一次** → 报告里就没有「结果截图」，等于没做。
- 以为改完向量表就完事了 → 向量表在 `0:0`，程序里必须用 `es:` 前缀（`ES` 指向 0 段）。

### C.13.2 实验十三：编写、应用中断例程（`int 7ch`）

**任务**：写一个 `int 7ch` 例程，功能为「在 `dh` 行 `dl` 列用 `cl` 属性显示 `ds:si` 指向的 0 结尾字符串」。

**安装部分和实验十二完全一样，只改三处：**

```asm
    mov word ptr es:[7ch*4], 200h        ; ← 改 7ch 项（7ch × 4 = 1F0H）
    mov word ptr es:[7ch*4+2], 0
```

**例程体就是 C.12.1 的 `show_str`，把结尾的 `ret` 换成 `iret`：**

```asm
do7c:
    push ax
    push bx
    push cx
    push dx
    push es
    push si
    push di
    mov ax, 0b800h
    mov es, ax
    mov al, dh
    mov ah, 0
    mov bl, 160
    mul bl
    mov di, ax
    mov al, dl
    mov ah, 0
    mov bl, 2
    mul bl
    add di, ax
    mov al, cl
    mov ch, 0
s:  mov cl, ds:[si]
    jcxz ok
    mov es:[di], cl
    mov es:[di+1], al
    inc si
    add di, 2
    jmp short s
ok: pop di
    pop si
    pop es
    pop dx
    pop cx
    pop bx
    pop ax
    iret                                 ; ★ 中断例程必须用 iret，不能用 ret
do7cend:
```

**调用示例（放在同一个程序的 `start` 里）：**

```asm
    mov dh, 10
    mov dl, 10
    mov cl, 02h
    mov si, offset buf
    int 7ch                              ; 调用自己的例程
    mov ax, 4c00h
    int 21h
buf db 'Hello, 7ch!', 0                  ; ★ 标号别叫 str（MASM 保留字，报 A2085）
```

**关键区别：`iret` vs `ret`。**

| | `call` / `ret` | `int` / `iret` |
|---|---|---|
| 压栈内容 | CS、IP | **FLAGS**、CS、IP |
| 出栈内容 | IP（`ret`）/ IP、CS（`retf`） | IP、CS、**FLAGS** |
| 用错会怎样 | 用 `retf` 返回 `call` → 多弹一个字，栈错位 | 用 `ret` 返回 `int` → 栈上剩一个 FLAGS，**`SP` 错位 2 字节，后面全乱** |

### C.13.3 实验十四：访问 CMOS RAM

**任务**：从 CMOS 读出年月日时分秒，存成 `"YY/MM/DD HH:MM:SS"` 并显示。

```asm
assume cs:code
code segment
start:
    mov ax, cs
    mov ds, ax
    mov si, offset buf              ; 目标缓冲区
    mov bx, offset unit             ; 单元号表
    mov bp, 6                       ; 6 个时间项（★ 用 BP 计数，把 CX 让给移位）

read:
    mov al, ds:[bx]
    out 70h, al                     ; ① 先把【单元地址】送到 70H 端口
    in  al, 71h                     ; ② 再从 71H 端口读【数据】
    mov ah, al
    mov cl, 4
    shr ah, cl                      ; AH = 十位（BCD 高 4 位）
    and al, 0fh                     ; AL = 个位（BCD 低 4 位）
    add ah, 30h                     ; ★ 数字 + 30H = ASCII
    add al, 30h
    mov ds:[si], ah
    mov ds:[si+1], al
    add si, 2
    mov al, ds:[bx+6]               ; 取分隔符（分隔符表紧跟单元号表）
    mov ds:[si], al
    inc si
    inc bx
    dec bp
    jnz read

    mov dx, offset buf
    mov ah, 9                       ; DOS 9 号功能：显示以 '$' 结尾的字符串
    int 21h

    mov ax, 4c00h
    int 21h

buf  db 32 dup (0)                  ; 缓冲区（★ 标号别叫 str：MASM 保留字，报 A2085）
unit db 9, 8, 7, 4, 2, 0            ; CMOS 单元号：年、月、日、时、分、秒
sep  db '/', '/', ' ', ':', ':', '$' ; 分隔符，最后一个用 '$' 收尾
code ends
end start
```

**运行结果**：屏幕上显示当前时间，形如 `26/10/06 15:32:47`。

**CMOS 的关键事实（详见[第 14 章](第14章-端口.md)）：**

| 单元号 | 内容 | 单元号 | 内容 |
|---|---|---|---|
| 0 | 秒 | 7 | 日 |
| 2 | 分 | 8 | 月 |
| 4 | 时 | 9 | 年 |

- **访问顺序不能颠倒**：必须先 `out 70h, 单元号`，再 `in al, 71h`。
- **读出来的是 BCD 码**：`26H` 表示 26，不是 38。所以要先 `shr` 取十位、再 `and 0fh` 取个位。
- **`'0'` 的 ASCII 是 `30H`**，所以数字 `0~9` 加 `30H` 就是对应的字符。

**易错点：**

- 端口顺序写反（先读后写）→ 读到的是上一次的单元。
- 忘了 BCD 拆位，直接把 `26H` 当数字输出 → 显示乱码。
- 用 `int 21h` 9 号功能却用 `0` 结尾 → 必须用 `'$'` 结尾。
- 用 `CX` 同时当循环计数和移位次数 → `mov cl, 4` 把循环计数毁了（本程序改用 `BP` 计数就是为了避开这个）。

### C.13.4 实验十五：安装新的 `int 9` 中断例程

**任务**：写一个新的 `int 9` 例程，按下 `A` 键就满屏显示 `A`；**其他键仍然正常工作**。

```asm
assume cs:code
stack segment
    db 128 dup (0)
stack ends
code segment
start:
    mov ax, stack
    mov ss, ax
    mov sp, 128                     ; ① 设置自己的栈（例程运行时要压栈）

    ; ---- ② 把新例程搬到 0:204 ----
    push cs
    pop ds
    mov si, offset int9
    mov ax, 0
    mov es, ax
    mov di, 204h                    ; ★ 从 204H 开始（200H~203H 留给原向量）
    mov cx, offset int9end - offset int9
    cld
    rep movsb

    ; ---- ③ 把原来的 int 9 向量保存到 0:200 ----
    push es:[9*4]
    pop  es:[200h]
    push es:[9*4+2]
    pop  es:[202h]

    ; ---- ④ 改向量表第 9 项 → 0:204 ----
    cli                             ; ★ 改向量表时关中断，防止改到一半来中断
    mov word ptr es:[9*4], 204h
    mov word ptr es:[9*4+2], 0
    sti                             ; 改完立刻开中断

    mov ax, 4c00h
    int 21h

; ================= 新的 int 9 中断例程 =================
int9:
    push ax
    push bx
    push cx
    push es                         ; 保护现场

    in al, 60h                      ; ★ 从 60H 端口读键盘扫描码

    pushf                           ; ★★ 模拟一次 int 调用
    call dword ptr cs:[200h]        ; ★★ 调用原来的 int 9 例程
                                    ;    这样其他按键仍能进入键盘缓冲区

    cmp al, 1eh                     ; 'A' 的通码是 1EH
    jne int9ret                     ; 不是 A → 直接返回

    mov ax, 0b800h                  ; 是 A → 满屏显示 'A'
    mov es, ax
    mov bx, 0
    mov cx, 2000                    ; 80 × 25 = 2000 个字符位置
s:
    mov byte ptr es:[bx], 'A'       ; 只改字符，属性保持原样
    add bx, 2
    loop s

int9ret:
    pop es
    pop cx
    pop bx
    pop ax
    iret
int9end:
code ends
end start
```

**运行结果**：运行后按 `A`（大写，不按 Shift）→ 满屏 `A`；按其他键 → 正常显示在命令行上（**证明原例程被正确调用**）。

**四个必须讲透的点：**

1. **`pushf` + `call dword ptr cs:[200h]` 是在「模拟一次 `int` 调用」。** 原例程末尾是 `iret`，它要弹出 FLAGS、CS、IP 三个字。`call dword ptr` 只压了 CS 和 IP，所以**必须先 `pushf` 把 FLAGS 压上**，栈布局才对得上。
2. **`dword ptr` 是因为中断向量是「偏移 + 段」4 个字节。** 用 `word ptr` 只会取到偏移。
3. **不调用原例程的后果：键盘全系统失灵。** 原例程负责把扫描码转成字符码送进键盘缓冲区——你不调它，所有按键都不再有反应，**包括 Ctrl+Alt+Del**。
4. **例程从 `0:204` 开始，不是 `0:200`。** 因为 `200H~203H` 这 4 个字节要用来存放原 `int 9` 的向量。

**易错点：**

- 忘了 `pushf` → 原例程的 `iret` 弹错，系统直接崩。
- 忘了 `cli` / `sti` → 改向量表改到一半来了键盘中断，跳到半个向量上。
- 例程里忘了保护寄存器 → 被中断的程序数据错乱。
- 把通码和断码搞混 → 断码 = 通码 + `80H`。`A` 的通码是 `1EH`，断码是 `9EH`。**只比较通码才会「按下才显示」**，比较断码会变成「松手才显示」。
- 没有自己的栈 → 例程和主程序共用 DOS 的栈，深度不够会覆盖数据。

### C.13.5 实验十六、实验十七：用子程序入口地址表分派功能

**任务**：写一个含多个功能子程序的中断例程，用**一张表**根据功能号 `ah` 分派——加功能只要加表项和子程序，不用改分派代码。

```asm
assume cs:code
code segment
start:
    ; ---- 安装到 0:200（同实验十二）----
    mov ax, cs
    mov ds, ax
    mov si, offset setscreen
    mov ax, 0
    mov es, ax
    mov di, 200h
    mov cx, offset setscreenend - offset setscreen
    cld
    rep movsb
    mov word ptr es:[7ch*4], 200h
    mov word ptr es:[7ch*4+2], 0

    ; ---- 测试：功能号 1 = 设置前景色 ----
    mov ax, 0102h           ; ★ AH=01 是功能号，AL=02 是前景色（绿）。
                            ;   AL 必须显式赋值！上面 mov ax,0 之后 AL 一直是 0，
                            ;   功能 1 会把全屏属性字节低 3 位清 0（07H → 00H，黑底黑字），文字全看不见
    int 7ch

    mov ax, 4c00h
    int 21h

; ================= 中断例程：功能号分派 =================
setscreen:
    jmp short set
    table dw sub1, sub2, sub3, sub4   ; ★★★ 子程序入口地址表
set:
    push bx
    push ds
    mov bx, cs
    mov ds, bx                        ; ★ 保证 DS 指向例程所在段（表在 DS 段里）
    cmp ah, 3                         ; ★ 越界检查：功能号 0~3
    ja  sret
    mov bl, ah
    mov bh, 0
    add bx, bx                        ; ★ 表项是【字】，所以下标要 ×2
    call word ptr table[bx]           ; ★ 查表调用
sret:
    pop ds
    pop bx
    iret

; ---- 功能 0：清屏 ----
sub1:
    push bx
    push cx
    push es
    mov bx, 0b800h
    mov es, bx
    mov bx, 0
    mov cx, 2000
sub1s:
    mov byte ptr es:[bx], ' '
    add bx, 2
    loop sub1s
    pop es
    pop cx
    pop bx
    ret

; ---- 功能 1：设置前景色 ----
sub2:
    push bx
    push cx
    push es
    mov bx, 0b800h
    mov es, bx
    mov bx, 1                         ; 从奇地址（属性字节）开始
    mov cx, 2000
sub2s:
    and byte ptr es:[bx], 11111000b   ; ★ 低 3 位清零 = 清前景色
    or  byte ptr es:[bx], al          ; ★ 用 AL 的低 3 位作新前景色
    add bx, 2
    loop sub2s
    pop es
    pop cx
    pop bx
    ret

; ---- 功能 2：设置背景色（AL 的低 3 位 = 背景色）----
sub3:
    push ax
    push bx
    push cx
    push es
    and al, 00000111b                 ; ★ 只用 AL 的低 3 位，免得误改闪烁位
    mov cl, 4
    shl al, cl                        ; ★ 左移 4 位 → 背景色落到 bit6~4
    mov bx, 0b800h
    mov es, bx
    mov bx, 1                         ; 从奇地址（属性字节）开始
    mov cx, 2000
sub3s:
    and byte ptr es:[bx], 10001111b   ; ★ 清掉背景色 3 位（bit6~4），保留前景色和闪烁位
    or  byte ptr es:[bx], al          ; ★ 写入新背景色
    add bx, 2
    loop sub3s
    pop es
    pop cx
    pop bx
    pop ax
    ret

; ---- 功能 3：向上滚动一行 ----
sub4:
    push ax
    push bx
    push cx
    push es
    mov bx, 0b800h
    mov es, bx
    mov bx, 0                         ; 从第 0 行第 0 列开始
    mov cx, 24 * 80                   ; ★ 前 24 行：把「下一行」搬到「本行」
sub4s:
    mov ax, es:[bx+160]               ; ★ 下一行同列的一个字（字符 + 属性），一行 = 160 字节
    mov es:[bx], ax
    add bx, 2
    loop sub4s
    mov cx, 80                        ; ★ 循环结束后 bx = 24×160 = 3840，正好是最后一行的开头
sub4s2:
    mov word ptr es:[bx], 0720h       ; ★ 0720H = 属性 07H（黑底白字）+ 字符 20H（空格）
    add bx, 2
    loop sub4s2
    pop es
    pop cx
    pop bx
    pop ax
    ret

setscreenend:
code ends
end start
```

> **★ 上面 4 个功能都是完整实现，整段代码可以直接汇编运行**（本机 MASM 6.11 实测 `exit=0`）。教材实验十六把功能 2、3 留作练习，这里一并给出，方便你对照自己的写法。

> **★ 调功能 1、2 之前必须给 `AL` 赋值——这是个确定性的坑，不是「有时会错」。**
>
> 安装段里 `mov ax, 0` 之后，`AL` 再没被任何指令改过（`mov es,ax` / `mov di,200h` / `mov cx,…` / `cld` / `rep movsb` / 两条 `mov word ptr es:[…]` 都不碰 `AL`）⟹ **进入 `int 7ch` 时 `AL` 恒等于 `0`**。
>
> 而功能 1 的实现是 `and byte ptr es:[bx], 11111000b` + `or byte ptr es:[bx], al` ⟹ 全屏属性字节低 3 位被清 0，**`07H` 变成 `00H`（黑底黑字），屏幕上所有文字立刻看不见**。学生会以为程序坏了，其实是没传参数。
>
> **规矩：功能号放 `AH`，参数放 `AL`——两个都要显式赋值。** 本程序测试时写 `mov ax, 0102h`（功能 1 + 前景色 2 = 绿）最省事。

**功能 2、3 的两个关键点：**

| 功能 | 关键点 |
|---|---|
| 2：设置背景色 | 掩码换成 `and byte ptr es:[bx], 10001111b`（清 bit6~4 背景色位），`or` 上去的值要先 `shl al, 4` 移到背景色位 |
| 3：向上滚动一行 | `mov ax, es:[bx+160]` + `mov es:[bx], ax` 把下一行搬到本行（**一次搬一个字 = 字符 + 属性一起搬**）；搬完 24 行后 `bx` 正好停在最后一行开头，再用 `mov word ptr es:[bx], 0720h` 清空它（`0720H` = 属性 `07H` + 空格 `20H`） |

**表驱动编程的三个好处：**

| 好处 | 说明 |
|---|---|
| 代码短 | 分派逻辑只有 6 条指令，不管有 4 个还是 40 个功能 |
| 易扩展 | 加功能 = 加一个 `subN` + 在 `table` 里加一行，**分派代码一个字都不用改** |
| 执行快 | 查表是 `O(1)`，比一串 `cmp`/`je` 的 `O(n)` 快 |

> **重点**：**`table dw sub1, sub2, sub3, sub4` 存的是「段内偏移」，不是「段:偏移」。** 因为整个例程被 `rep movsb` **整体**搬到了 `0:200`，表项和子程序之间的相对位置没变，偏移仍然有效。如果要跨段调用，就得用 `dd` 存「偏移 + 段」。详见[第 16 章](第16章-直接定址表.md)。

**易错点：**

- `add bx, bx` 忘了写 → 表项是**字**（2 字节），下标不乘 2 就会跳到半个表项上。
- 没做越界检查 `cmp ah, 3` / `ja sret` → 功能号乱传会跳到任意地址。
- 例程里 `call word ptr table[bx]` 用 `DS` 寻址，但 `DS` 未必指向例程所在段 → **必须显式 `mov ds, cs`**（本程序用 `mov bx, cs` / `mov ds, bx` 中转）。
- 忘了 `pop ds` → 破坏调用者的 `DS`。
- 每个子程序忘了保护自己用到的寄存器 → 功能之间互相干扰。

### C.13.6 六个实验的对照速查

| 实验 | 中断号 | 例程干什么 | 最关键的一点 |
|---|---|---|---|
| 十二 | 0 号（除零） | 显示 `divide error!` 后返回 DOS | `jmp short` 跳过字符串 |
| 十三 | 7ch | 显示字符串 / 求平方 | `ret` 换成 `iret` |
| 十四 | 不用中断 | 读 CMOS 时间并显示 | `out 70h` 先、`in al,71h` 后；BCD 要拆位 |
| 十五 | 9 号（键盘） | 按 `A` 满屏显示 | `pushf` + `call dword ptr cs:[200h]` 调原例程 |
| 十六 | 7ch | 用入口地址表分派多个功能 | 表项是字，下标要 ×2；越界检查 |
| 十七 | 7ch | 同上（更多功能） | 加功能只加表项，不改分派代码 |

## C.14 实验报告模板与实验易错点

**实验报告模板（七段式）：**

| 段落 | 写什么 | 分值占比（参考） |
|---|---|---|
| 一、实验目的 | 2~3 条，抄实验指导书也要结合自己理解改写 | 5% |
| 二、实验环境 | DOSBox 版本 + MASM 版本 + 路径（`D:\Netease\masm`） | 5% |
| 三、源程序 | **完整代码 + 逐行中文注释**（注释要说明「这行在干什么」） | 25% |
| 四、**跟踪记录** | **DEBUG 的 `-r` / `-d` 输出粘贴**，逐步说明寄存器与标志位怎么变 | **30%** |
| 五、运行结果 | 程序实际输出的截图或文字 | 15% |
| 六、问题与解决 | 遇到什么报错、怎么定位、怎么改好的 | 10% |
| 七、思考题 | 回答实验指导书后面的思考题 | 10% |

> **重点：第四段「跟踪记录」占分最重，也是唯一无法编造的部分。** 建议的写法是「**贴一段 DEBUG 输出 + 写一句解读**」交替进行，例如：
>
> ```
> -r
> AX=0000  BX=0000  CX=0000  DX=0000  SP=FFEE  BP=0000  SI=0000  DI=0000
> DS=075A  ES=075A  SS=075A  CS=075A  IP=0100  NV UP EI PL NZ NA PO NC
>
> 解读：程序刚装入，DS=ES=SS=CS=075A（都在 PSP 段），IP=0100 是 .COM 的入口；
>       标志位全是初始值（NV=无溢出、UP=正向、EI=开中断、PL=正、NZ=非零、NC=无进位）。
> ```

**实验易错点 ≥12 条：**

1. **忘了 `mov ax, 4c00h` / `int 21h` 正常返回 DOS。** 后果：程序跑飞、DOSBox 卡死、只能重启 DOSBox。**每个程序结尾都要有这一对。**
2. **以为 `assume` 会真的把段地址装进 `DS`。** 错在哪：`assume` 是**伪指令**，只在汇编期起作用，**不产生任何机器码**。正确做法：必须自己写 `mov ax, data` / `mov ds, ax`。
3. **写 `mov ds, 1000h`。** 错在哪：**段寄存器不能用立即数赋值**，汇编器直接报错。正确做法：`mov ax, 1000h` / `mov ds, ax`。
4. **用 `mov ax, [0]` 想访问内存 0 处。** 错在哪：MASM 把 `[0]` 当成**常量表达式 0**，生成 `mov ax, 0`（机器码 `B8 0000`），**静默出错不报错**。正确做法：`mov ax, ds:[0]`，或先 `mov bx, 0` 再 `mov ax, [bx]`。
5. **写 `jmp 1234:0`。** 错在哪：那是 **DEBUG 的 `a` 命令语法**，MASM 报 `error A2096`。正确做法：`jmp far ptr 标号`，或 `push 段` + `push 偏移` + `retf`。
6. **写 `mov ax, 123B` 想表示十六进制。** 错在哪：MASM 里 `B` 是**二进制后缀**，`123B` 被当成二进制数，报 `error A2048: nondigit in number`。正确做法：写 `123BH`。
7. **用 `c` 当标号。** 错在哪：`C` 是 MASM 的**语言说明符保留字**，报 `error A2008: syntax error : c`。正确做法：改名 `tb`、`cnt` 之类。
8. **嵌套循环里内层 `loop` 把外层的 `CX` 改掉了。** 错在哪：`loop` 只用 `CX` 一个计数器。正确做法：内层循环前 `push cx`、结束后 `pop cx`，或在数据段开一个字单元暂存。
9. **栈不平衡导致 `ret` 飞掉。** 错在哪：`push` 了 3 个只 `pop` 了 2 个，`ret` 弹出的是错误地址。正确做法：**每个子程序的 `push`/`pop` 必须严格逆序配平**，出口只有一个。
10. **在 DOSBox 里想改源程序却找不到编辑器。** 错在哪：**本机 `D:\Netease\masm` 目录树下没有 `EDIT.COM` 也没有 `EDIT.EXE`**（详见 C.2 的「第 ⑤ 步」说明），敲 `edit 1.asm` 只会得到 `Bad command or file name`。正确做法：**在 Windows 侧用记事本（或 `D:\Netease\masm\Masm\winasm.exe`）改完、按 ANSI 编码存盘，再切回 DOSBox 汇编**；确实想在 DOSBox 内编辑，必须自备 `EDIT.COM` 放到与 `MASM.EXE` 同一目录。
11. **`.obj` / `.exe` 被占用导致 `link` 失败。** 错在哪：上一次的 `1.exe` 还在运行（或 DEBUG 没退出），文件被锁。正确做法：先在 DOSBox 里 `q` 退出 DEBUG、或关掉正在运行的程序，再重新 `link`。
12. **`int 21h` 的 9 号功能字符串忘了用 `'$'` 结尾。** 错在哪：9 号功能靠 `'$'` 判断结束，用 `0` 结尾会一直输出到内存里碰巧出现 `'$'` 为止，屏幕刷满乱码。正确做法：`db 'hello', '$'`。
13. **源程序存成了 UTF-8 带 BOM。** 错在哪：MASM 6.11 把开头三字节 BOM 当乱码，第一行就报语法错。正确做法：记事本存盘时编码选 **ANSI**。
14. **中断例程里用 `ret` 而不是 `iret`。** 错在哪：`int` 压了 FLAGS、CS、IP 三个字，`ret` 只弹一个 IP，**栈错位 2 字节**，返回后程序全乱。正确做法：中断例程一律 `iret`。
15. **改了 `int 9` 却不调用原例程。** 错在哪：原例程负责把扫描码送进键盘缓冲区，不调它**整个系统的键盘都会失灵**。正确做法：`pushf` + `call dword ptr cs:[200h]`。
16. **忘了给栈段预留空间。** 错在哪：`stack segment` / `stack ends` 中间什么都不写，栈段长度为 0，`push` 直接覆盖段外数据。正确做法：`db 128 dup (0)` 或 `dw 0,0,...`。

## C.15 自测题

**1.** 用 DEBUG 跟踪时发现 `mov ax, [0]` 被汇编成了 `mov ax, 0000h`（机器码 `B8 00 00`），为什么？怎么改？
**答**：**MASM 把 `[数字]` 当成常量表达式，不当作内存地址**——所以 `mov ax, [0]` 等价于 `mov ax, 0`（机器码 `B8 0000`），而且**不报错**（静默错误）。DEBUG 的 `a` 命令里 `[0]` 确实是内存地址，两者语法不同。改法有两种：① 加段前缀 `mov ax, ds:[0]`（生成 `A1 00 00`，才是真正取内存；`AX` 作目的时用 3 字节的 `A1 moffs16` 短形式，非 `AX` 才是 `8B 06 xx xx`）；② 先把地址放进寄存器再寻址：`mov bx, 0` / `mov ax, [bx]`（`8B 07`）。

**2.** 程序用 `int 3` 下断点后，按 `t` 发现跳到了 ROM 区（IP 变成很小的值、反汇编出来是 BIOS 代码），怎么继续单步？
**答**：这说明 `t` 进入了 INT 3 的中断处理程序。**手工把 IP 改回 `CC` 后面那条指令的地址**即可：先用 `-u` 找到 `CC` 那一行，**下一行开头的地址**就是目标（例如 `0014`）；然后 `-r ip`，输入 `0014`，再按 `t` 就能正常往下走。本机实测用 `0017H` / `0024H` / `002AH` 这类值都可以。

**3.** `div` 指令报 `divide error` 的可能原因有哪些？
**答**：两种。① **除数为 0**（`div bl` 而 `bl = 0`）。② **商溢出**——`div` 要求被除数的高半部必须小于除数，即 8 位除法要 `AH < 除数`、16 位除法要 `DX < 除数`；不满足时商放不进 `AL`/`AX`，触发除法错误。**特别容易忘的是 16 位除法前忘了 `mov dx, 0`**，此时 `DX` 是随机值，只要它 ≥ 除数就溢出。解决办法：除前清 `dx`、检查除数；大数除法用 `divdw` 拆成两次（见 C.12.2）。

**4.** 为什么 `1.exe` 用 DEBUG 打开时 `DS` 比 `CS` 小 `10H`？
**答**：`.exe` 装入内存时，前面有 **256 字节 = `10H` 个节**的 **PSP（程序段前缀）**。DOS 把 `DS` 和 `ES` 指向 PSP 的段地址，而 `CS` 指向代码段；代码段紧跟在 PSP 之后，所以 `CS = DS + 10H`，反过来 `DS = CS − 10H`。用 `-d ds:0` 能看到 PSP 的开头是 `CD 20`（一条 `int 20h`）。

**5.** 一个两层循环，外层 3 次、内层 16 次，结果只跑了 1 个外层就结束了，最可能是什么原因？
**答**：**内层的 `loop` 把外层的 `CX` 改掉了。** `loop` 指令只用 `CX` 一个计数器，内层跑完 `CX` 变成 0，外层 `loop` 判断时直接结束。改法：外层循环体开头 `push cx`、结尾 `pop cx`（注意 `pop` 必须在 `loop` **之前**），或在数据段开一个字单元暂存外层计数。

**6.** 往显存写字符时，为什么 `mov es:[di], ax` 一次就能同时写好「字符 + 属性」？
**答**：因为 `AX` 的低字节 `AL` 是字符、高字节 `AH` 是属性，而内存里**低字节存在低地址**（偶地址 = 字符位置）、**高字节存在高地址**（奇地址 = 属性位置）。**小端序的字节顺序正好和显存「偶地址字符、奇地址属性」的布局吻合**，所以一个字的传送就完成了两个字节的写入。

**7.** 中断例程末尾用 `ret` 代替 `iret` 会发生什么？
**答**：`int n` 执行时压栈顺序是 **FLAGS → CS → IP**（共 3 个字）。`iret` 依次弹出 IP、CS、FLAGS，正好配平；`ret` 只弹出 IP 一个。结果是**栈顶残留 2 个字（CS 和 FLAGS），`SP` 比 `iret` 正常返回时小 4**，返回后所有 `push`/`pop` 的地址全部错位，程序行为不可预测（通常表现为「返回后执行了完全无关的代码」）。**中断例程必须用 `iret`。**

**验算**：设中断前 `SP = S`。`push FLAGS` → `S−2`，`push CS` → `S−4`，`push IP` → `S−6`（栈顶是 IP）。
- 用 `iret`：弹 3 个字 → `SP = S`（正常）。
- 用 `ret`：只弹 IP 一个字 → **`SP = S−4`，比正常值小 4**（栈往低地址方向多留了 4 字节垃圾）。
- 例：`S = 0100H` 时，`iret` 后 `SP = 0100H`，`ret` 后 `SP = 00FCH`。

**8.** 改了 `int 9` 之后键盘完全失灵，包括 `Ctrl+Alt+Del` 都没反应，为什么？
**答**：因为新的 `int 9` 例程**没有调用原来的例程**。原 BIOS 例程负责把从 `60H` 端口读到的扫描码转换成字符码、送进键盘缓冲区，供 `int 16h` 和 DOS 读取。不调用它，扫描码就进不了缓冲区，**整个系统的键盘输入都断了**。正确做法是在读走 `60H` 端口的扫描码之后，用 `pushf` + `call dword ptr cs:[200h]` 调用原例程（`pushf` 是为了让原例程末尾的 `iret` 有正确的栈布局）。

**9.** 从 CMOS 读出来的年是 `26H`，直接按数字输出会得到什么？正确做法是什么？
**答**：`26H` 是 **BCD 码**，表示十进制 **26**，不是 38。如果直接按数值处理会得到 38，完全错。正确做法：拆成高 4 位和低 4 位——`mov ah, al` / `mov cl, 4` / `shr ah, cl` 取十位，`and al, 0fh` 取个位；再各加 `30H` 转成 ASCII 字符（`2 + 30H = '2'`，`6 + 30H = '6'`），最后拼成字符串 `"26"`。

**10.** `table dw sub1, sub2, sub3, sub4` 里存的是「偏移」还是「段:偏移」？为什么可以直接 `call word ptr table[bx]`？
**答**：存的是**段内偏移**（每个表项一个字）。可以直接近调用，是因为整个例程（包括表和各子程序）被 `rep movsb` **整体**搬到了 `0:200`，表项与子程序之间的**相对位置没变**，偏移仍然指向正确的子程序。如果要跨段调用，就必须用 `dd` 存「偏移 + 段」，再用 `call dword ptr`。
