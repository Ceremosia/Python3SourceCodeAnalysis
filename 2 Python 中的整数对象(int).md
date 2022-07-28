# 2. Python中的整数类型(int)
&emsp;&emsp;Python 中存在三种不同的数字类型: 整数, 浮点数 和 复数。 此外，布尔值属于整数的子类型。 

&emsp;&emsp;整数具有无限的精度。 

&emsp;&emsp;浮点数通常使用 C 中的 double 来实现。

&emsp;&emsp;复数包含实部和虚部，分别以一个浮点数表示。要从一个复数 z 中提取这两个部分，可使用 `z.real` 和 `z.imag`。

&emsp;&emsp;先讨论最常使用到的整数类型，即 `int`。值得注意的是，`int` 类型对应到源码中的关键词是 `long` （在 C 中用 `long` 来表示长整型） 而不是 `int`。

&emsp;&emsp;`int` 类型的类型是 `type`，`int` 类的基类是 `object`。
&emsp;&emsp;`int` 类型对应的实例是 `PyLong_Type`。
&emsp;&emsp;`int` 类型对应的结构体是 `PyLongObject`。

```python3
>>> int
<class 'int'>
>>> int.__class__
<class 'type'>
>>> int.__base__
<class 'object'>
```

&emsp;&emsp;当我们讨论一个 Python 中的对象时，会考虑它是否可变，即区分为“可变对象”和“不可变对象”，以及它的长度是否可变，即区分为“定长对象”和“变长对象”。**`int` 类型生成的对象**是“不可变”“变长”的。

&emsp;&emsp;先看 `PyLongObject`，`PyLongObject` 的定义如下。
```c
[/Include/pytypedefs.h]
typedef struct _longobject PyLongObject;

[/Include/cpython/longintrepr.h]
struct _longobject {
    PyObject_VAR_HEAD
    digit ob_digit[1];
};
```

&emsp;&emsp;将两段合并，并展开其中的宏，得到：

```c
struct _longobject {
    struct {
    	struct _object {
		    Py_ssize_t ob_refcnt;
		    PyTypeObject *ob_type;
		} ob_base;
    	Py_ssize_t ob_size; /* Number of items in variable part */
	} ob_base;

    digit ob_digit[1];
}PyLongObject;
```

&emsp;&emsp;先忽略其中的结构体嵌套，那么就得到：

```c
struct _longobject {
    Py_ssize_t ob_refcnt;
    PyTypeObject *ob_type;
   	Py_ssize_t ob_size; /* Number of items in variable part */
    digit ob_digit[1];
}PyLongObject;
```

&emsp;&emsp;一共4个字段，前3个字段来自父类 `PyVarObject`，那么最后一个字段显然就是用来存储 `PyLongObject` 对象的值了。

XXX: 解释 digit，解释 ob_digit扩容、存储（30位有效数字），解释负数，解释 0 的长度是 24而 1的长度是 28（即特例0）

>&emsp;&emsp;如果你喜欢考究的话，你会发现在 Python2 中，同时存在着 `PyIntObject` 和 `PyLongObject` 两种类型，前者所生成的对象是不可变长度的，受限于具体的系统，在跨平台时可能会出错。而后者更像如今的 `PyLongObject`，是可变长度的，仅受限于内存。具体可查看 [PEP 237 – Unifying Long Integers and Integers](https://peps.python.org/pep-0237/)
