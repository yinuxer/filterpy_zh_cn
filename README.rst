FilterPy  -  Python中的卡尔曼滤波器和其他最优和非最佳估计滤波器。
-----------------------------------------------------------------------------------------

.. image:: https://img.shields.io/pypi/v/filterpy.svg
        :target: https://pypi.python.org/pypi/filterpy

**注意**：Python 2.7,3.4的支持即将下降。请参阅以下部分了解详情。

该库提供用Python实现的卡尔曼滤波和各种相关的最优和非最优滤波算法。它包含卡尔曼滤波器，扩展卡尔曼滤波器，无迹卡尔曼滤波器，卡尔曼
平滑器，最小二乘滤波器，衰落记忆滤波器，g-h滤波器，
离散贝叶斯等等。

该代码是我联合我的书《卡尔曼和贝叶斯滤波器的Python 实现》实现的代码，您可以在下面链接中阅读/下载
https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python/

我的目标很大程度上是学术性的 - 我选择了与相关文本中的方程式一对一的清晰代码，即使影响性能。这种权衡在有些地方不太明确 - 例如，我发现使用线性代数编写一小组方程更为清晰，但numpy在小矩阵上的过度开销使得它比手动编写每个方程要运行地慢。此外，Zarchan这样的书籍呈现出书面形式，而不是线性代数形式。
我很难选择哪个演示文稿“更清晰” - 这取决于观众。在这种情况下，我通常选择更快的实现方式。

我使用NumPy和SciPy进行所有计算。我已经尝试过Numba，它以最低的成本产生了令人印象深刻的速度，但我不相信我想将这个要求添加到我的项目中。然而，仍然在我的事情清单上。

Sphinx生成的文档位于http://filterpy.readthedocs.org/。
当我办理登机手续时，git会触发生成，所以这将始终是最前沿的开发版本 - 它通常会超过发布版本。 


放弃Python 2.7支持的计划------------------------------------

我还没有最终确定我的决定，但NumPy在2018年12月放弃了Python 2.7的支持。到那时我肯定会放弃Python 2.7的支持;我可能会早点做到这一点。

目前，FilterPy的版本为1.x. 我打算将项目分支到2.0版，并且只支持Python 3.5+。1.x版本仍然可用，但我不会支持它。如果我添加一些令人惊奇的东西到2.0并且有人真的乞求，我可能会后退它;我更有可能接受一个拉回请求，并将该功能反向移植到1.x. 但说实话，我并不预料到这种情况会发生。

为什么3.5+，而不是3.4+？3.5引入了矩阵乘法符号，我希望我的代码能够利用它。另外，说实话，我很自私。我不想花费我的生命来支持这个软件包，并且尽可能多地移动到现在意味着在我选择的Python版本变得无可救药地过时并且承担责任之前的几年。我知道这使得人们在他们的Linux发行版中运行默认的Python更加痛苦。我只能说我没有决定做Python 3分叉，而且我没有时间再支持分叉了。

我现在正在编辑包以支持我的书;一旦完成，我可能会创建2.0分支。
我正在考虑为这本书添加一个SLAM，我不确定我是否会在3.5+中执行此操作。


安装------------

最常见的安装只是使用pip，它应该附带任何现代Python发行版。

.. image:: https://img.shields.io/pypi/v/filterpy.svg
        :target: https://pypi.python.org/pypi/filterpy
        
::

    pip install filterpy

如果您更喜欢自己下载源代码

::

    cd <directory you want to install to>
    git clone http://github.com/rlabbe/filterpy
    python setup.py install

如果您使用Anaconda，则可以从conda-forge频道安装。如果您还没有这样做，则需要添加conda-forge通道：

::
    conda config --add channels conda-forge
    
然后安装：

::
    conda install filterpy
    

并且，如果你想从最前沿的git版本安装

::

    pip install git+https://github.com/rlabbe/filterpy.git

注意：如果从这里安装，我不保证一切正常。
我是唯一的开发人员，因此我不担心开发/发布分支等。除非我为你修复了一个bug并告诉你得到这个版本因为我还没有发布新版本，所以我强烈建议不要从git安装。


    

基本用途---------

完整文档位于https://filterpy.readthedocs.io/en/latest/


