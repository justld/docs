## torch.nn.LayerNorm
### [torch.nn.LayerNorm](https://pytorch.org/docs/stable/generated/torch.nn.LayerNorm.html?highlight=layernorm#torch.nn.LayerNorm)

```python
torch.nn.LayerNorm(normalized_shape,
                   eps=1e-05,
                   elementwise_affine=True,
                   device=None,
                   dtype=None)
```

### [paddle.nn.LayerNorm](https://www.paddlepaddle.org.cn/documentation/docs/zh/api/paddle/nn/LayerNorm_cn.html#layernorm)

```python
paddle.nn.LayerNorm(normalized_shape,
                    epsilon=1e-05,
                    weight_attr=None,
                    bias_attr=None,
                    name=None)
```

两者功能一致但参数不一致，部分参数名不同，具体如下：
### 参数差异
| PyTorch       | PaddlePaddle | 备注                                                   |
| ------------- | ------------ | ------------------------------------------------------ |
| eps           | epsilon      | 为了数值稳定加在分母上的值。                                     |
| -             | weight_attr  | 指定权重参数属性的对象。如果为 False, 则表示每个通道的伸缩固定为 1，不可改变。默认值为 None，表示使用默认的权重参数属性。 |
| -             | bias_attr    | 指定偏置参数属性的对象。如果为 False, 则表示每一个通道的偏移固定为 0，不可改变。默认值为 None，表示使用默认的偏置参数属性。 |
| -             | name         | 层名称，Pytorch 无此参数，Paddle 保持默认即可。 |
| elementwise_affine        | -            | 是否进行反射变换，PaddlePaddle 无此参数。         |
| device        | -            | 设备类型，PaddlePaddle 无此参数。 |
| dtype         | -            | 参数类型，PaddlePaddle 无此参数。         |

### 转写示例
#### affine：是否进行反射变换
```python
# 当 PyTorch 的反射变换设置为`False`，表示 weight 和 bias 不进行更新，Paddle 可用代码组合实现该 API
class LayerNorm(paddle.nn.LayerNorm):
    def __init__(self,
                 normalized_shape,
                 eps=1e-05,
                 elementwise_affine=True,
                 device=None,
                 dtype=None):
        weight_attr = None
        bias_attr = None
        if not elementwise_affine:
            weight_attr = paddle.ParamAttr(learning_rate=0.0)
            bias_attr = paddle.ParamAttr(learning_rate=0.0)
        super().__init__(
                 normalized_shape,
                 epsilon=eps,
                 weight_attr=weight_attr,
                 bias_attr=weight_attr,
                 name=None)

# 当 PyTorch 的 elementwise_affine 为`True`，torch 写法
torch.nn.LayerNorm(normalized_shape=(256, 256), eps=1e-05, elementwise_affine=True)

# paddle 写法
paddle.nn.LayerNorm(normalized_shape=(256, 256), epsilon=1e-05)
```