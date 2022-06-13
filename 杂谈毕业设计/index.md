# 杂谈毕业设计


<!--more-->

> 《基于人工智能的大气重点污染物浓度预测》

## 毕设灵感

---

很久之前，我就有将循环神经网络用在大气污染物浓度预测上的想法，可以自由选择题目的课程设计也就成了实现这个想法最好的平台。最初的想法非常简单：随便找几个监测站的大气污染物数据，把别人写好的 LSTM、GRU 之类的模型拿来用一用，再随便拟合一下就算大功告成。

考研初试结束后，我也总算有了一些时间继续做一些自己喜欢的工作。在学习的过程中，我发现了一个以前没有注意到过的领域 — [视频预测(Video Prediction)](https://paperswithcode.com/task/video-prediction)领域。这个方向的问题大概为：输入一个视频的前几帧，让你去预测下一帧的图像。针对这个领域的问题，非常具有代表性的一个工作就是 [ConvLSTM](https://proceedings.neurips.cc/paper/2015/file/07563a3fe3bbe7e3ba84431ad9d055af-Paper.pdf)，这是一篇NIPS2015的论文，讲的是利用 CNN 和 LSTM 结合，通过前几帧的卫星图像，来预测以后的卫星图像，进而对降水进行预测。我忽然意识到，如果将大气污染物的浓度分布作为原始输入，不也可以预测未来的污染物浓度分布吗？

这激起了我的好奇心，检索了一下相关的工作，有一篇[上海交大的硕士毕业论文](https://kns.cnki.net/kcms/detail/detail.aspx?dbcode=CMFD&filename=1021679960.nh)完成了这项工作，作者使用的就是原始的 ConvLSTM。事实上，从 ConvLSTM 出现至今，视频理解方向的工作已经有了很多进步，从 [Papers with Code](https://paperswithcode.com/task/video-prediction) 的网站上就可以看到。但是这些变化，还没有应用到大气污染物预测的领域，作为一个专业的学术易拉罐，这样的好机会我是不会放过的。

就这样，毕设难度一下子顶了上去，从原本的浓度单点预测，变成了现在的浓度分度预测。

## 实验推进

---

深度学习，模型、数据、计算资源缺一不可。

模型方面，目前实现了 ConvLSTM 和 ConvGRU 两个模型，打算最后要是有时间还可以再做一下 Transformer 的结合，虽然说大概率没希望，因为数据量太少了，难以支撑起 Transformer 的效果。

数据方面，我原本想要自己安装一下 WRF-Chem 来跑些训练数据出来，先是根本找不到 WRF-Chem 完整的学习资料，导致安装摸爬滚打完之后，连软件该怎么运行都搞不清楚；后是排放清单数据拿不到，最终只好搁置了这个计划。转头去找学姐发文时用的数据，终于找到了大概有 550 多条数据，数据量堪忧。

计算资源方面，目前只有我寝室使用的 1050Ti。

总之就是前途非常渺茫。

### 模型实现

代码仓库：[DL_PM_Pred](https://gitee.com/Blathers/dl_-pm_-pred)，公式如下：

#### ConvLSTM


$$
I_{t} = Sigmoid(Conv(X_t, W_{xi}) + Conv(H_{t-1}, W_{hi}) + b_i) \\\
F_{t} = Sigmoid(Conv(X_t, W_{xf}) + Conv(H_{t-1}, W_{hf}) + b_f) \\\
O_{t} = Sigmoid(Conv(X_t, W_{xo}) + Conv(H_{t-1}, W_{ho}) + b_o)  \\\ 
\tilde{C_t} = Tanh(Conv(X_t, W*{xc}) + Conv(H_{t-1}, W_{hc}) + b_h) \\\ 
C_t = F_t \odot C_{t-1} + I_t \odot \tilde{C_t} \\\
H_t = O_t  \odot Tanh(C_t)\\\
Pred = ReLU(Conv(X_t, W*{xx}) + Conv(H_t, W_{xh}) + b_{p}) \\\
$$

#### ConvGRU


$$
R_{t} = Sigmoid(Conv(X_t, W_{xr}) + Conv(H_{t-1}, W_{hr})+b_r) \\\ 
Z_{t} = Sigmoid(Conv(X_t, W_{xz}) + Conv(H_{t-1}, W_{hz})+b_z) \\\ \tilde{H_t} = Tanh(Conv(X_t, W*{xh})+Conv(R_t \odot H_{t-1}, W_{rh}) + b_{h}) \\\ 
H_t = Z_t \odot H_{t-1} + (1 - Z_t) \odot \tilde{H} \\\ 
Pred = ReLU(Conv(X_t, W_{xx}) + Conv(H_t, W_{xh}) + b_{p}) \\\
$$

### 数据分析


#### 模拟范围

模拟范围如图：

![](https://s3.bmp.ovh/imgs/2022/04/23/22967f3605ac175e.png)

### 变量选择

解包 wrfout 文件后，文件保存了 281 个变量，分别是：

```python
['Times', 'XLAT', 'XLONG', 'LU_INDEX', 'ZNU', 'ZNW', 'ZS', 'DZS', 'VAR_SSO', 'U', 'V', 'W', 'PH', 'PHB', 'T', 'HFX_FORCE', 'LH_FORCE', 'TSK_FORCE', 'HFX_FORCE_TEND', 'LH_FORCE_TEND', 'TSK_FORCE_TEND', 'MU', 'MUB', 'NEST_POS', 'P', 'PB', 'FNM', 'FNP', 'RDNW', 'RDN', 'DNW', 'DN', 'CFN', 'CFN1', 'THIS_IS_AN_IDEAL_RUN', 'P_HYD', 'Q2', 'T2', 'TH2', 'PSFC', 'U10', 'V10', 'RDX', 'RDY', 'RESM', 'ZETATOP', 'CF1', 'CF2', 'CF3', 'ITIMESTEP', 'XTIME', 'QVAPOR', 'QCLOUD', 'QRAIN', 'QICE', 'QSNOW', 'QGRAUP', 'SHDMAX', 'SHDMIN', 'SNOALB', 'TSLB', 'SMOIS', 'SH2O', 'SMCREL', 'SEAICE', 'XICEM', 'SFROFF', 'UDROFF', 'IVGTYP', 'ISLTYP', 'VEGFRA', 'GRDFLX', 'ACGRDFLX', 'ACSNOM', 'SNOW', 'SNOWH', 'CANWAT', 'SSTSK', 'COSZEN', 'LAI', 'VAR', 'MAPFAC_M', 'MAPFAC_U', 'MAPFAC_V', 'MAPFAC_MX', 'MAPFAC_MY', 'MAPFAC_UX', 'MAPFAC_UY', 'MAPFAC_VX', 'MF_VX_INV', 'MAPFAC_VY', 'F', 'E', 'SINALPHA', 'COSALPHA', 'HGT', 'TSK', 'P_TOP', 'T00', 'P00', 'TLP', 'TISO', 'TLP_STRAT', 'P_STRAT', 'MAX_MSTFX', 'MAX_MSTFY', 'RAINC', 'RAINSH', 'RAINNC', 'SNOWNC', 'GRAUPELNC', 'HAILNC', 'CLDFRA', 'SWDOWN', 'GLW', 'SWNORM', 'OLR', 'XLAT_U', 'XLONG_U', 'XLAT_V', 'XLONG_V', 'ALBEDO', 'CLAT', 'ALBBCK', 'EMISS', 'NOAHRES', 'TMN', 'XLAND', 'UST', 'PBLH', 'HFX', 'QFX', 'LH', 'ACHFX', 'ACLHF', 'SNOWC', 'SR', 'SAVE_TOPO_FROM_REAL', 'EROD', 'CLDFRA2', 'RAINPROD', 'EVAPPROD', 'UST_T', 'ROUGH_COR', 'SMOIS_COR', 'DUSTLOAD_1', 'DUSTLOAD_2', 'DUSTLOAD_3', 'DUSTLOAD_4', 'DUSTLOAD_5', 'DRYDEPVEL', 'GRASET_1', 'GRASET_2', 'GRASET_3', 'GRASET_4', 'GRASET_5', 'DRYDEP_1', 'DRYDEP_2', 'DRYDEP_3', 'DRYDEP_4', 'DRYDEP_5', 'EDUST1', 'EDUST2', 'EDUST3', 'EDUST4', 'EDUST5', 'DRY_DEP_LEN', 'DRY_DEP_FLUX', 'WET_DEP_FLUX', 'EBIO_ISO', 'EBIO_API', 'LAI_VEGMASK', 'dvel_o3', 'PM2_5_DRY', 'PM10', 'DMS_0', 'PHOTR201', 'PHOTR202', 'PHOTR203', 'so2', 'sulf', 'no2', 'no', 'o3', 'hno3', 'h2o2', 'ald', 'hcho', 'op1', 'op2', 'paa', 'ora1', 'ora2', 'nh3', 'n2o5', 'no3', 'pan', 'hc3', 'hc5', 'hc8', 'eth', 'co', 'ol2', 'olt', 'oli', 'tol', 'xyl', 'aco3', 'tpan', 'hono', 'hno4', 'ket', 'gly', 'mgly', 'dcb', 'onit', 'csl', 'iso', 'hcl', 'ho', 'ho2', 'so4aj', 'so4ai', 'nh4aj', 'nh4ai', 'no3aj', 'no3ai', 'naaj', 'naai', 'claj', 'clai', 'orgaro1j', 'orgaro1i', 'orgaro2j', 'orgaro2i', 'orgalk1j', 'orgalk1i', 'orgole1j', 'orgole1i', 'orgba1j', 'orgba1i', 'orgba2j', 'orgba2i', 'orgba3j', 'orgba3i', 'orgba4j', 'orgba4i', 'orgpaj', 'orgpai', 'ecj', 'eci', 'p25j', 'p25i', 'antha', 'seas', 'soila', 'nu0', 'ac0', 'corn', 'ISEEDARR_SPPT', 'ISEEDARR_SKEBS', 'ISEEDARR_RAND_PERTURB', 'ISEEDARRAY_SPP_CONV', 'ISEEDARRAY_SPP_PBL', 'ISEEDARRAY_SPP_LSM', 'BF', 'C1H', 'C2H', 'BH', 'C1F', 'C2F', 'C3H', 'C4H', 'C3F', 'C4F', 'PCB', 'PC', 'LANDMASK', 'LAKEMASK', 'SST', 'SST_INPUT']
```

数据量相当夸张。为了减轻渣渣 1050Ti 的压力，需要从这些变量中选出主要变量。从这些变量中找出对 PM 影响较大的变量不是一件容易的事情，这里首先从数据的 `shape` 入手，将 `shape` 不是四维的数据首先排除可能，经筛选后，变量名、描述及翻译如下：


|    变量名    |                             描述                             |              翻译               |
| :----------: | :----------------------------------------------------------: | :-----------------------------: |
|      U       |                       x-wind component                       |           x 轴风分量            |
|      V       |                       y-wind component                       |           y 轴风分量            |
|      W       |                       z-wind component                       |           z 轴风分量            |
|      PH      |                  perturbation geopotential                   |          摄动位势高度           |
|     PHB      |                   base-state geopotential                    |          基态位势高度           |
|      T       |        perturbation potential temperature (theta-t0)         |            摄动势温             |
|      P       |                    perturbation pressure                     |             摄动压              |
|      PB      |                     BASE STATE PRESSURE                      |            基态压力             |
|    P_HYD     |                     hydrostatic pressure                     |            流体静压             |
|    QVAPOR    |                   Water vapor mixing ratio                   |           水汽混合比            |
|    QCLOUD    |                   Cloud water mixing ratio                   |           云水混合比            |
|    QRAIN     |                   Rain water mixing ratio                    |           雨水混合比            |
|     QICE     |                       Ice mixing ratio                       |            冰混合比             |
|    QSNOW     |                      Snow mixing ratio                       |            雪混合比             |
|    QGRAUP    |                     Graupel mixing ratio                     |           霰粒混合比            |
|     TSLB     |                       SOIL TEMPERATURE                       |            土壤温度             |
|    SMOIS     |                        SOIL MOISTURE                         |            土壤湿度             |
|     SH2O     |                      SOIL LIQUID WATER                       |           土壤液态水            |
|    SMCREL    |                    RELATIVE SOIL MOISTURE                    |          相对土壤水分           |
|    CLDFRA    |                        CLOUD FRACTION                        |             云分数              |
|     EROD     |        fraction of erodible surface in each grid cell        | 每个网格单元中可侵蚀表面的分数  |
|   CLDFRA2    |                        CLOUD FRACTION                        |             云分数              |
|   RAINPROD   |                  TOTAL RAIN PRODUCTION RATE                  |            总降雨率             |
|   EVAPPROD   |                    RAIN EVAPORATION RATE                     |           降雨蒸发率            |
|    EDUST1    |                     DUST emissions bin1                      |            粉尘排放             |
|    EDUST2    |                     DUST emissions bin2                      |            粉尘排放             |
|    EDUST3    |                     DUST emissions bin3                      |            粉尘排放             |
|    EDUST4    |                     DUST emissions bin4                      |            粉尘排放             |
|    EDUST5    |                     DUST emissions bin5                      |            粉尘排放             |
| DRY_DEP_LEN  |                   dry deposition velocity                    |           干沉降速率            |
| DRY_DEP_FLUX |                     dry deposition flux                      |           干沉降通量            |
| WET_DEP_FLUX |                  column wet scavening flux                   |          柱湿清除通量           |
|   dvel_o3    |                    O3 deposition velocity                    |         O$_3$ 沉积速度          |
|  PM2_5_DRY   |                    pm2.5 aerosol dry mass                    |     PM$_{2.5}$ 气溶胶干质量     |
|     PM10     |                        pm10 dry mass                         |        PM$_{10}$ 干质量         |
|   PHOTR201   |                     cl2 photolysis rate                      |          Cl$_2$ 光解率          |
|   PHOTR202   |                     hocl photolysis rate                     |          次氯酸光解率           |
|   PHOTR203   |                     fmcl photolysis rate                     |                -                |
|     so2      |                       SO2 mixing ratio                       |          SO$_2$ 混合比          |
|     sulf     |                      SULF mixing ratio                       |          硫酸盐混合比           |
|     no2      |                       NO2 mixing ratio                       |          NO$_2$ 混合比          |
|      no      |                       NO mixing ratio                        |            NO 混合比            |
|      o3      |                       O3 mixing ratio                        |          O$_3$ 混合比           |
|     hno3     |                      HNO3 mixing ratio                       |         HNO$_3$ 混合比          |
|     h2o2     |                      H2O2 mixing ratio                       |        H$_2$O$_2$ 混合比        |
|     ald      |                       ALD mixing ratio                       |                -                |
|     hcho     |                      HCHO mixing ratio                       |           HCHO 混合比           |
|     op1      |                       OP1 mixing ratio                       |                -                |
|     op2      |                       OP2 mixing ratio                       |                -                |
|     paa      |                       PAA mixing ratio                       |          过乙酸混合比           |
|     ora1     |                      ORA1 mixing ratio                       |                -                |
|     ora2     |                      ORA2 mixing ratio                       |                -                |
|     nh3      |                       NH3 mixing ratio                       |          NH$_3$ 混合比          |
|     n2o5     |                      N2O5 mixing ratio                       |        N$_2$O$_5$ 混合比        |
|     no3      |                       NO3 mixing ratio                       |          NO$_3$ 混合比          |
|     pan      |                       PAN mixing ratio                       |      过氧乙酰硝酸酯混合比       |
|     hc3      |                       HC3 mixing ratio                       |                -                |
|     hc5      |                       HC5 mixing ratio                       |                -                |
|     hc8      |                       HC8 mixing ratio                       |                -                |
|     eth      |                       ETH mixing ratio                       |                -                |
|      co      |                       CO mixing ratio                        |            CO 混合比            |
|     ol2      |                       OL2 mixing ratio                       |                -                |
|     olt      |                       OLT mixing ratio                       |                -                |
|     oli      |                       OLI mixing ratio                       |                -                |
|     tol      |                       TOL mixing ratio                       |                -                |
|     xyl      |                       XYL mixing ratio                       |                -                |
|     aco3     |                      ACO3 mixing ratio                       |                -                |
|     tpan     |                      TPAN mixing ratio                       |                -                |
|     hono     |                      HONO mixing ratio                       |           HONO 混合比           |
|     hno4     |                      HNO4 mixing ratio                       |                -                |
|     ket      |                       KET mixing ratio                       |                -                |
|     gly      |                       GLY mixing ratio                       |                -                |
|     mgly     |                      MGLY mixing ratio                       |                -                |
|     dcb      |                       DCB mixing ratio                       |                -                |
|     onit     |                      ONIT mixing ratio                       |                -                |
|     csl      |                       CSL mixing ratio                       |                -                |
|     iso      |                       ISO mixing ratio                       |                -                |
|     hcl      |                       HCL mixing ratio                       |                -                |
|      ho      |                       HO mixing ratio                        |                -                |
|     ho2      |                       HO2 mixing ratio                       |                -                |
|    so4aj     |                   Sulfate conc. Acc. mode                    |         硫酸盐浓度 Acc.         |
|    so4ai     |                  Sulfate conc. Aitken mode                   |        硫酸盐浓度 Aitken        |
|    nh4aj     |                   Ammonium conc. Acc. mode                   |          铵盐浓度 Acc.          |
|    nh4ai     |                 Ammonium  conc. Aitken mode                  |         铵盐浓度 Aitken         |
|    no3aj     |                   Nitrate  conc. Acc. mode                   |         硝酸盐浓度 Acc.         |
|    no3ai     |                  Nitrate  conc. Aitken mode                  |        硝酸盐浓度 Aitken        |
|     naaj     |                   Sodium   conc. Acc. mode                   |          钠盐浓度 Acc.          |
|     naai     |                  Sodium   conc. Aitken mode                  |         钠盐浓度 Aitken         |
|     claj     |                   Chloride conc. Acc. mode                   |         氯化物浓度 Acc.         |
|     clai     |                  Chloride conc. Aitken mode                  |        氯化物浓度 Aitken        |
|   orgaro1j   |        SOA Anth. org. conc. from aromatics Acc. mode         |  人为源二次有机气溶胶浓度 Acc.  |
|   orgaro1i   |       SOA Anth. org. conc. from aromatics Aitken mode        | 人为源二次有机气溶胶浓度 Aitken |
|   orgaro2j   |        SOA Anth. org. conc. from aromatics Acc. mode         |  人为源二次有机气溶胶浓度 Acc.  |
|   orgaro2i   |       SOA Anth. org. conc. from aromatics Aitken mode        | 人为源二次有机气溶胶浓度 Aitken |
|   orgalk1j   | SOA Anth. org. conc. from alkanes and others except aromatics Acc. mode |  人为源二次有机气溶胶浓度 Acc.  |
|   orgalk1i   | SOA Anth. org. conc. from alkanes and others except aromatics Aitken mode | 人为源二次有机气溶胶浓度 Aitken |
|   orgole1j   | SOA Anth. org. conc. from alkenes and others except aromatics Acc. mode |  人为源二次有机气溶胶浓度 Acc.  |
|   orgole1i   | SOA Anth. org. conc. from alkenes and others except aromatics Aitken mode | 人为源二次有机气溶胶浓度 Aitken |
|   orgba1j    |        SOA Biog. org. conc. from aromatics Acc. mode         |  生物源二次有机气溶胶浓度 Acc.  |
|   orgba1i    |       SOA Biog. org. conc. from aromatics Aitken mode        | 生物源二次有机气溶胶浓度 Aitken |
|   orgba2j    |        SOA Biog. org. conc. from aromatics Acc. mode         |  生物源二次有机气溶胶浓度 Acc.  |
|   orgba2i    |       SOA Biog. org. conc. from aromatics Aitken mode        | 生物源二次有机气溶胶浓度 Aitken |
|   orgba3j    |        SOA Biog. org. conc. from aromatics Acc. mode         |  生物源二次有机气溶胶浓度 Acc.  |
|   orgba3i    |       SOA Biog. org. conc. from aromatics Aitken mode        | 生物源二次有机气溶胶浓度 Aitken |
|   orgba4j    |        SOA Biog. org. conc. from aromatics Acc. mode         |  生物源二次有机气溶胶浓度 Acc.  |
|   orgba4i    |       SOA Biog. org. conc. from aromatics Aitken mode        | 生物源二次有机气溶胶浓度 Aitken |
|    orgpaj    |       Prim. anth. org. conc. from aromatics Acc. mode        |      人为源一次污染物 Acc.      |
|    orgpai    |      Prim. anth. org. conc. from aromatics Aitken mode       |     人为源一次污染物 Aitken     |
|     ecj      |                  Elemental carbon Acc. mode                  |           碳元素 Acc.           |
|     eci      |                 Elemental carbon Aitken mode                 |          碳元素 Aitken          |
|     p25j     |                   Primary PM2.5 Acc. mode                    |       一次PM$_{2.5}$ Acc.       |
|     p25i     |                  Primary PM2.5 Aitken mode                   |      一次PM$_{2.5}$ Aitken      |
|    antha     |                Coarse anthropogenic aerosols                 |       粗粒子模人为气溶胶        |
|     seas     |                    Coarse marine aerosols                    |       粗粒子模海洋气溶胶        |
|    soila     |                 Coarse soil-derived aerosols                 |     粗粒子模土壤衍生气溶胶      |
|     nu0      |                      Aitken mode number                      |            爱根模数             |
|     ac0      |                   Accumulation mode number                   |            积聚模数             |
|     corn     |                      Coarse mode number                      |           粗粒子模数            |

> 补充：颗粒物的三模态。
>
> 根据颗粒物的表面积和粒径分布的关系，将大气中的气溶胶粒子分为三种模结构，分别是爱根核模(Aitken nuclei mode)、积聚模(Accumulation mode)和粗粒子模(Coarse particle mode)。

### 预实验

默认实验条件如下：

|      变量名      |   值    |
| :--------------: | :-----: |
|      Model       | ConvGRU |
|  Learning Rate   | 0.0005  |
|    Batch Size    |    4    |
|   Kernel Size    |    5    |
| Number of Epochs |   200   |
| Number of Steps  |    5    |
| Number of Layers |    3    |
|   Random Iter    |  True   |
|   Weight Decay   |    0    |

#### 预实验  1

预实验 1 的目的是确定变量的选择。有两个方案可供选择：

##### 方案 1

变量为全部的非零变量中去重后的变量，总计 78 个变量，变量名如下：

```python
key = ['PM10', 'PM2_5_DRY', 'U', 'V', 'QVAPOR','CLDFRA','QRAIN','QICE','QSNOW','QGRAUP','CLDFRA','so2','sulf','no2', 'no','o3','hno3','h2o2','ald','hcho','op1','op2','paa','ora1','ora2','nh3','n2o5','no3','pan','hc3','hc5','hc8','eth','co','ol2','olt','oli','tol','xyl','aco3','tpan','hono','hno4','ket','gly','mgly','dcb','onit','csl','iso','hcl','ho','ho2','so4aj', 'so4ai', 'nh4aj', 'nh4ai', 'no3aj', 'no3ai', 'naaj','naai','claj','clai','orgaro1j', 'orgaro1i', 'orgaro2j', 'orgaro2i', 'orgalk1j', 'orgalk1i', 'orgole1j', 'orgole1i', 'orgpaj', 'orgpai', 'ecj', 'eci', 'p25j', 'p25i', 'antha']
```

实验结果如下：

<img src="https://s2.loli.net/2022/04/27/WTAg4ru7sLlK2et.jpg" style="zoom:150%;" />

##### 方案 2

 变量为上述 78 个变量中按照变量描述选择的 30 个变量：

```python
key = ['PM10', 'PM2_5_DRY', 'U', 'V', 'QVAPOR','CLDFRA','so2','no2', 'no','so4aj', 'so4ai', 'nh4aj', 'nh4ai', 'no3aj', 'no3ai', 'orgaro1j', 'orgaro1i', 'orgaro2j', 'orgaro2i', 'orgalk1j', 'orgalk1i', 'orgole1j', 'orgole1i', 'orgpaj', 'orgpai', 'ecj', 'eci', 'p25j', 'p25i', 'antha']
```

实验结果如下：

<img src="https://s2.loli.net/2022/04/27/I6gAv1T3dhJQGlr.png" style="zoom:150%;" />

{{<admonition type=Note title="结论">}}

可以看出所选的 30 个变量效果甚至优于 78 个变量。因此后续实验以 30 变量进行。猜测这是因为选择的 30 个变量中已经包含了预测 PM 所需的大部分信息。

{{</admonition>}}

#### 预实验 2

预实验 2 讨论的问题为训练当中的两个 MSE 尖峰是如何产生的，猜测原因为数据中存在时序上不连续的数据。减少数据量，选择时序上一直连续的数据，进行实验：

<img src="https://s2.loli.net/2022/04/27/wTnzW2MRsclSFGh.png" style="zoom:150%;" />

发现不存在尖峰且效果良好，因此增加去除非时序连续数据代码，恢复数据量，重新实验：

<img src="https://s2.loli.net/2022/04/28/IBdH3npuzXQb2i9.png" style="zoom:150%;" />

{{<admonition type=Note title="结论">}}

可以看出尖峰的出现的确与数据时序不连贯有关，但尖峰依旧存在，猜测这是因为在优化的过程中出现了训练集上的局部过拟合。解决方法一般为增加 Batch Size，但是由于硬件原因不能实现。此处尝试增加正则化手段减少过拟合。

{{</admonition>}}

#### 预实验 3

预实验 3 讨论的问题为 Batch Normalization 的作用。添加 BN 层，进行试验：

<img src="https://s2.loli.net/2022/04/28/ljihNxqZdesSC8F.png" style="zoom:150%;" />

{{<admonition type=Note title="结论">}}

效果提升显著并且不再出现 MSE 上升，证明了上述猜测。因此后续采用带有 Batch Normalization 的网络。并且本次实验结果作为实验的基准效果。

{{</admonition>}}

### 实验

#### 基准实验

基准实验的参数选择如下：

|     变量名      |   值    |
| :-------------: | :-----: |
|      Model      | ConvGRU |
|   Batch Size    |    4    |
|   Kernel Size   |    5    |
| Number of Steps |    5    |
|  Weight Decay   |    0    |

实验效果：

<img src="https://s2.loli.net/2022/04/29/PTCX1Fn2qLVZgtE.png" style="zoom: 150%;" />

#### 实验 1

实验 1 讨论的问题为 Weigth Decay 的影响。当 Weigth_Decay 为 1e-4 时：

<img src="https://s2.loli.net/2022/05/03/SIpM1wsb7q5xnzC.png" style="zoom: 50%;" />

当 Weight_Decay = 1e-5 时：

<img src="https://s2.loli.net/2022/05/03/TG5pydvW9FcPzeC.png" style="zoom:50%;" />

当 Weight_Decay = 1e-6  时：

<img src="https://s2.loli.net/2022/05/03/J4v5P2qWZc9yawg.png" style="zoom:50%;" />

{{<admonition type=Note title="结论">}}

可以看出 Weigth_Decay 确实对过拟合有一定程度的缓和，但总体影响不大。

 {{</admonition>}}

#### 实验 2

实验 2 主要的讨论目标为 Kernel_Size 对训练效果的影响。当降低 Kernel_Size 为 4 时：

<img src="https://s2.loli.net/2022/05/04/QUP4CSiuvMzNq3J.png" style="zoom:50%;" />

 当增加 Kernel_Size 为 7 时：

<img src="https://s2.loli.net/2022/05/04/bF2foXILUB3YRKt.png" style="zoom:50%;" />

{{<admonition type=Note title="结论">}}

可以看出，减小 Kernel_Size 后，模型泛化能力下降，增加 Kernel_Size 后，模型过拟合现象更加严重。

{{</admonition>}}

#### 实验 3

实验 3 主要讨论的影响因素为 Batch_Size 对训练效果的影响。当减小 Batch_Size 到 2 时：

<img src="https://s2.loli.net/2022/05/04/5bmD8ykLC6d4AGP.png" style="zoom:50%;" />

当增加 Batch_Size 到 6 时：

<img src="https://s2.loli.net/2022/05/04/ZsMT5oXzQ2NE9CB.png" style="zoom:50%;" />

{{<admonition type=Note title="结论">}}

可以看出，增加或减小 Batch_Size 并没有明显影响。

{{</admonition>}}

#### 实验 4

实验 4 的目的为探究 Num_Steps 对训练效果的影响。当减小 Num_Steps 到 3 时：

<img src="https://s2.loli.net/2022/05/04/RzSpUxBjYCrP7u6.png" style="zoom:50%;" />

当增加 Num_Steps 到 7 时：

<img src="https://s2.loli.net/2022/05/04/FSXyb4tHRqsf1xj.png" style="zoom:50%;" />

{{<admonition type=Note title="结论">}}

可以看出，增加 Num_Steps 加剧了过拟合现象，减小 Num_Steps 降低了模型的泛化能力。

{{</admonition>}}

#### 实验 5

 实验 5 的目的为探究 ConvLSTM 的效果。当将模型切换为 ConvLSTM 后：

<img src="https://s2.loli.net/2022/05/04/nrMiCJ1tZ9s3lHG.png" style="zoom:50%;" />

{{<admonition type=Note title="结论">}}

可以看出模型泛化能力下降。

{{</admonition>}}

### 总结

在上述的实验中，可以得出：

1. **Batch Size** 对网络训练效果影响不大；
2. **Weigth Decay** 的引入可以提升网络的泛化能力；
3.  **Kernel Size**、**Num Steps** 的增加会加剧网络过拟合，其减小又会引发泛化能力的下降；
4. 相较于 **ConvLSTM**，**ConvGRU** 具有更好的泛化能力。

{{<admonition type=question title="问题">}}

事实上，上述实验结果的导出十分具有局限性。因为最后实验数据仅仅整理出 166 条训练集数据及 55 条测试集数据。在深度学习领域，这样的数据量是极小的。并且值得注意的是，训练集数据并没有分布在测试数据所在的时间段上，这也是训练集效果良好，测试集效果差距较大的原因之一。

{{</admonition>}}

## 绘图

---

<image src="/images/Res_Tiny.gif" style="zoom:150%;">

## 工作时间线

---

2022/04/21及之前：完成了模型训练、预测的代码。

2022/04/22：从实验室区筛选了带有 PM 的 WRF 数据，大概有550条。

2022/04/23：确认数据，筛选合适的数据。最终数据分布在 3 个时间段上，共计 221 条。

2022/04/25：训练一部分数据，训练集 MSELoss 为 12.6。

2022/05/01：实验完成。

2022/05/08：画个图。



---

> 作者: Blathers  
> https://blathers23.vercel.app/%E6%9D%82%E8%B0%88%E6%AF%95%E4%B8%9A%E8%AE%BE%E8%AE%A1/
