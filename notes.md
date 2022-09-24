# snn-iir

* 原代码没有提供conv snn，只提供了mlp snn
* 原代码只处理了mnist，其他数据集都没处理
* 原代码中mlp snn中没有把train set分成train set和dev set，
而是直接用了test set作为dev set，可能论文里面也没有分。
把trainset分成50000+10000
* dataset本身会droplast，改成不droplast
* 1和2的区别是dual_exp_iir_layer和first_order_low_pass_layer


1. 添加了snn_conv_1_mnist模型
2. non_zero就是网络状态的初始化比较特别，其他都一样的
3. snn_conv_1_mnist_poisson_input占内存太多了，把batch size从64降到32
4. associative memory内存不够，把batch size从64降到32，epoch从50升到100
5. snn_conv_1_nmnist的length从25改成30，那么相当于每个sample有30步、每步是10ms的accumulate（OR计算）结果。另外，每个timestamp都要先/1000，才能变成ms的单位，一共有300步
6. multiprocessing来加载nmnist的数据集，不用的话太慢了
7. nmnist数据集论文中，说明了数据是300ms的，所以超过300ms的都截掉了，而且要除以1000才是ms，时间要减去最早的时间，超过300ms的截掉,batch size从64改成48，三张卡
8. gesture数据集论文中，说Each gesture lasts about 6 seconds，没有固定的长度。。。128x128,2 channel,时间长度范围(1750000, 18456951)，均值6531519.463114754
9. gesture从25步改成50步，每个sample都搞成50步，所以不同sample的步的时间不同，batch size改成16..论文中网络结构写成10，不清楚是不是去掉了其他手势，还是说只是typo。。。反正我的是11分类的。。。288个test data、1176个train data(1000+176),更小的batch 8
    1. test_data 264才对
    2. train data 1077才对
    3. 用spikingjelly读取事件数据，train有1056（928+128），test为288
10. Australian Sign Language signs (High Quality) Data Set

## gesture结果

1. gesture:30长度
    1. Best train acc: 0.9609375, epoch: 59
    2. Best test acc: 0.859375, epoch: 59
    3. best checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_59_20210124-131913
    4. Test checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_30_59_20210124-131913, acc: 0.6875
2. gesture:50长度
    1. Best train acc: 0.9547413793103449, epoch: 46
    2. Best test acc: 0.8515625, epoch: 46
    3. best checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_46_20210124-140846
    4. Test checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_46_20210124-140846, acc: 0.6631944444444444
3. gesture:avg
    1. Best train acc: 0.08189655172413793, epoch: 0
    2. Best test acc: 0.0859375, epoch: 0
    3. best checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_avg_0_20210125-105229
    4. 失败尝试
4. gesture:max
    1. Best train acc: 0.978448275862069, epoch: 68
    2. Best test acc: 0.7578125, epoch: 68
    3. best checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_max_68_20210125-122242
    4. Test checkpoint: ./checkpoint/checkpoint_snn_conv_1_gesture_max_68_20210125-122242, acc: 0.7048611111111112


1. 训练好之后，把dev上最佳的model path写入yaml中，然后再跑--test代码
2. non_zero、poisson_input要不要做？
3. poisson_input用到基于poisson input的数据集处理class，有一个mnist poisson input的例子，要不要做？
4. n-caltech101搞不搞？

 ## to do?

1. 50长度的训练结果如何？测试结果如何？
2. 30长度的训练完了，但还没测试
3. 50长度，但是sum的方式，而不是OR的spike处理方式，训练如何？测试如何？