首先，导入过滤器和辅助函数。

.. code-block:: python

    import numpy as np
    from filterpy.kalman import KalmanFilter
    from filterpy.common import Q_discrete_white_noise

现在，创建过滤器

.. code-block:: python

    my_filter = KalmanFilter(dim_x=2, dim_z=1)


初始化过滤器的矩阵。

.. code-block:: python

    my_filter.x = np.array([[2.],
                    [0.]])       # initial state (location and velocity)

    my_filter.F = np.array([[1.,1.],
                    [0.,1.]])    # state transition matrix

    my_filter.H = np.array([[1.,0.]])    # Measurement function
    my_filter.P *= 1000.                 # covariance matrix
    my_filter.R = 5                      # state uncertainty
    my_filter.Q = Q_discrete_white_noise(2, dt, .1) # process uncertainty


Finally, run the filter.

.. code-block:: python

    while True:
        my_filter.predict()
        my_filter.update(get_some_measurement())

        # do something with the output
        x = my_filter.x
        do_something_amazing(x)

对不起，这是文档的范围。但是，库被分解为子目录：gh，kalman，memory，leastsq等。
每个子目录都包含与该形式的过滤器相关的python文件。
函数和方法包含非常好的文档字符串。

我的书https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python/使用这个库，如果你想了解卡尔曼过滤和/或这个库，那么它就是你要去的地方。这两个并不完全同步 - 我的正常开发周期是在这里添加文件，测试它们，弄清楚如何以家谱方式呈现它们，然后在书中编写相应的部分或章节。所以这里的代码还没有在书中讨论过。


要求  -  -  -  -  -  -

该库使用NumPy，SciPy，Matplotlib和Python。 

我没有广泛测试向后兼容性 - 我使用Anaconda发行版，因此我使用的是Python 3.6和2.7.14，以及它们提供的任何版本的NumPy，SciPy和matplotlib。但是我使用非常基本的Python  -  numpy.array，在我的测试中可能是列表理解。

我从** __ future __ **导入以确保代码在Python 2和3中有效。


测试-------

编写所有测试以使用py.test。只需在命令行输入``py.test``即可。

如上所述，测试不稳健。我仍处于视觉情节是了解事物如何运作的最佳方式的阶段。
道歉，但我认为这是发展的合理选择。过滤器很容易在理论极限内执行（我们可以编写非可视化测试）但在某种程度上“关闭”。代码本身包含断言和属性形式的测试，以确保数组具有适当的维度等。

参考文献----------

I use three main texts as my refererence, though I do own the majority
of the Kalman filtering literature. First is Paul Zarchan's
'Fundamentals of Kalman Filtering: A Practical Approach'. I think it by
far the best Kalman filtering book out there if you are interested in
practical applications more than writing a thesis. The second book I use
is Eli Brookner's 'Tracking and Kalman Filtering Made Easy'. This is an
astonishingly good book; its first chapter is actually readable by the
layperson! Brookner starts from the g-h filter, and shows how all other
filters - the Kalman filter, least squares, fading memory, etc., all
derive from the g-h filter. It greatly simplifies many aspects of
analysis and/or intuitive understanding of your problem. In contrast,
Zarchan starts from least squares, and then moves on to Kalman
filtering. I find that he downplays the predict-update aspect of the
algorithms, but he has a wealth of worked examples and comparisons
between different methods. I think both viewpoints are needed, and so I
can't imagine discarding one book. Brookner also focuses on issues that
are ignored in other books - track initialization, detecting and
discarding noise, tracking multiple objects, an so on.

I said three books. I also like and use Bar-Shalom's Estimation with
Applications to Tracking and Navigation. Much more mathematical than the
previous two books, I would not recommend it as a first text unless you
already have a background in control theory or optimal estimation. Once
you have that experience, this book is a gem. Every sentence is crystal
clear, his language is precise, but each abstract mathematical statement
is followed with something like "and this means...".


License
-------
.. image:: https://anaconda.org/rlabbe/filterpy/badges/license.svg   :target: https://anaconda.org/rlabbe/filterpy

The MIT License (MIT)

Copyright (c) 2015 Roger R. Labbe Jr

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.TION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
