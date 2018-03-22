# profile 分析 {#6-profile-分析}

算法工程师将算法移到 vsp\_simulate 工程上来，除了将算法跑通，还有一项重要的功能就是优化算法，要尽可能节省内存空间，尽可能的提升算法的执行效率，本章就讲讲如何利用 profile 来快速定位哪个算法函数效率低下。

### 性能评测工具 gprof

gprof 是 GNU Binutils 之一，它能为 Linux 程序精确分析性能瓶颈，能精确地给出函数被调用的时间和次数以及函数的调用关系，堪称神器。我们的 dsp 工具链也集成了此工具叫 xt-gprof。

### xt-gprof 命令产生 profile

如何使用该命令，请阅读 tb/Makefile。产生 profile 报告前，建议将 vsp\_main.c 中的宏 CALC\_PROFILE 打开，能缩短产生 profile 报告的时间。 下图是一份 profile 报告。![](https://nationalchip.gitbooks.io/vsp_simulate/content/assets/%E9%80%89%E5%8C%BA_115.png)

**报告说明：**

* **%**: 最左边的 %，它表示该函数消耗时间占程序所有时间百分比，注意这个是占整个程序生命期的百分比，可以考虑通过优化此函数来提升算法的执行效率。
* **cumulative cycles\(K\)**: 函数累积所执行的 cycles 数，单位是 K，一般没有太多实际意义。
* **self cycles\(K\)**: 整个程序生命期内，函数本身执行时间和，注意不包含调用的时间，理论上等于 calls \* \(self cycles/call\)，也没有太多实际意义。
* **calls**: 整个程序生命期内，函数调用的次数
* **self cycles/call**: 函数平均执行时间，注意不包含调用时间
* **total cycles/call**: 函数平均执行时间，包含调用时间，这个指标应该是重点关照的时间。
* **name**: 函数名称，需要注意的是静态函数是不会被跟踪的。 算法工程师可以依据这份报告来对自己的算法去做优化。

### cycle 分析

首先我们得知道dsp的主频是多少，**假设 300M，意味着 1 秒钟可以跑 300M 的 cycle**。再结合 6.2 去分析算法需要执行多少 cycles，从而分析出来是否满足要求。


