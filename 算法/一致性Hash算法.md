# 一致性Hash算法

1.  hash 算法都是将 value 映射到一个 32 位的 key 值, 看成一个环形空间
2. 把需要缓存的key映射到hash 空间
3. 把服务器节点映射到 hash 空间
4. 根据范围把key映射到服务器节点

这样, 每当增减节点的时候, 只要重新映射该节点前的key 即可

问题点:

​	如果节点较少, key 不能均匀缓存到节点上

解决方案:

​	新增虚拟节点, 例如原节点 hash('202.168.14.241'), 可引入虚拟节点 hash('202.168.14.241#1')

```python
# 这里有两个问题: 一个是效率问题, hash_map 不能用 list; 另一个是hash 碰撞怎么办? 当然这两个都是小概率问题, 因为节点不会很多, 但是 get_node 查找应该改一下, 至少二分

from hashlib import md5

class HashConsistency:
    def __init__(self, nodes):
        self.nodes = nodes
        self.hash_map = []   # hash: node, 这里的数据结构应该采用何种数据结构: 快速插入; 给定任意值n, 可快速查找 >n 的所有值中的最小值, 红黑树可以
        self.virtual_node_replicas = 5
        self.virtual_node_map = {}  # node: [virtual_node_hash]

        for n in nodes:
            self.add_node(n)

    def _get_hash(self, x):
        x = str(x)
        return int(md5(x.encode()).hexdigest(), 16) & (-1 ^ (-1 << 32))

    def add_node(self, node):
        self.hash_map.append([self._get_hash(node), node])
        virtual_node_map = []

        for i in range(self.virtual_node_replicas):
            virtual_node = '%s#%d' % (node, i)
            virtual_hash = self._get_hash(virtual_node)
            self.hash_map.append([virtual_hash, node])
            virtual_node_map.append(virtual_hash)

        self.hash_map.sort(key=lambda x: x[0])
        self.virtual_node_map[node] = virtual_node_map


    def get_node(self, data):
        hash = self._get_hash(data)
				# 这里查找太慢
        for i in self.hash_map:
            if hash < i[0]:
                return i[1]

        return self.hash_map[0][1]


    def remove_node(self, node):
        hash_map = []
        remove_hash = self.virtual_node_map[node] + [self._get_hash(node)]

        for i in self.hash_map:
            if i[0] not in remove_hash:
                hash_map.append(i)

        self.hash_map = hash_map
        del self.virtual_node_map[node]


a = HashConsistency(['10.1.1.1', '10.1.1.2', '10.1.1.3', '10.1.1.4'])

for i in range(10):
    print(a.get_node(i))
```



