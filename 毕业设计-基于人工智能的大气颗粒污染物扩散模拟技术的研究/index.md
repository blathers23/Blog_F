# 毕业设计-基于人工智能的大气颗粒污染物扩散模拟技术的研究

<!--more-->

{{<admonition note>}}

论文初稿，主要阐述了研究方向、技术路线及研究结果。

{{</admonition>}}

## 摘要

本研究以辽宁省及其周边为研究区域，引入人工智能模型，通过捕获大气颗粒物污染的历史时空间浓度分布、地理环境和大气信息，用以预测未来的大气颗粒物空间分布情况，并将预测结果与基于WRF-Chem模型的预测结果进行比较，试图构建更准确的模型，做到更为快速及时的大气颗粒污染物预测响应。

## 绪论

良好生态环境是实现中华民族永续发展的内在要求，是增进民生福祉的优先领域，是建设美丽中国的重要基础。随着我国可持续发展理念认知的不断深入，在大力开展经济建设的同时，生态文明建设也日益加强。环境污染的防治成为国家发展建设中的一项重要工作，其中的大气污染防治更是重中之重。2021年11月，国务院发布了关于深入打好污染防治攻坚战的意见，其中明确提到，要深入打好蓝天保卫战，为我国的大气污染防治工作提出了更高的要求[^1]。

在众多大气环境污染问题之中，以颗粒物（$PM_{2.5}$ 、$PM_{10}$）污染是现阶段我国面临的最主要大气环境污染问题。《2020年中国生态环境状况公报》[^2]显示，全国337个地级及以上城市中，以$PM_{2.5}$为首要污染物的超标天数（空气质量指数（AQI）大于100的天数）占到了总超标天数的51.0%。大气颗粒污染物对人体健康、大气能见度及气候变化都有着重要的影响，已经受到了公众的广泛关注，并且成为了近年来学术界的研究热点。相关研究表明，$PM_{2.5}$与死亡率以及心肺疾病住院率密切相关[^3]；以$PM_{2.5}$为主的户外空气污染，导致全世界每年330万（95%的置信区间）人的过早死亡[^4]。为确保我国经济的可持续发展，坚决打赢蓝天保卫战，大气颗粒物污染的治理是重中之重。

为了减少大气颗粒物污染对生态环境、人类健康和社会经济带来的危害，制定合理有效的污染防治策略，有必要把握空气中颗粒物形成和扩散的动态过程，并对颗粒物浓度空间分布做出相对准确的预测。空气质量预报模型可以预估大气污染变化趋势，同时对大气污染过程、污染物来源解析以及区域输送过程进行及时分析，从而提高城市预报预警能力以及污染物溯源能力。短期的空气质量预测可以协助公众及时响应高污染事件，做好相应的防护措施[^5]；长期的空气质量预测则可以为相关部门大气污染联防联控提供准确及时的大气污染信息，提高污染管控能力，减少社会经济损失[^6]。了解大气污染过程以及大气污染状况，建立准确的空气质量预报模型，可以向大气颗粒物污染治理提供更加合理的决策支持。

空气质量模型，按照机理划分，可以分为数值模型和统计模型两种。空气质量数值模型通过对污染物在空气中发生的物理化学过程（如传输、扩散、化学反应等）进行数学抽象而建立[^7]，根据大气的实际情况，在一定的初值和边界条件下对大气污染的物理化学过程进行仿真，从而预测未来一段时间内大气污染物的浓度分布情况。常用的空气质量模型如：CALPUFF[^8]、WRF-Chem[^9]、CMAQ[^10]等模型都属于数值模型。数值模型可解释性强，但是存在3个主要问题：（1）大气边界层模拟引起的不确定性；（2）大气污染物排放源引起的不确定性；（3）大气物理化学过程引起的不确定性。这3个主要问题大大限制了空气质量模型的模拟精度[^11]。

统计模型以现有的大量数据为基础，通过对数据进行统计分析，建立空气质量模型[^7]。以$PM_{10}$预测统计模型为为例，常用的建模算法有：人工神经网络（Artificial Neural Networks， ANN）、多变量线性回归（Multi-variate Linear Regression，MLR）、多层感知机（Multi Layer Perceptron，MLP）、支持向量机（Support Vector Machines，SVM）等[^12]，这些建模算法都隶属于机器学习（Machine Learning，ML）的范畴。机器学习是一类强大的可以从经验中学习的技术，通常采用观测数据或与环境交互的方式，机器学习算法会积累更多的经验，其性能也会逐步提高[^13]。在数值模型的3个主要问题中，问题（1）和问题（3）主要由当前对大气物理化学过程理解不充分而导致的建模不准确引起[^11]。统计模型的建立依赖于观测得到的数据，而并不依赖于大气物理化学理论的进展，因此统计模型可以有效地弥补数值模型的缺陷。

深度学习（Deep Learning，DL）是机器学习的一个分支，通过层次化，使计算机构建较为简单的概念，并将简单概念组合来学习复杂概念。自深度学习这个术语在2006年被正式提出后，在最近10多年得到了巨大发展，它使人工智能（Artificial Intelligence，AI）产生了革命性的突破[^14]，也大大推进了空气质量统计模型的发展，相关工作层出不穷。2015年，尹文君等将深度置信网络[^15]（Deep Belief Network，DBN）运用到了空气质量预测上，将过去一天空气污染情况、过去一天和预报的污染物扩散条件作为神经网络的输入来预测预报污染物的浓度，相较经典的机器学习预报模型，DBN模型性能更好[^16]。其弊端也显而易见：对于统计模型来说，空气质量预测问题作为一种典型的时空序列预测问题，强烈依赖于历史数据所提供时间和空间上的信息，而该模型不仅没有考虑空间内大气物理化学因素的影响，在时间上也只能处理过去一天的平均污染物浓度而忽略了时间序列上的其它信息。2017年，Xiang Li等人利用长短期记忆[^17]（Long Short-Term Memory，LSTM）神经网络，提出了一种基于历史大气污染浓度和气象数据建立的$PM_{2.5}$浓度预测模型[^18]。该模型能对具有长时间依赖性的时间序列进行建模，充分考虑了时间序列上的信息，但空间内信息依旧考虑不足。2021年，黄伟建等提出了时间序列记忆网络（Time Series Memory Network，TSMN）模型。该模型使用门控循环单元[^19]（Gated Recurrent Unit，GRU）和卷积神经网络[^20]（Convolutional Neural Network，CNN），构建了处理时间信息的本地记忆组件和处理空间信息的邻域组件，并结合通过全连接层（Fully Connected Layer，FC）整合两组件提取到的时空特征，获得目标站点未来24小时的$PM_{2.5}$ 浓度预测值。尽管TSMN模型整合了时间信息和空间信息，但该模型仅能给出目标站点处$PM_{2.5}$的预测浓度，与可以给出污染物空间浓度分布预测的WRF-Chem、CMAQ等数值模型相比仍有差距。

