기본 틀
```python
from torch.utils.data import random_split

random_split(dataset, lengths)
```


예시
```python
train_ds, test_ds = random_split(full, [0.9, 0.1])
#   ↑         ↑
#  0.9       0.1  ← 순서 그대로 대응
```
