---
title:  自旋霍尔电导计算简介
tags: DFT
pageview: true
layout: article
license: ture
toc: ture
key: a20230109
header:
  theme: dark
  background: 'linear-gradient(135deg, rgb(21, 81, 138), rgb(147, 15, 139))'
aside:
    toc: true
sitemap: true
mathjax: true
mathjax_autoNumber: true
author: Leoi
---
本文原创，转载请联系 lvr#mail.ustc.edu.cn（＃换为@）

[PDF版下载](https://leoijeon.github.io/assets/自旋霍尔电导计算简介.pdf)

# 简介

本介绍包括两个软件，一为 **PAOFlow**，用于计算自旋霍尔电导张量元的数值；另一为 **Linear Response Symmetry**，根据晶体的对称性（磁空间群）生成 SHC 张量，只含有各张量元基本关系，不是具体数值。

例如我们通过 **Linear Response Symmetry** 计算得到 $\sigma^z$=
$$
\left[\begin{array}{cccc}
     a & -b & 0 \\
     b &  a & 0  \\
     0 &  0 & c
\end{array}\right]$$

同时通过**PAOFlow** 计算得到 $\sigma^z$=
   $$ \left[ \begin{array}{cccc}
        -9.16 & -13.7 & 0 \\
        13.7 & -9.16 & 0  \\
        0 & 0 & 0.1
    \end{array}\right]$$ 对比这两个软件的结果，我们可以看出 **PAOFlow** 的结果与 **Linear Response Symmetry** 的结果对称性相符。

**Wannier90** 也可以做类似 **PAOFlow** 的工作，但其准确性强烈依赖于 Wannier 局域函数做的好不好。而 **PAOFlow** 直接从 **Quantum Espresso** 的结果生成赝原子轨道，对称性保持的更好些。

# **PAOFlow**

**PAOFlow** 是基于 **QE** 计算结果，将其转换为以赝原子轨道（pesudo-atom orbit, PAO）为基的形式，进而生成哈密顿量文件（可保存成 z2_pack 格式，与 **Wannier90** 生成的 hr.dat 文件格式相同）。

**PAOFlow** 代码基于 python，需要先安装 python，再通过
`pip install paoflow` 安装。

执行过程：pw.x 计算 scf、nscf； projwfc.x 执行投影； python main.py 执行 **PAOFlow** 程序。（输入文件参见 example 文件）

其执行是通过在 main.py 中用 "import PAOFLOW" 调用 **PAOFlow** 包，再通过在定义主函数 "def main()" 里设置需要执行哪些模块。（详细内容见说明文件）

基本语句如 PAOFLOW.z2_pack(fname = 'z2_pack-hamitonian.dat')，"PAOFLOW"是调用的包名，点号后的"z2_pack"是模块名，括号内的是参数。（详细内容见说明文件） 例子：

    [user@master paoflow]$ more main.py
        from PAOFLOW import PAOFLOW
        
        def main():
        paoflow = PAOFLOW.PAOFLOW(savedir='./tmp/SrIrO3.save',verbose=True,npool=4)
        paoflow.read_atomic_proj_QE()
        paoflow.projectability()
        paoflow.pao_hamiltonian(symmetrize=True)
        paoflow.z2_pack(fname='z2pack_hamiltonian.dat')
        paoflow.spin_Hall(emin=-1., emax=1.)
        path = 'Y-S-R-T-Y'
        sym_points = {'G':[0.00,  0.00,  0.00], 
            'S':[0.50,  0.50,  0.00], 
            'Y':[0.00,  0.50,  0.00],
            'R':[0.50,  0.50,  0.50], 
            'T':[0.00,  0.50,  0.50], 
            'Z':[0.00,  0.00,  0.50]}
        paoflow.bands(ibrav = 0,nk=400,band_path=path,high_sym_points=sym_points)
        paoflow.finish_execution()
        
        if __name__== '__main__':
        main()

常用模块有：
pao_hamiltonian、bands、spin_Hall，用于生成哈密顿量、能带文件（用于绘图对比）、自旋霍尔电导。

运行命令:

        mpirun -np 32 python main.py > pao.out

计算生成的文件保存在 ./output/ 下。这里用到的有：能带及高对称点 `bands_0.dat, kpath_points.txt`、自旋霍尔电导张量元 `shcEf-x_yz.dat`、哈密顿量文件 `z2pack_hamiltonian.dat`。

官网 <http://aflowlib.org/src/PAOFlow/>。详细参数说明见 Computational Materials Science 200, 110828 (2021)。

**注1：** **PAOFlow** 代码中设计了并行功能，在计算前通过 `pip install mpi4py` 安装 python 的并行模块。在 main.py 中合理设置 `npool`,提交计算任务时即可并行，加快计算。

**注2：** 如果你还没有配置好 conda 环境，请先安装 annaconda。然后在你自己 conda 环境中安装这些软件。

#  **Linear Response Symmetry**

**Linear Response Symmetry**（包名为 symmetr）是一个用于确定磁性和非磁性晶体的各种对称特性的程序。目前该程序可以确定各种线性响应特性的对称性和经典磁相互作用的对称性。

symmetr 包与 **PAOFlow** 一样，都是基于 python 的。故安装：`pip install symmetr`

例子：

        SrIrO3  #标题行
        0       #误差（tolerance）。输入 0 则使用默认值 1d-6
        1       #给出晶格参数的方式（解释在后面）
        5.639900000000000   0.000000000000000   0.000000000000000   #晶格的三个向量
        0.000000000000000   7.889799999999998   0.000000000000000
        0.000000000000000   0.000000000000000   5.639900000000000
        2       #给出晶胞基矢的方式
        P       
        20      #原子数
        Ir Ir Ir Ir Sr Sr Sr Sr O O O O O O O O O O O O         #下面的原子坐标须与这里一一对应
        magnetic   #体系是否有磁性。每个原子的磁矩写在下面原子相对坐标后，如果没有磁矩则写0 0 0
        0.0000000000        0.5000000000        0.0000000000     0      0     0
        0.5000000000        0.0000000000        0.5000000000     0      0     0
        0.5000000000        0.5000000000        0.5000000000     0      0     0
        0.0000000000       -0.0000000000       -0.0000000000     0      0     0
        0.5407216681        0.7500000000        0.0073552187     0      0     0
        -0.0407216681       0.2500000000        0.5073552187     0      0     0
        0.4592783319        0.2500000000       -0.0073552187     0      0     0
        0.0407216681        0.7500000000        0.4926447813     0      0     0
        0.2931764767        0.5423229561        0.2070156506     0      0     0
        0.2068235233        0.0423229561        0.7070156506     0      0     0
        0.2068235233        0.4576770439        0.7070156506     0      0     0
        0.7068235233        0.0423229561        0.7929843494     0      0     0
        0.7068235233        0.4576770439        0.7929843494     0      0     0
        0.7931764767        0.9576770439        0.2929843494     0      0     0
        0.7931764767        0.5423229561        0.2929843494     0      0     0
        0.2931764767        0.9576770439        0.2070156506     0      0     0
        0.4769442411        0.7500000000        0.5830988832     0      0     0
        0.0230557589        0.2500000000        0.0830988832     0      0     0
        0.5230557589        0.2500000000        0.4169011168     0      0     0
        0.9769442411        0.7500000000        0.9169011168     0      0     0

给出晶格参数的方式： **1** 为使用三个向量（笛卡尔坐标下的惯用晶胞（conventional unit cell））； **2** 为给出长度与夹角 (a,b,c,alpha,beta,gamma）（角的单位为度（degree））

给出晶胞基矢： **1** 为以惯用晶胞基矢写出的（每个向量分行写；小数点后至少有三位；若晶胞与惯用晶胞相同，则写1,0,0、0,1,0
和 0,0,1）； **2** 为输入已知的晶胞中心： **P** (primitive or no known
centering), **I** (body-centered), **F** (face-centered),
**A**,**B**,**C** (base centered), or **R** (rhombohedral centered with
coordinates of centered points at (2/3,1/3,1/3) and (1/3,2/3,2/3), the
convention used in International Tables of Crystallography).

执行命令：

        symmetr res j E -f findsym.in > vv.out

得到计算结果文件 `vv.out`，其中包含了电导张量。其中 res 用于计算线性响应的对称性结果。`j` 代表电流，是可观测量。`E` 代表电场。其中 `j` 可以被 `s.j` （自旋流）替换。`-f findsym.in` 表示 `findsym.in` 文件作为输入文件。

同理，执行

        symmetr res s.j E -f findsym.in > svv.out

则可以得到自旋流对电场的响应，即自旋霍尔电导张量（Spin Hall Conductance, SHC）。

官网 <https://bitbucket.org/zeleznyj/linear-response-symmetry/src/master/>。参数说明文件为该包内 `README.rst` 和 `symmetr/findsym/findsym.txt`。