在深度学习领域，将预测给定视频帧的未来帧的任务称为视频预测[^21]（Video Prediction）任务，早在2015年，视频预测这个概念就已经被提出[^22]。通过将雷达图视为视频帧，Xingjian Shi等提出了卷积长短期记忆（Convolutional Long Short-Term Memory，Conv-LSTM）模型用于预测降水强度[^23]。Conv-LSTM模型兼顾了CNN在提取空间信息上的优势和LSTM在提取时间信息上的优势，以过去数帧雷达回波图作为输入，预测未来的雷达回波图，从而推断出空间范围内的降水强度。2020年，张迪等通过运行WRF气象模型和CMAQ空气质量数值模型得到的中国地区细颗粒物空间浓度分布数据作为Conv-LSTM模型的训练数据，构建了基于深度学习的大气细颗粒物时空预报模型[^24]。与其它基于深度学习的空气质量预报模型相比，Conv-LSTM模型可以给出污染物浓度的空间分布，而与CMAQ等数值模型相比，Conv-LSTM模型又有着预测速度快，资源消耗少的优点。近年来深度学习发展迅速，距Conv-LSTM提出至今7年以来，针对视频预测任务的深度学习模型不断更新，预测精度与预测速度等多方面都有所显著改善[^25]。将更有效的模型运用到大气颗粒物污染浓度预测模型的构建上势在必得。

本文综合考虑我国大气污染问题和空气质量模型研究领域现状，有针对性地展开研究。本研究以辽宁省及其周边区域为研究区域，以大气颗粒物为研究对象，引入深度学习模型，使用数值模型WRF-Chem的输出数据作为训练数据，尝试使模型捕获模型内部的大气物理化学过程模式来构建基于人工智能的大气颗粒污染物扩散模拟模型。

## 研究方法

### 数据准备

数据于空气质量模型是非常重要的。对于数值模型而言，原始数据作为数值模拟的初始和边界条件，允许模型计算求解通过数学抽象污染物输送、扩散、化学转化等大气过程得到的方程组来预测大气污染物的浓度分布[^7]。对于统计模型来说，建立模型本身就依赖于对原始数据的统计与分析，以统计模型中广泛应用的机器学习算法为例，机器学习算法通过从原始数据中提取模式来不断提高自身的性能[^14]，因此原始数据的质量与模型最终的性能密切相关。同时，深度学习算法对数据量也有着较高的需求，统计估计的一大问题就是泛化（generalization）问题，泛化指的是训练好的模型在前所未见的数据上性能的好坏，而增加数据量可以有效提高模型的泛化能力。因此，为构建基于深度学习的大气颗粒物污染浓度预测模型，大量且准确的数据是必要的。然而，真实环境下的历史数据难以取得，主要原因是真实环境下的大气污染物浓度数据多为监测站点处浓度，是时间维度上的一维变量，而为了对大气颗粒物空间浓度分布进行统计预测，需要时间维度和空间维度组合的三维变量。为此，本研究选择WRF-Chem空气质量模型输出的WRFOut数据作为深度学习模型的训练数据。

本研究采用的WRFOut数据共三段，分别是2018年1月25日00:00到2018年1月27日07:00的逐小时数据55条（数据段1）、2019年1月10日00:00到2019年1月13日15:00的逐小时数据87条（数据段2）及2019年2月26日00:00到2019年3月1日08:00的逐小时数据79条（数据段3）。这三段数据的模拟范围均采用兰伯特投影坐标系，中心经纬线为北纬41.75°和东经123.53°，即以沈阳市为中心。模拟网格分辨率为27千米$\times$27千米，网格数量为90$\times$90共8100格。模拟范围见下图。

