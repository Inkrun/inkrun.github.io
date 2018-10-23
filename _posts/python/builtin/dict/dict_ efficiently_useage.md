字典的高效使用
-----
<table>
<thead>
<tr>
<td>条目</td>
<td>bad</td>
<td>good</td>
<td>原因</td>
</tr>
</thead>
<tbody>
<tr>
<td>检查 key 是否存在</td>
<td>
<pre lang='python'>
d = {'name': 'python'}
if d.has_key('name'):
    pass
</pre>
</td>
<td>
<pre lang="python">
if 'name' in d:
    pass
</pre>
</td>
<td>`in` 关键字处理更快（?），`Python` 3中已经稯 `has_key` 方法</td>
</tr>
<tr>
<td>获取字典值</td>
<td><pre lang="python">
d = {'name': 'python'}
if 'name' in d:
    print(d['hello'])
else:
    print('default')
</pre></td>
<td><pre lang='python'>
print(d.get("name", "default"))
</pre></td>
<td>`key` 不存在时，返回默认值，不会报出`KeyError`的错误</td>
</tr>
<tr>
<td>设置字典值</td>
<td><pre lang='python'>
for (key, value) in data:
    if key in groups:
        groups[key].append(value)
    else:
        groups[key] = [value]
</pre></td>
<td><pre lang='python'>
groups = {}
for (key, value) in data:
    groups.setdefault(key, []).append(value)
</pre></td>
<td>`setdefault` 的作用是 : <ol><li>如果 key 存在于字典中，那么直接返回对应的值，等效于 get 方法;</li><li>如果 key 不存在字典中，则会用 setdefault 中的第二个参数作为该 key 的值，再返回该值。</li></ol></td>
</tr>
<tr><td>字典初始化</td>
<td colspan=2><pre lang='python'>
from collections import defaultdict
 
groups = defaultdict(list)
for (key, value) in data:
    groups[key].append(value)
</pre></td>
<td>当 key 不存在于字典中时，list 函数将被调用并返回一个空列表赋值给 d[key]，这样一来，你就不用担心调用 d[k] 会报错了。</td>
</tr>
<tr>
<td>将列表转换成字典</td>
<td colspan=2><pre lang='python'>
keys = {'a', 'e', 'i', 'o', 'u' }
value = []
d = dict.fromkeys(keys, value)
print(d)
# result
#{'i': [], 'u': [], 'e': [], 'a': [], 'o': []}
</pre>
</td><td></td>
</tr>
<tr>
<td>用字典实现 `switch ... case` 语句
</td>
<td><pre lang='python'>
if arg == 0:
    return 'zero'
elif arg == 1:
    return 'one'
elif arg == 2:
    return "two"
else:
    return "nothing"
</pre></td>
<td><pre lang='python'>
data = {
 0: "zero",
 1: "one",
 2: "two",
}
data.get(arg, "nothing")
</pre></td><td>Python 中没有 switch ... case 语句，这个问题Python之父龟叔表示这个语法过去没有，现在没有，以后也不会有。因为Python简洁的语法完全可以用 if ... elif 实现。如果有太多的分支判断，还可以使用字典来代替。</td>
</tr>
<tr><td>使用 iteritems 迭代字典中的元素</td>
<td><pre lang='python'>
d = {
    0: "zero",
    1: "one",
    2: "two",
}
 
for k, v in d.items():
    print(k, v)
</pre></td><td><pre lang='python'>
for k, v in d.iteritems():
    print(k, v)
</pre>
</td><td><ul>
<li>items 方法返回的是（key ,value）组成的列表对象，这种方式的弊端是迭代超大字典的时候，内存瞬间会扩大两倍，因为列表对象会一次性把所有元素加载到内存，更好的方式是使用 iteritems</li>
<li>iteritems 返回的是迭代器对象，迭代器对象具有惰性加载的特性，只有真正需要的时候才生成值，这种方式在迭代过程中不需要额外的内存来装载这些数据。注意 Python3 中，只有 items 方法了，它等价于 Python2 中的 iteritems，而 iteritems 这个方法名被移除了。</li></ul></td>
</tr>
<tr><td>字典推导式</td>
<td><pre lang='python'>
numbers = [1,2,3]
d = dict([(number,number*2) for number in numbers])
</pre></td>
<td><pre lang='python'>
numbers = [1, 2, 3]
d = {number: number * 2 for number in numbers}
</pre></td>
<td>推导式是个绝妙的东西，列表推导式一出，map、filter等函数黯然失色，自 Python2.7以后的版本，此特性扩展到了字典和集合身上，构建字典对象无需调用 dict 方法。</td>
</tr>
</tbody>
</table>
