# ReLUFormer.c：一个用C语言实现的全ReLU Transformer

这是一个在Attention，FFN和Output处均采用ReLU和其变体激活函数的Transformer，旨在**提高模型表现的同时**支持全流程且端到端可微分的稀疏计算和优化。

- **Attention**: 基于ReLU^2过滤掉负点积值并制造Softmax式的尖峰性，采用L1归一化分数并促进稀疏解，不使用密集的Softmax注意力。
- **FFN**: 基于ReLU^2激活函数的FFN，已在RWKV系列和Primer论文中被证明表现良好，具有超越SwiGLU的性能。
- **Output**: 使用ReLU过滤负相关词的概率，更符合人类进行预测时的行为，只在总词表中选取一个可能子集。
- **.c** 基于karpathy的llama2.c实现，便于训练，验证和推理。

## 如何训练

### 数据处理

参考[README_llama2.c.md](./README_llama2.c.md)进行数据推理

```bash
python3 tinystories.py download
python3 tinystories.py train_vocab --vocab_size=4096
python3 tinystories.py pretokenize --vocab_size=4096
```

### 训练

```bash
python3 train.py
```

### 测试

```bash
pip install pytest
python test_all.py
```

###稀疏性与尖峰性
![Softmax热力图](https://github.com/DisOOM/ReLUFormer.c/blob/main/AttnMatForSoftMax.png)
![ReLU热力图](https://github.com/DisOOM/ReLUFormer.c/blob/main/AttnMatForReLU.png)
可见，ReLU Attention具有更高的尖峰性，这使得在需要精确“点关注”的任务场景下ReLU Attention能工作的更好，但也能进行均匀分布的关注。
与此同时ReLU Attention在这些token中通过ReLU大量置以负值，模型可以提高信噪比，理论上能够缓解SoftMax Attention在长上下文中的注意力稀释问题。

## 许可证

MIT