![](https://s2.loli.net/2022/05/25/CIWQ9kywvPXKoJS.jpg "模拟范围")

WRFOut数据以网络通用数据格式[^26]（Network Common Data Form，NetCDF）存储，使用Python编程语言下的netcdf4-python[^27]库读取WRFOut数据。NetCDF格式的数据以变量（variable）为单位进行保存，每个变量都有变量描述、变量单位、变量形状、变量值等多个属性。对于WRF-Chem模拟导出的WRFOut数据，其中包含了时间（Times）、经度（XLONG）、维度（XLAT）等时空间位置描述性变量；X轴风速分量（U），雨水混合率（QRAIN）等气象数据；土壤温度（TSLB）、土壤液态水（SH2O）等地理数据；$SO_{2}$混合比（so2）、PAN混合比（pan）等污染物浓度数据以及积聚核模人为源芳香烃二次有机气溶胶浓度（orgaro1j）等污染物排放数据共计281个变量。若将变量全部用于神经网络的训练，则模型对内存的要求将大大超出可接受范围，为此，需要从变量中筛选出对大气颗粒物影响较大的变量以减少硬件压力。以变量形状、变量值以及变量描述为依据，最终选出30个变量作为神经网络训练的输入变量，详见下表。

| 变量关键字 |                    变量描述                     | 变量单位    |
| :----------: | :---------------------------------------------: | :-----------: |
| PM10       |                 $PM_{10}$干质量                 | $ug/m^{3}$  |
| PM2_5_DRY  |             $PM_{2.5}$气溶胶干质量              | $ug/m^{3}$  |
| U          |                   X轴风速分量                   | m/s         |
| V          |                   Y轴风速分量                   | m/s         |
| QVAPOR     |                   水汽混合比                    | kg/kg       |
| CLDFRA     |                      云量                       | %           |
| so2        |                 $SO_{2}$混合比                  | ppmv        |
| no2        |                 $NO_{2}$混合比                  | ppmv        |
| no         |                    NO混合比                     | ppmv        |
| so4aj      |               积聚核膜硫酸盐浓度                | ug/kg干空气 |
| so4ai      |               爱根核模硫酸盐浓度                | ug/kg干空气 |
| nh4aj      |                积聚核膜铵盐浓度                 | ug/kg干空气 |
| nh4ai      |                爱根核模铵盐浓度                 | ug/kg干空气 |
| no3aj      |               积聚核膜硝酸盐浓度                | ug/kg干空气 |
| no3ai      |               爱根核模硝酸盐浓度                | ug/kg干空气 |
| orgaro1j   |     积聚核模人为源芳香烃二次有机气溶胶浓度*     | ug/kg干空气 |
| orgaro1i   |     爱根核模人为源芳香烃二次有机气溶胶浓度*     | ug/kg干空气 |
| orgaro2j   |     积聚核模人为源芳香烃二次有机气溶胶浓度*     | ug/kg干空气 |
| orgaro2i   |     爱根核模人为源芳香烃二次有机气溶胶浓度*     | ug/kg干空气 |
| orgalk1j   | 积聚核模人为源除芳香烃外烷烃二次有机气溶胶浓度* | ug/kg干空气 |
| orgalk1i   | 爱根核模人为源除芳香烃外烷烃二次有机气溶胶浓度* | ug/kg干空气 |
| orgole1j   | 积聚核模人为源除芳香烃外烷烃二次有机气溶胶浓度* | ug/kg干空气 |
| orgole1i   | 爱根核模人为源除芳香烃外烷烃二次有机气溶胶浓度* | ug/kg干空气 |
| orgpaj     |     积聚核模人为源芳香烃一次有机气溶胶浓度      | ug/kg干空气 |
| orgpai     |     爱根核模人为源芳香烃一次有机气溶胶浓度      | ug/kg干空气 |
| ecj        |                 积聚核膜元素碳                  | ug/kg干空气 |
| eci        |                 爱根核模元素碳                  | ug/kg干空气 |
| p25j       |             积聚核膜一次$PM_{2.5}$              | ug/kg干空气 |
| p25i       |             爱根核模一次$PM_{2.5}$              | ug/kg干空气 |
| antha      |                 人为源粗气溶胶                  | ug/kg干空气 |

> *虽然描述相同，但并不是重复的数据。这是因为WRF-Chem运行时采用的排放清单对不同来源的人为源做了区分（如交通排放、生产排放等），但未对变量描述做对应的区分，仅修改了变量关键字。

为了简化操作，需要将NetCDF格式的数据重新打包为npy格式。NumPy[^28]（Numerical Python）是Python常用的矩阵运算拓展库，而npy格式是NumPy专用的二进制格式。与NetCDF格式相比，npy格式的文件以矩阵为单位组织数据，仅保存变量值而忽略了变量描述、变量单位等其他信息，大大降低了变量值操作复杂度，便于神经网络的训练。经NumPy重新组织的变量值为一个4维矩阵数据，4个维度分别为：时间、变量、X轴、Y轴。在神经网络训练前，需要将数据划分为训练集和和测试集，一般训练集和测试集数据量比为8:2，但是为了保证数据时间上的连续性，本研究将数据量较多的数据段2和数据段3划分为训练集，并以npy格式保存。训练集矩阵形状为（166，30，90，90），其中166代表数据段2和数据段3共166条逐小时数据，30代表经筛选的神经网络输入变量共30个，（90，90）代表WRF-Chem模拟网格数量为90$\times$90。同时将数据量较少的数据段1划分为测试集并以npy格式保存，测试集矩阵形状为（55，30，90，90）。至此，神经网络模型训练数据准备阶段完毕。

### 模型构建

构造大气颗粒物浓度预测模型的目标是：给定先前观测到气象、污染物排放等数据的时间序列，来预测下一个时间点处大气颗粒物浓度的空间分布。以机器学习的观点来看，这个问题可以归结为时空间序列预测问题。假设某动态系统在一个以$M \times N$网格表示的空间区域内被观测，在每个网格内，动态系统的状态都可以通过$L$个变量来描述，那么任何时刻的该系统整体的状态都可以通过一个矩阵$\mathcal{X} \in R^{L \times M \times N}$来表示，其中$R$代表被观测特征的范围.每次对该动态系统进行观测，都可以得到观测时刻$t$的系统状态$\mathcal{X_{t}}$。时空序列预测问题就是根据包含当前时刻在内的$K$个先前的观测得到的系统状态$\hat{\mathcal{X_{t-K+1}}},\hat{\mathcal{X_{t-K+2}}},...,\hat{\mathcal{X_{t}}}$，来预测未来最可能的系统状态$\tilde{\mathcal{X_{t+1}}}$，即：
$$
\tilde{\mathcal{X_{t+1}}} = \mathop{\arg\max}\limits_{\mathcal{X_{t+1}} \in R^{L \times M \times N} } \ {P(\mathcal{X_{t+1}} \mathcal{|} \hat{\mathcal{X_{t-K+1}}},\hat{\mathcal{X_{t-K+2}}},...,\hat{\mathcal{X_{t}}})}
$$

对于时空间序列预测问题而言，为解决这一类问题而构建的神经网络模型可以通过函数$y=f(x;\theta)$表示。其中$f$表示神经网络的架构、$\theta$表示神经网络的参数、$x$，$y$分别表示网络的输入和输出，即：
$$
\tilde{\mathcal{X_{t+1}}}=f(\hat{\mathcal{X_{t-K+1}}},\hat{\mathcal{X_{t-K+2}}},...,\hat{\mathcal{X_{t}}};\theta)
$$
当模型架构$f$给定后，神经网络通过$\theta$定义了条件分布$\tilde{P}(y|x ; \theta)$。神经网络训练，或称模型优化，就是指通过调整$\theta$使得模型定义的条件概率分布$\tilde{P}$与尽可能逼近真实环境下的条件概率分布$\hat{P}$的过程。为了达到这一目的，一般需要定义一个代价函数$L(\tilde{y},\hat{y};\theta)$，通过计算得到的代价值来衡量当前神经网络预测的结果$\tilde{y}$与观测结果$\hat{y}$之间的差距，并寻找一个最小化所有观测得到$n$个样本上总损失的参数$\theta^{\ast}$，这个过程可以以表示为：
$$
\theta^{\ast} = \mathop{\arg\min}\limits_{\theta} \ {\frac{1}{n} \sum^{n}_{i=0} L(\tilde{y}^{(i)},\hat{y}^{(i)};\theta)}
$$
综上所述，为构造基于深度学习的大气颗粒物浓度预测模型，需要讨论模型架构和模型优化两个问题。

#### 模型架构

架构（Architecture）一词指的是神经网络整体的结构，即网络应该由多少个单元组成，以及这些单元之间的连接方式。讨论模型架构问题，就是要找到适合当前任务所需要的架构。

大多数神经网络被组织成称为层的单元组。大多数神经网络架构将这些层布置成链式结构，其中每一层都是前一层的函数。在这种结构中，第一层由下式给出：
$$
h^{(1)} = \sigma^{(1)}(f^{(1)}(x;\theta^{(1)}))
$$
第二层由
$$
h^{(2)}=\sigma^{(2)}(f^{(2)}(h^{(1)};\theta^{(2)}))
$$
给出，以此类推。其中$f^{(1)}$和$f^{(2)}$代表某种运算，如矩阵乘法、卷积（Convolution）运算等；$\sigma^{(1)}$和$\sigma^{(2)}$被称为激活函数（Activation Function），用于将非线性特性引入到神经网络中，使神经网络拥有学习非线性函数的能力。在这种链式架构中，架构主要考虑的是网络的深度和每一层的宽度。网络的深度一般指链式架构的长度，每一层的宽度则代表了该层参数的数量，宽度越宽，则参数量越大。一般而言，增加参数量可以提高网络的表示能力，增加网络的深度可以使网络更容易泛化到测试集。对于一个具体的任务，理想的网络架构必须通过实验，观测在验证集上的误差来找到。

##### 卷积神经网络

卷积神经网络通过卷积运算来模拟人类视觉系统来提取空间上的信息，是一种非常适合于处理具有类似网格结构的数据的神经网络。一般将至少使用一层卷积运算的网络称为卷积神经网络，而使用卷积运算的层则称为卷积层（Convolution Layer）。以2维卷积层为例，卷积运算以一个2维矩阵$I$作为输入（Input），以另一个2维矩阵$S$作为输出（Output）。卷积运算通常以$\ast$表示，定义如下：
$$
S(i,j)=(I \ast K)(i,j)=\sum_{m=0}^{k_m} \sum_{n=0}^{k_n} I(i+m,j+n)K(m,n)
$$
其中，$K$被称为卷积核（Convolution Kernel），是需要通过模型优化进行学习的矩阵参数。$k_m$和$k_n$定义了2维矩阵$K$的形状，是一种称为卷积核大小（Kernel Size）的超参数（Hyperparameter）。超参数指的是在机器学习中，在模型优化前需要人为指定的参数。

2维矩阵卷积计算过程如下图所示：

![](https://s2.loli.net/2022/05/25/zKmkeEou7InfBrM.png "二维卷积计算过程")

该图表示了当2维矩阵$I$的大小为3$\times$3，$k_m$和$k_n$均为2时，卷积运算的步骤与结果。可以注意到，在卷积层中，参数的数量仅仅与卷积核大小，即$k_m$和$k_n$的值有关。采用较小的卷积核，会导致神经网络参数量下降，难以学习复杂的表示，而采用较大的卷积核，则会增加计算量，增加硬件负担。因此，采用卷积神经网络架构时，卷积核大小是一个非常重要的参数。

为了在时空间序列预测问题上使用卷积架构，需要考虑输入矩阵和输出矩阵大小的问题。注意到在经过2$\times$2卷积核$K$的卷积计算后，输入2维矩阵的大小从3$\times$3降为了输出2维矩阵的大小2$\times$2，即，卷积计算会减少矩阵的大小，但是在时空间序列预测问题上，期望得到的预测$\mathcal{X}$的大小应当与输入矩阵的大小相同。为了解决这个问题，这里引入填充（Padding）操作。填充操作通过在输入矩阵的周围填充数据，可以使输入矩阵的大小和输出矩阵的大小保持一致，一般选择填入的数据为0。以大小为3$\times$3的输入矩阵为例，在输入矩阵四周分别填充一行0后，输入矩阵的大小变为5$\times$5，经2$\times$2的卷积核卷积计算后，输出矩阵的大小为3$\times$3，保证了卷积前后输入输出矩阵的大小一致。

另外一个需要考虑的问题是输入输出维度的问题。时空间序列预测模型的期望输入和输出矩阵为3维矩阵，需要对2维卷积运算进行扩展：
$$
S^{(p)}(i,j)=(I \ast K)(i,j)=\sum_{l=0}^{L} \sum_{m=0}^{k_m} \sum_{n=0}^{k_n} I(l,i+m,j+n)K^{(p)}(l,m,n)
$$
由于输入矩阵$I$变为3维矩阵，因此卷积核$K$也相应的拓展为3维矩阵，矩阵大小为$L \times k_m \times k_n$，即在变量维度上与输入矩阵的维度大小保持一致。注意到，对于某一个卷积核$K^{(p)}$，经卷积运算后，得到的输出矩阵$S^{(p)}$依旧为2维矩阵。为了便于多个卷积层之间的链式连接，一般使用多个卷积核对同一个输入矩阵$I$进行卷积运算，并将多个2维矩阵堆叠在一起作为输出，确保输出矩阵$S$同样为3维矩阵。

##### 循环神经网络

对于时空间序列预测问题，所有观测到的系统状态$\mathcal{X}$之间不是相互独立（Independently）的，这是因为未来的系统状态明显是受到过去系统状态的影响。虽然卷积神经网络解决了如何提取空间信息的问题，但依旧难以将时间上的信息纳入考量。循环神经网络[^29]（Recurrent Neural Network，RNN）是一类用于处理序列数据的神经网络。通过引入状态变量，RNN得以保存过去的信息，同时使用过去的信息与当前的输入来确定当前的输出，有效达成了融合时间信息的目的。

一般的循环神经网络包含一个称为隐状态（Hidden State）的状态变量，以$h$表示。某时刻$t$的隐状态$h_{t}$由下式求得：
$$
h_{t} = g(h_{t-1},x_{t};\theta_{g})
$$
其中，$g$和$\theta_{g}$分别表示某种网络架构及其参数。为了得到当前时刻的输出，还需要经过下式计算：
$$
y_{t} = f(h_{t};\theta_{f})
$$
其中，$f$和$\theta_{f}$同样表示某种网络架构及其参数。因此，循环神经网络的计算过程可以由下图表示：

![](https://s2.loli.net/2022/05/25/qUsdSOr196CJfch.png "循环神经网络计算过程")

虽然在理论上，循环神经网络引入隐状态的设计可以捕获过去的信息用于与当前输入进行融合以获得当前输出，但事实上，正是因为这种设计，导致了RNN的梯度消失（Vanishing Gradient）问题[^30]，简单来说，梯度消失会导致神经网络难以被优化/解决这一问题最早的方法之一是1997年Hochreiter等人提出的长短期记忆模型。与一般的循环神经网络相比，除了隐状态外LSTM又额外引入了记忆元（Memory Cell），或称单元（Cell）作为状态变量用于记录附加的信息。为了控制记忆元，需要3个称为门（Gate）的组件，这三个门分别是：用于决定何时将数据读入单元的输入门（Input Gate）、从单元中输出数据的输出门（Output Gate）以及用来重置单元的遗忘门（Forget Gate）来管理。

以$t$时刻某一维向量$X_{t} \in R^{1 \times d}$和前一时刻的隐状态$H_{t-1} \in R^{1 \times h}$作为输入为例，对于长短期记忆单元的计算过程，首先需要计算输入门、输出门和遗忘门，计算过程如下：
$$
I_{t} = \sigma(X_{t}W_{xi}+H_{t-1}W_{hi}+b_{i})
$$
$$
F_{t} = \sigma(X_{t}W_{xf}+H_{t-1}W_{hf}+b_{f})
$$
$$
O_{t} = \sigma(X_{t}W_{xo}+H_{t-1}W_{ho}+b_{o})
$$
式中，$I_{t}$，$F_{t}$，$O_{t} \in R^{1 \times h}$分别为输入门、遗忘门和输出门，$W_{xi}$，$W_{xf}$，$W_{xo} \in R^{d \times h}$和$W_{hi}$，$W_{hf}$，$W_{ho} \in R^{h \times h}$称为权重参数，$b_{i}$，$b_f$，$b_{o} \in R^{1 \times h}$称为偏置参数。权重参数和偏置参数均为模型优化时需要学习的参数，一维向量$X$的长度$d$则是由所研究问题自身决定的，而隐状态$H$的长度$h$，则是被称为隐状态维度（Hidden Dimension）的超参数，与卷积核大小一样需要在模型优化之前人为指定。可以看出，$h$越大，模型参数量就越多，则模型越宽，意味着模型表达能力的上升，同时也会增加硬件的压力。$\sigma$是激活函数，此处选择Sigmoid函数。Sigmoid函数由下列公式定义：
$$
Sigmoid(x)=\frac{1}{1+e^{-x}}
$$
Sigmoid函数可以将一个实数映射到$(0,1)$的区间范围内，因此这三个门的输出值都在$(0,1)$之间。

接下来需要计算候选记忆（Candidate Memory）$\tilde{C_{t}} \in R^{1 \times h}$。候选记忆的计算过程如下：
$$
\tilde{C_{t}}=Tanh(X_{t}W_{xc}+H_{t-1}W_{hc}+b_{c})
$$
式中，$W_{xc} \in R^{d \times h}$，$W_{hc} \in R^{h \times h}$和$b_{c}$均为模型优化时需要学习的参数。与门计算不同的是，候选记忆的激活函数为双曲正切，简称tanh函数。Tanh函数由下列公式定义：
$$
Tanh(x) = \frac{e^{x} - e^{-x}}{e^{x}+e^{-x}}
$$
双曲正切函数可以将一个实数映射到$(-1,1)$的区间范围内，因此候选记忆元的值在$(-1,1)$之间。LSTM中输入门、输出门、遗忘门和候选记忆元的计算过程如下图所示：

![](https://s2.loli.net/2022/05/25/a7OiKMhjNTJ6xoS.png "LSTM计算过程第一步")

在LSTM中，输入门用于控制记忆元$C_{t} \in R^{1 \times h}$中有多少数据来自于候选记忆$\tilde{C_{t}}$中的新数据，而遗忘门用于控制上个时刻的记忆元$C_{t-1}$中的数据有多少会被丢弃。因此记忆元由下列公式给出：
$$
C_{t} = F_{t} \odot C_{t-1} + I_{t} \odot \tilde{C_{t}}
$$
式中的$\odot$表示哈达玛积（Hadamard Product），即矩阵对应位置相乘。记忆元的计算过程如下图所示：

![](https://s2.loli.net/2022/05/25/EnOjX718r3McvLH.png "LTSM计算过程第二步")

最后，LSTM会读取当前记忆元$C_{t}$，并通过输出门来计算当前隐状态$H_{t}$。隐状态的计算由下式给出：
$$
H_{t} = O_{t} \odot Tanh(C_{t})
$$
在计算$H_{t}$前，先将$C_{t}$送入双曲正切函数，确保$H_{t}$的值始终在区间$(-1,1)$之间。计算过程如下：

![](https://s2.loli.net/2022/05/25/UZV6dATFgs7Ln9K.png "LSTM计算过程第三步")

长短期记忆模型通过引入记忆元以及门的概念，使模型能够控制隐状态的更新，有效解决了RNN模型优化中的梯度消失问题。在LSTM提出的门控思想启发下，2014年，Kyunghyun Cho等人提出了门控循环单元[^31]（Gated Recurrent Unit，GRU）。与LSTM相比，GRU提供了相同的效果，并且计算速度明显更快[^33]。

与 LSTM相似，GRU引入了重置门（Reset Gate）以及更新门（Update Gate）来控制隐状态的更新。以$t$时刻某一维向量$X_{t}$和前一时刻的隐状态$H_{t-1}$作为输入为例，重置门及更新门计算的数学表达式如下：
$$
R_{t} = Sigmoid(X_{t}W_{xr}+H_{t-1}W_{hr}+b_{r})
$$
$$
U_{t} = Sigmoid(X_{t}W_{xu}+H_{t-1}W_{hu}+b_{u})
$$
式中，$R_{t}$和$U_{t}$分别为重置门和更新门，$W_{xr}$、$W_{xu}$、$W_{hr}$，$W_{hu}$、$b_{r}$、$b_{u}$均为模型优化时需要学习的参数。与LSTM不同的是，GRU没有引入记忆元，而是引入了候选隐状态（Candidate Hidden State）的概念来取代记忆元的作用。$t$时刻候选隐状态$\tilde{H_{t}}$的计算如下：
$$
\tilde{H_t} = Tanh(X_{t}W_{xh}+(R_{t} \odot H_{t-1})W_{hh}+b_{h})
$$
候选隐状态通过重置门来控制以往隐状态对当前隐状态的影响。在计算得到候选隐状态后，就可以通过更新门计算当前隐状态$H_{t}$。计算的数学表达式如下：
$$
H_{t} = U_{t} \odot H_{t-1} + (1 - U_{t}) \odot \tilde{H_{t}}
$$
当更新门接近于1时，隐状态倾向于保留过去的状态，而当更新门接近于0时，隐状态倾向于接受当前时刻的信息。GRU计算流程图如下所示：

![](https://s2.loli.net/2022/05/25/kyRSjGn42eu1Zq9.png "GRU计算过程")

可以看出，与LSTM相比，GRU大大简化了计算的流程，在解决梯度消失问题的同时提高了运算速度，减少了内存占用。

##### Conv-GRU模型

卷积神经网络和循环神经网络各有所长，卷积神经网络适合于处理网格数据，而循环神经网络适合处理时间序列数据，但是对于需要处理时空间序列的时空间序列预测问题，两种架构都不能直接使用。将卷积神经网络和长短期记忆网络融合在一起的卷积长短期记忆网络就是针对处理时空间序列数据而设计的神经网络架构。简单来说，就是将长短期记忆网络中的矩阵乘法运算替换为卷积运算，使得长短期记忆神经网络可以处理网格数据。Conv-LSTM的数学表达式如下：

$$
I_{t} = \sigma(X_{t} \ast W_{xi}+H_{t-1} \ast W_{hi}+b_{i})
$$
$$
F_{t} = \sigma(X_{t} \ast W_{xf}+H_{t-1} \ast W_{hf}+b_{f})
$$
$$
O_{t} = \sigma(X_{t} \ast W_{xo}+H_{t-1} \ast W_{ho}+b_{o})
$$
$$
\tilde{C_{t}}=Tanh(X_{t} \ast W_{xc}+H_{t-1} \ast W_{hc}+b_{c})
$$
$$
H_{t} = O_{t} \odot Tanh(C_{t})
$$

2017年，Conv-LSTM的作者Xingjian Shi等人提出了基于GRU的改进，即卷积门控循环单元[^32]（Convolutional Gated Recurrent Unit，Conv-GRU）。Conv-GRU的数学表达式如下：
$$
R_{t} = Sigmoid(X_{t} \ast W_{xr}+H_{t-1} \ast W_{hr}+b_{r})
$$
$$
U_{t} = Sigmoid(X_{t} \ast W_{xu}+H_{t-1} \ast W_{hu}+b_{u})
$$
$$
\tilde{H_t} = Tanh(X_{t} \ast W_{xh}+(R_{t} \odot H_{t-1}) \ast W_{hh}+b_{h})
$$
$$
H_{t} = U_{t} \odot H_{t-1} + (1 - U_{t}) \odot \tilde{H_{t}}
$$
Conv-GRU继承了GRU的优点，相较于Conv-LSTM，Conv-GRU运算速度更快，内存占用更小。

综上所述，本研究主要采用Conv-GRU建立大气颗粒物污染扩散模拟模型。为了提高模型的表达能力，便于在数据中学习到更为复杂的非线性关系，本研究将相同的Conv-GRU块堆叠3次，作为模型的主干。每次重叠意味着上一个Conv-GRU块的输出作为下一个Conv-GRU块的输入。在最后一个Conv-GRU块给出当前的隐状态$H_{t}$后，需要经过解码器（Decoder）计算才能得到当前的输出$Y_{t}$，即对下一时刻系统状态$\tilde{\mathcal{X_{t+1}}}$的预测。解码器本身相当于一个卷积层，其计算过程如下：首先将$t$时刻系统状态$\mathcal{X_{t}} \in R^{L \times M \times N}$和隐状态$H_{t} \in R^{h \times M \times N}$在第一个维度上进行拼接得到解码器的输入$D_{t} \in R^{(L+h) \times M \times N}$，然后将$D_{t}$与卷积核进行卷积运算，得到最终的输出。本研究构建的模型总览图如下：

![](https://s2.loli.net/2022/05/25/rYz7decEGIg2oLa.png "模型架构")

解码器的构建借鉴了残差连接[^34]（Residual Connection）的思想。残差连接由Kaiming He等人于2016年提出，通过引入残差连接，可以缓解梯度消失等问题，使模型更容易被优化。同时，为了在相同条件下比较Conv-GRU与Conv-LSTM的效果，本研究同样构建了基于Conv-LSTM的大气颗粒物污染扩散模拟模型。该模型与基于Conv-GRU的模型相比，仅仅将所有的Conv-GRU块替换为了Conv-LSTM块，以保证其他条件不变。

#### 模型优化

##### 代价函数

代价函数是用于衡量模型预测值与真实值之间差距的函数，通过代价函数，优化算法得以对模型进行优化。常用的代价函数有：均方差（Mean Squared Error，MSE）函数、交叉熵（Cross Entropy）函数等。一般而言，不同的代价函数是针对不同的任务所设计的。根据任务的特点，本研究选用均方差函数作为代价函数。以某时刻观测结果$\hat{\mathcal{X}} \in R^{L \times M \times N}$与预测结果$\tilde{\mathcal{X}} \in R^{L \times M \times N}$为例，均方差函数计算如下：
$$
L(\tilde{\mathcal{X}},\hat{\mathcal{X}}) = \frac{1}{L \times M \times N} \sum_{l=1}^{L} \sum_{m=1}^{M} \sum_{n=1}^{N}(\tilde{\mathcal{X}}(l,m,n) - \hat{\mathcal{X}}(l,m,n))^{2}
$$

##### 样本划分

序列数据是连续的，因此需要引入RNN处理序列数据。但是在实际模型优化的过程中，序列极长导致硬件无法一次加载整个序列。因此在模型优化前需要将序列数据划分为多个样本，在优化时以样本为单位训练神经网络。划分序列时，一般按照固定长度划分，这个长度被称为时间步数（Numer of Steps）。样本划分的方式如下图所示：

![样本划分](https://s2.loli.net/2022/05/25/k7VaXpQEBsovjhO.png "样本划分")

该图展示了时间步数为5时的一个样本。其中蓝框内的5个时刻对应的系统状态将会依次送入模型中以计算隐状态，而当最后一个时刻的系统状态送入模型后，将会给出模型对下一个时刻的系统状态的预测。因此，为了计算损失值，还需要将红框中下一个时刻的系统状态送入损失函数以对模型进行优化。因此，当时间步数为5时，一个样本包含6个时刻的系统状态。

##### 优化算法

机器学习一般通过梯度下降（Gradient Descent）的方式来不断减小损失值达到优化模型的目的。梯度下降算法通过一次性计算全部样本的损失来优化模型，但是在数据规模大幅增长的深度学习领域，这种计算方式对硬件内存有着极高的要求。为此，几乎所有的深度学习算法都采用了梯度下降的扩展算法：随机梯度下降（Stochastic Gradient Descent，SGD）对模型进行优化。具体而言，在随机梯度下降的每一步，算法都会从训练集中抽出一小批量（Minibatch）的样本对模型进行优化。这个小批量中样本的数量称为批量大小（Batch Size）。一般而言，较小的批量大小会导致模型难以优化，而较大的批量大小又会导致硬件压力过大，因此，选择一个合适的批量大小是非常重要的。

自随机梯度下降算法提出以来，至今已经有很多变种。这些算法都有一个称为学习率（Learning Rate）的超参数用于控制模型在小批量上一次优化时改变参数值的大小。学习率是非常难以设置的超参数，并且对模型的性能有着显著的影响。2014年，Diederik Kingma等人提出了Adam算法[^35]，该算法可以自适应学习率，对超参数不敏感，有较强的健壮性。本研究采用Adam算法对模型进行优化。

#### 模型泛化

机器学习的一个核心问题时设计不仅在训练集上表现良好，而且能在测试集上泛化好的算法。在机器学习中，许多策略被显式地设计用以减小测试误差，这些策略统称为正则化（Regularization）。为了提高模型预测效果，本研究采用了批标准化和权重衰减两种正则化策略。

##### 批标准化

批标准化[^36]（Batch Normalization）于2015年由Sergey Ioffe等人提出。批标准化能使优化平面更加平滑，从而更快地进行训练[^37]。同时，批量标准化在计算过程中会随机添加一些噪声（Noise），这通常会加速训练并提高模型泛化能力[^13]。

##### 权重衰减

奥卡姆剃刀（Occam's Razor）原则指出，在同样能够解释已知观测现象的假设当中，应该挑选最简单的那一个，权重衰减（Weight Decay）即是一项体现了奥卡姆剃刀原则的正则化技术。权重衰减通过添加一个惩罚项来限制模型的学习能力，即期望模型能学习到更加简单的表示来增加模型的泛化能力。本研究中通过添加批标准化和权重衰减的正则化策略，期望增强模型泛化能力，进而提高模型性能。

### 编程实现

本研究使用Python编程语言实现上述数据处理、模型构建以及模型优化的所有功能。Python语言有着简洁易读且扩展性强的特点，并且可以引入很多库（Library）来减小实现深度学习模型的难度。实现基于人工智能的大气污染扩散模拟的详细软件硬件配置如下表所示：

| 硬件/软件  |                  型号/版本                   |
| :--------: | :------------------------------------------: |
|    CPU     |      AMD Ryzen 7 3700X 8-Core Processer      |
|    GPU     |        NVIDIA GeForce GTX 1050 Ti 4GB        |
|  操作系统  |               Windows 11 21H2                |
|  编程语言  |                 Python 3.9.7                 |
| 矩阵运算库 |                 NumPy 1.21.5                 |
| 机器学习库 | PyTorch 1.10.1 build:py3.9_cuda11.3_cudnn8_0 |
| 数据处理库 |             NetCDF4-Python 1.5.7             |
|   绘图库   |        Matplotlib 3.5.1、Plotly 5.7.0        |

本研究基于PyTorch库实现了深度学习模型。PyTorch是开源的机器学习库，内置了SGD、Adam等常用的模型优化算法及批标准化、权重衰减等正则化策略，便于直接调用，使得编程时可以聚焦于Conv-GRU的实现。同时，PyTorch支持使用GPU加速，大大缩短了模型优化所需要的时间。为了简化参数调整的过程，本研究在编程实现时留下了用于修改网络各个参数的接口，以便于修改网络参数。同时，本研究使用Matplotlib和Plotly库对模型实时训练效果和最终实验效果进行了可视化，可以直观感受不同参数对训练效果带来的影响。

## 结果和讨论

### 实验结果

在构建基于深度学习的大气颗粒物污染扩散模型时，模型的超参数选择对模型最终的效果有很大的影响。为了详细比较不同模型、正则化策略和超参数对训练带来的影响，本研究以$PM_{10}$为研究对象，通过控制不同变量对模型进行优化并比较模型性能。本研究通过模型在测试集上的均方差来衡量当前参数下模型的性能。一般来说，模型在优化的过程中，模型在测试集上的均方差并不是一直在下降的，通常会保存模型优化过程中均方差最小时模型的参数。因此本研究以模型在优化过程中测试集上均方差的最小值作为模型性能比较的依据。

经多次超参数调整后，效果最佳的超参数组合如下表所示：

|   超参数   |   值   |
| :--------: | :----: |
|  批量大小  |   4    |
|  时间步数  |   5    |
| 隐状态维度 |   64   |
| 卷积核大小 |   5    |
|  批标准化  |  True  |
|  权重衰减  | 0.0005 |



模型优化过程中，模型在训练集和测试集的均方差如下图：

![实验结果1](https://s2.loli.net/2022/05/25/sZHpgF92zRTclS3.png "实验结果1")

如图所示，在第60轮（Epoch）优化时，模型测试集均方差达到最小值30.047，而经200轮优化后，模型训练集均方差达到6.082。可以看出，模型在训练过程中，测试集上的均方差不断减小，训练集上的均方差则先减小后增大，两者之间的差距越来越大，这个差距便被称为泛化差距。

将模型替换为Conv-LSTM模型后，模型优化过程中模型在训练集和测试集的均方差如下图：

![](https://s2.loli.net/2022/05/25/WoyCpnLGu6lJe3N.png "实验结果2") 

在第50轮优化时，Conv-LSTM模型在测试集上的均方差达到最小值35.154，明显劣于Conv-GRU模型，性能较差同时，Conv-LSTM的优化时间和内存消耗也不及Conv-GRU模型。Conv-GRU模型的优化速度可以达到每秒3.428个样本，并且模型参数仅有6594KB，与之相比，Conv-LSTM模型的优化速度仅有每秒3.063个样本，并且模型参数达到了8778KB。并且可以看出，与Conv-GRU模型相比，Conv-LSTM模型的泛化差距始终较大，模型泛化能力较差。

以同样的超参数，$PM_{2.5}$预测模型在模型优化过程中，模型在训练集和测试集上的均方差变化如下图：

![](https://s2.loli.net/2022/05/25/e3DmCo1xt7gVGnH.png "实验结果3")

在第120轮优化时，模型测试集均方差达到最小值24.083，经200轮优化后，模型训练集均方差达到4.061。与$PM_{10}$相比，$PM_{2.5}$模型的均方差明显更小，这是由大气中$PM_{2.5}$浓度小于$PM_{10}$导致的。

### 误差分析

以WRF-Chem的输出为基准，神经网络预测产生误差的原因主要有以下2个方面：

万能近似定理[^38]表明，一个前馈神经网络如果具有线性输出层和至少一层具有任何一种“挤压（squeeze）”性质的激活函数的隐藏层，只要给与网络足够数量的隐藏单元，它就可以以任意精度来近似任何一个从一个有限维空间到另一个有限维空间的Borel可测函数。但是这个足够数量的隐藏单元往往可能大到不可实现。以同类视频预测问题举例，在Moving MNIST数据集上，目前效果最好的深度网络模型Crevnet[^39]的均方差为 22.3，而其模型参数达到了195MB（本研究由于硬件限制，模型参数仅有6.5MB左右）。

尽管万能近似定理表明无论试图学习什么函数，一个足够大的前馈神经网络一定能够表示这个函数，但并不意味着训练算法一定能学习这个函数。主要有以下两点原因：（1）训练算法可能找不到用于期望函数的参数值。这可能由不当的超参数（例如学习率，weight decay等）设置不当，或计算机存储的数值出现下溢等原因引起。（2）训练算法可能由于过拟合而选择了错误的参数。这可能由数据量不足，数据分布不均匀等原因引起。

## 结论

本研究提出了基于Conv-GRU的大气颗粒物污染扩散模拟模型，并通过拟合WRF-Chem的输出对模型进行优化。与其它有关研究相比，本研究主要有以下特点：（1）充分利用时空间大气污染物浓度及气象信息，预测未来大气颗粒物的空间浓度分布；（2）采用了效率更高的深度学习模型，进一步提高了模型的运行速度，降低了模型运行的内存需求，提高了模型的精度；（3）调整了多个超参数并比较对模型训练效果的影响，进一步提升了模型的性能。

本研究基本取得了预期的研究目标，但是仍然存在一些不足和需要进一步研究的内容：	

（1）数据问题。由于现实数据收集困难，本研究的数据来源于WRF-Chem预测的污染物浓度分布，尽管WRF-Chem是较为准确的数值模型，但与真实环境之间仍有较大的差距，并且不能发挥统计模型不依赖于当前大气物理化学理论进展的优势。同时，数据量也是限制模型性能的一大关键，充足的数据量可以更好的发挥深度学习模型的性能。因此，如何收集足量真实环境的数据并据此优化模型是提高模型实用性的重点问题。

（2）模型问题。由于存在硬件限制，本研究仅实现了Conv-GRU一种模型并对该模型进行了优化。事实上，在深度学习时空间预测问题领域还有很多效果优秀的模型值得借鉴，优秀的模型有着更高的精度，更快运行速度以及更小的泛化差距，其它模型在大气颗粒物扩散模拟问题上的效果是下一步值得探讨的问题。

（3）优化问题。尽管本研究采用了一些正则化策略来减小泛化差距，但在本研究中较大的泛化差距依旧是影响模型性能最严重的问题。如何进一步减小泛化差距是提高基于深度学习的大气颗粒物污染扩散模型性能的关键问题，有待进一步研究。

## 参考资料

[^1]: [中共中央 国务院关于深入打好污染防治攻坚战的意见 中央有关文件 中国政府网](http://www.gov.cn/zhengce/2021-11/07/content_5649656.htm)
[^2]:[2020年中国生态环境状况公报](https://www.mee.gov.cn/hjzl/sthjzk/zghjzkgb/202105/P020210526572756184785.pdf)
[^3]:[室内外大气颗粒物和典型有机污染物的环境行为及人体呼吸暴露风险](https://kns.cnki.net/kcms/detail/detail.aspx?dbcode=CDFD&dbname=CDFDLAST2018&filename=1018073474.nh&uniplatform=NZKPT&v=-BH-IhyGE-EZuVAFazqt5wobiT5ktF4JS_LclVGEMvHjaIWBtYDmUb6RXWdnshLQ)
[^4]:[The contribution of outdoor air pollution sources to premature mortality on a global scale](https://www.nature.com/articles/nature15371)
[^5]:[Application of bias adjustment techniques to improve air quality forecasts]()
[^6]:[Urban air quality and regional haze weather forecast for Yangtze River Delta region]()
[^7]:[国内外空气质量模型研究进展](https://kns.cnki.net/kcms/detail/detail.aspx?dbcode=CJFD&dbname=CJFD2013&filename=HJKD201303006&uniplatform=NZKPT&v=f_bZin98q_qMwR_UrNP9EmHcGlJqIXp4A87eVNu4VYll9YyCy1xbI7ldTllPyUUt)
[^8]:[CALPUFF Modeling System](http://www.src.com/)
[^9]:[WRF-CHEM](https://www2.acom.ucar.edu/wrf-chem)
[^10]:[The Community Multiscale Air Quality Modeling System](https://www.epa.gov/cmaq)
[^11]:[Progress on the Development and Application of Air Quality Models](http://www.hjjkyyj.com/hjjkyyj_en/ch/reader/view_abstract.aspx?file_no=A20140831001&flag=1)
[^12]:[Statistical Modeling Approaches for PM10 Predictionin Urban Areas; A Review of 21st-Century Studies](https://www.researchgate.net/publication/292072964_Statistical_Modeling_Approaches_for_PM10_Prediction_in_Urban_Areas_A_Review_of_21st-Century_Studies)
[^13]:[Dive into Deep Learning](https://d2l.ai/)
[^14]:[Deep Learning](https://www.deeplearningbook.org/)
[^15]:[Reducing the Dimensionality of Data with Neural Networks](https://pubmed.ncbi.nlm.nih.gov/16873662/)
[^16]:[基于深度学习的大数据空气污染预报](https://kns.cnki.net/kcms/detail/detail.aspx?dbcode=CJFD&dbname=CJFDLAST2016&filename=ZHGL201506013&uniplatform=NZKPT&v=ac3fyeKJ2wb0zZ0Rq84x49Am5z8DTb4ou0xNviU_vBznzMl7sG0KIdw0JyRcYHqU)
[^17]:[Long Short-term Memory](https://www.researchgate.net/publication/13853244_Long_Short-term_Memory)
[^18]:[Long short-term memory neural network for air pollutant concentration predictions: Method development and evaluation](https://www.researchgate.net/publication/319714176_Long_short-term_memory_neural_network_for_air_pollutant_concentration_predictions_Method_development_and_evaluation)
[^19]:[Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation](http://cn.arxiv.org/abs/1406.1078)
[^20]:[Backpropagation Applied to Handwritten Zip Code Recognition](https://ieeexplore.ieee.org/document/6795724)
[^21]:[Photo-Realistic Video Prediction on Natural Videos of Largely Changing Frames](https://arxiv.org/abs/2003.08635)
[^22]:[Unsupervised Learning of Video Representations using LSTMs](https://arxiv.org/abs/1502.04681v3)
[^23]:[Convolutional LSTM Network: A Machine Learning Approach for Precipitation Nowcasting](https://arxiv.org/abs/1506.04214v2)
[^24]:[基于深度学习的大气细颗粒物污染时空预报研究](https://kns.cnki.net/kcms/detail/detail.aspx?dbcode=CMFD&dbname=CMFD202201&filename=1021679960.nh&uniplatform=NZKPT&v=2jaQGsvXotQ08OsWU1Mdsu14nRD33qb__5K-MNp9acXjQXWhtJjskmB3bbvcIJUq)
[^25]:[Video Prediction](https://paperswithcode.com/task/video-prediction)
[^26]:[Network Common Data Form (NetCDF)](https://www.unidata.ucar.edu/software/netcdf/)
[^27]:[netcdf4-python](http://unidata.github.io/netcdf4-python/)
[^28]:[NumPy](https://numpy.org/)
[^29]:[Learning representations by back-propagating errors](https://www.nature.com/articles/323533a0)
[^30]:[Learning long-term dependencies with gradient descent is difficult](https://ieeexplore.ieee.org/document/279181)
[^31]:[On the Properties of Neural Machine Translation: Encoder-Decoder Approaches](https://arxiv.org/abs/1409.1259)
[^32]:[Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling](https://arxiv.org/abs/1412.3555)
[^33]:[Deep Learning for Precipitation Nowcasting: A Benchmark and A New Model](https://arxiv.org/abs/1706.03458)
[^34]:[Deep Residual Learning for Image Recognition](https://ieeexplore.ieee.org/document/7780459/figures#figures)
[^35]:[Adam: A Method for Stochastic Optimization](https://www.researchgate.net/publication/269935079_Adam_A_Method_for_Stochastic_Optimization#:~:text=We%20introduce%20Adam%2C%20an%20algorithm%20for%20first-order%20gradient-based,adaptive%20estimates%20of%20lower-order%20moments%20of%20the%20gradients.)
[^36]:[Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift](https://arxiv.org/abs/1502.03167)
[^37]:[How Does Batch Normalization Help Optimization?](https://arxiv.org/abs/1805.11604)
[^38]:[Multilayer feedforward networks are universal approximators](https://www.sciencedirect.com/science/article/abs/pii/0893608089900208)
[^39]:[CrevNet: Conditionally Reversible Video Prediction](https://arxiv.org/abs/1910.11577)


---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/%E6%AF%95%E4%B8%9A%E8%AE%BE%E8%AE%A1-%E5%9F%BA%E4%BA%8E%E4%BA%BA%E5%B7%A5%E6%99%BA%E8%83%BD%E7%9A%84%E5%A4%A7%E6%B0%94%E9%A2%97%E7%B2%92%E6%B1%A1%E6%9F%93%E7%89%A9%E6%89%A9%E6%95%A3%E6%A8%A1%E6%8B%9F%E6%8A%80%E6%9C%AF%E7%9A%84%E7%A0%94%E7%A9%B6/  

