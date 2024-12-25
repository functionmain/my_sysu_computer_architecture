# 实验操作

根据提供的 `README.md` 文件内容，实验流程可以分为以下几个主要步骤：

### 1. 环境准备（Prerequisites）

在开始实验之前，需要确保系统满足以下配置要求：

- G++ v6.3.0 20170516
- CMake v3.20.2
- md5sum v8.26
- Perl v5.24.1
- [DEPRECATED] Megatools 1.11.0

### 2. 安装（Installation）

1. **克隆GitHub仓库**：

```
git clone https://github.com/CMU-SAFARI/Pythia.git
```

2. **克隆bloomfilter库**：

```
cd Pythia
git clone https://github.com/mavam/libbf.git libbf
```

3. **构建bloomfilter库**：

```
cd libbf
mkdir build && cd build
cmake ../
make clean && make
```

4. **构建Pythia**：

```
cd $PYTHIA_HOME
./build_champsim.sh multi multi no 1
```

（这里`multi multi no 1`分别代表L1、L2和LLC的预取器配置，`1`代表单核）

5. **设置环境变量**：

```
source setvars.sh
```

### 3. 准备跟踪数据（Preparing Traces）

1. **下载必要的ChampSim跟踪数据**：

```
mkdir $PYTHIA_HOME/traces/
cd $PYTHIA_HOME/scripts/
perl download_traces.pl --csv artifact_traces.csv --dir ../traces/
```

（脚本将下载233个跟踪数据，总大小约为52GB）

2. **校验下载的跟踪数据**：

```
cd $PYTHIA_HOME/traces
md5sum -c ../scripts/artifact_traces.md5
```

3. **如果跟踪数据下载在其他路径，更新**`experiments/MICRO21_1C.tlist`**和**`experiments/MICRO21_4C.tlist`**中的路径**。

### 4. 实验工作流（Experimental Workflow）

#### 发起实验（Launching Experiments）

1. **使用**`create_jobfile.pl`**脚本创建实验命令**：

```
cd $PYTHIA_HOME/experiments/
perl ../scripts/create_jobfile.pl --exe $PYTHIA_HOME/bin/perceptron-multi-multi-no-ship-1core --tlist MICRO21_1C.tlist --exp MICRO21_1C.exp --local 1 > jobfile.sh
```

2. **在**`experiments`**目录下运行实验**：

```
cd experiments_1C
source ../jobfile.sh
```

#### 统计汇总（Rolling up Statistics）

1. **使用**`rollup.pl`**脚本汇总统计数据**：

```
cd experiments_1C/
perl ../../scripts/rollup.pl --tlist ../MICRO21_1C.tlist --exp ../MICRO21_1C.exp --mfile ../rollup_1C_base_config.mfile > rollup.csv
```

2. **将**`rollup.csv`**文件导出到数据处理器中（如Python Pandas、Excel等）以分析**。
