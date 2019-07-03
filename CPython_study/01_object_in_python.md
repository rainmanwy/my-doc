# python中的object

# PyObject

* 所有python中的object的父类

```c
typedef struct _object {
    _PyObject_HEAD_EXTRA
    Py_ssize_t ob_refcnt;
    struct _typeobject *ob_type;
} PyObject;
```

## Py_ssize_t

* 大小为8个字节（64bit系统）

## PyVarObject

* python中所有变长object的父类

```c
typedef struct {
    PyObject ob_base;
    Py_ssize_t ob_size; /* Number of items in variable part */
} PyVarObject;
```

## PyTypeObject

* 类型定义

```c
typedef struct _typeobject {
    PyObject_VAR_HEAD
    const char *tp_name; /* For printing, in format "<module>.<name>" */
    Py_ssize_t tp_basicsize, tp_itemsize; /* For allocation */

    /* Methods to implement standard operations */

    destructor tp_dealloc;
    Py_ssize_t tp_vectorcall_offset;
    getattrfunc tp_getattr;
    setattrfunc tp_setattr;
    PyAsyncMethods *tp_as_async; /* formerly known as tp_compare (Python 2)
                                    or tp_reserved (Python 3) */
    reprfunc tp_repr;

    /* Method suites for standard classes */

    PyNumberMethods *tp_as_number;
    PySequenceMethods *tp_as_sequence;
    PyMappingMethods *tp_as_mapping;

    /* More standard operations (here for binary compatibility) */

    hashfunc tp_hash;
    ternaryfunc tp_call;
    reprfunc tp_str;
    getattrofunc tp_getattro;
    setattrofunc tp_setattro;

    /* Functions to access object as input/output buffer */
    PyBufferProcs *tp_as_buffer;

    /* Flags to define presence of optional/expanded features */
    unsigned long tp_flags;

    const char *tp_doc; /* Documentation string */

    /* Assigned meaning in release 2.0 */
    /* call function for all accessible objects */
    traverseproc tp_traverse;

    /* delete references to contained objects */
    inquiry tp_clear;

    /* Assigned meaning in release 2.1 */
    /* rich comparisons */
    richcmpfunc tp_richcompare;

    /* weak reference enabler */
    Py_ssize_t tp_weaklistoffset;

    /* Iterators */
    getiterfunc tp_iter;
    iternextfunc tp_iternext;

    /* Attribute descriptor and subclassing stuff */
    struct PyMethodDef *tp_methods;
    struct PyMemberDef *tp_members;
    struct PyGetSetDef *tp_getset;
    struct _typeobject *tp_base;
    PyObject *tp_dict;
    descrgetfunc tp_descr_get;
    descrsetfunc tp_descr_set;
    Py_ssize_t tp_dictoffset;
    initproc tp_init;
    allocfunc tp_alloc;
    newfunc tp_new;
    freefunc tp_free; /* Low-level free-memory routine */
    inquiry tp_is_gc; /* For PyObject_IS_GC */
    PyObject *tp_bases;
    PyObject *tp_mro; /* method resolution order */
    PyObject *tp_cache;
    PyObject *tp_subclasses;
    PyObject *tp_weaklist;
    destructor tp_del;

    /* Type attribute cache version tag. Added in version 2.6 */
    unsigned int tp_version_tag;

    destructor tp_finalize;
    vectorcallfunc tp_vectorcall;

#ifdef COUNT_ALLOCS
    /* these must be last and never explicitly initialized */
    Py_ssize_t tp_allocs;
    Py_ssize_t tp_frees;
    Py_ssize_t tp_maxalloc;
    struct _typeobject *tp_prev;
    struct _typeobject *tp_next;
#endif
} PyTypeObject;
```

## PyType_Type

* type的类型定义

```c
PyTypeObject PyType_Type = {
    PyVarObject_HEAD_INIT(&PyType_Type, 0)
    "type",                                     /* tp_name */
    sizeof(PyHeapTypeObject),                   /* tp_basicsize */
    sizeof(PyMemberDef),                        /* tp_itemsize */
    (destructor)type_dealloc,                   /* tp_dealloc */
    0,                                          /* tp_vectorcall_offset */
    0,                                          /* tp_getattr */
    0,                                          /* tp_setattr */
    0,                                          /* tp_as_async */
    (reprfunc)type_repr,                        /* tp_repr */
    0,                                          /* tp_as_number */
    0,                                          /* tp_as_sequence */
    0,                                          /* tp_as_mapping */
    0,                                          /* tp_hash */
    (ternaryfunc)type_call,                     /* tp_call */
    0,                                          /* tp_str */
    (getattrofunc)type_getattro,                /* tp_getattro */
    (setattrofunc)type_setattro,                /* tp_setattro */
    0,                                          /* tp_as_buffer */
    Py_TPFLAGS_DEFAULT | Py_TPFLAGS_HAVE_GC |
        Py_TPFLAGS_BASETYPE | Py_TPFLAGS_TYPE_SUBCLASS,         /* tp_flags */
    type_doc,                                   /* tp_doc */
    (traverseproc)type_traverse,                /* tp_traverse */
    (inquiry)type_clear,                        /* tp_clear */
    0,                                          /* tp_richcompare */
    offsetof(PyTypeObject, tp_weaklist),        /* tp_weaklistoffset */
    0,                                          /* tp_iter */
    0,                                          /* tp_iternext */
    type_methods,                               /* tp_methods */
    type_members,                               /* tp_members */
    type_getsets,                               /* tp_getset */
    0,                                          /* tp_base */
    0,                                          /* tp_dict */
    0,                                          /* tp_descr_get */
    0,                                          /* tp_descr_set */
    offsetof(PyTypeObject, tp_dict),            /* tp_dictoffset */
    type_init,                                  /* tp_init */
    0,                                          /* tp_alloc */
    type_new,                                   /* tp_new */
    PyObject_GC_Del,                            /* tp_free */
    (inquiry)type_is_gc,                        /* tp_is_gc */
};
```

* type的类型是type，type的父类是object
* type是所有类型的类型

## PyBaseObject_Type

* object的类定义

```c
PyTypeObject PyBaseObject_Type = {
    PyVarObject_HEAD_INIT(&PyType_Type, 0)
    "object",                                   /* tp_name */
    sizeof(PyObject),                           /* tp_basicsize */
    0,                                          /* tp_itemsize */
    object_dealloc,                             /* tp_dealloc */
    0,                                          /* tp_vectorcall_offset */
    0,                                          /* tp_getattr */
    0,                                          /* tp_setattr */
    0,                                          /* tp_as_async */
    object_repr,                                /* tp_repr */
    0,                                          /* tp_as_number */
    0,                                          /* tp_as_sequence */
    0,                                          /* tp_as_mapping */
    (hashfunc)_Py_HashPointer,                  /* tp_hash */
    0,                                          /* tp_call */
    object_str,                                 /* tp_str */
    PyObject_GenericGetAttr,                    /* tp_getattro */
    PyObject_GenericSetAttr,                    /* tp_setattro */
    0,                                          /* tp_as_buffer */
    Py_TPFLAGS_DEFAULT | Py_TPFLAGS_BASETYPE,   /* tp_flags */
    object_doc,                                 /* tp_doc */
    0,                                          /* tp_traverse */
    0,                                          /* tp_clear */
    object_richcompare,                         /* tp_richcompare */
    0,                                          /* tp_weaklistoffset */
    0,                                          /* tp_iter */
    0,                                          /* tp_iternext */
    object_methods,                             /* tp_methods */
    0,                                          /* tp_members */
    object_getsets,                             /* tp_getset */
    0,                                          /* tp_base */
    0,                                          /* tp_dict */
    0,                                          /* tp_descr_get */
    0,                                          /* tp_descr_set */
    0,                                          /* tp_dictoffset */
    object_init,                                /* tp_init */
    PyType_GenericAlloc,                        /* tp_alloc */
    object_new,                                 /* tp_new */
    PyObject_Del,                               /* tp_free */
};
```

* object的类型是type
* object是所有类型的父类

## Python中的类型关键字初始化

```c
PyStatus
_PyTypes_Init(void)
{
#define INIT_TYPE(TYPE, NAME) \
    do { \
        if (PyType_Ready(TYPE) < 0) { \
            return _PyStatus_ERR("Can't initialize " NAME " type"); \
        } \
    } while (0)

    INIT_TYPE(&PyBaseObject_Type, "object");
    INIT_TYPE(&PyType_Type, "type");
    INIT_TYPE(&_PyWeakref_RefType, "weakref");
    INIT_TYPE(&_PyWeakref_CallableProxyType, "callable weakref proxy");
    INIT_TYPE(&_PyWeakref_ProxyType, "weakref proxy");
    INIT_TYPE(&PyLong_Type, "int");
    INIT_TYPE(&PyBool_Type, "bool");
    INIT_TYPE(&PyByteArray_Type, "bytearray");
    INIT_TYPE(&PyBytes_Type, "str");
    INIT_TYPE(&PyList_Type, "list");
    INIT_TYPE(&_PyNone_Type, "None");
    INIT_TYPE(&_PyNotImplemented_Type, "NotImplemented");
    INIT_TYPE(&PyTraceBack_Type, "traceback");
    INIT_TYPE(&PySuper_Type, "super");
    INIT_TYPE(&PyRange_Type, "range");
    INIT_TYPE(&PyDict_Type, "dict");
    INIT_TYPE(&PyDictKeys_Type, "dict keys");
    INIT_TYPE(&PyDictValues_Type, "dict values");
    INIT_TYPE(&PyDictItems_Type, "dict items");
    INIT_TYPE(&PyDictRevIterKey_Type, "reversed dict keys");
    INIT_TYPE(&PyDictRevIterValue_Type, "reversed dict values");
    INIT_TYPE(&PyDictRevIterItem_Type, "reversed dict items");
    INIT_TYPE(&PyODict_Type, "OrderedDict");
    INIT_TYPE(&PyODictKeys_Type, "odict_keys");
    INIT_TYPE(&PyODictItems_Type, "odict_items");
    INIT_TYPE(&PyODictValues_Type, "odict_values");
    INIT_TYPE(&PyODictIter_Type, "odict_keyiterator");
    INIT_TYPE(&PySet_Type, "set");
    INIT_TYPE(&PyUnicode_Type, "str");
    INIT_TYPE(&PySlice_Type, "slice");
    INIT_TYPE(&PyStaticMethod_Type, "static method");
    INIT_TYPE(&PyComplex_Type, "complex");
    INIT_TYPE(&PyFloat_Type, "float");
    INIT_TYPE(&PyFrozenSet_Type, "frozenset");
    INIT_TYPE(&PyProperty_Type, "property");
    INIT_TYPE(&_PyManagedBuffer_Type, "managed buffer");
    INIT_TYPE(&PyMemoryView_Type, "memoryview");
    INIT_TYPE(&PyTuple_Type, "tuple");
    INIT_TYPE(&PyEnum_Type, "enumerate");
    INIT_TYPE(&PyReversed_Type, "reversed");
    INIT_TYPE(&PyStdPrinter_Type, "StdPrinter");
    INIT_TYPE(&PyCode_Type, "code");
    INIT_TYPE(&PyFrame_Type, "frame");
    INIT_TYPE(&PyCFunction_Type, "builtin function");
    INIT_TYPE(&PyMethod_Type, "method");
    INIT_TYPE(&PyFunction_Type, "function");
    INIT_TYPE(&PyDictProxy_Type, "dict proxy");
    INIT_TYPE(&PyGen_Type, "generator");
    INIT_TYPE(&PyGetSetDescr_Type, "get-set descriptor");
    INIT_TYPE(&PyWrapperDescr_Type, "wrapper");
    INIT_TYPE(&_PyMethodWrapper_Type, "method wrapper");
    INIT_TYPE(&PyEllipsis_Type, "ellipsis");
    INIT_TYPE(&PyMemberDescr_Type, "member descriptor");
    INIT_TYPE(&_PyNamespace_Type, "namespace");
    INIT_TYPE(&PyCapsule_Type, "capsule");
    INIT_TYPE(&PyLongRangeIter_Type, "long range iterator");
    INIT_TYPE(&PyCell_Type, "cell");
    INIT_TYPE(&PyInstanceMethod_Type, "instance method");
    INIT_TYPE(&PyClassMethodDescr_Type, "class method descr");
    INIT_TYPE(&PyMethodDescr_Type, "method descr");
    INIT_TYPE(&PyCallIter_Type, "call iter");
    INIT_TYPE(&PySeqIter_Type, "sequence iterator");
    INIT_TYPE(&PyPickleBuffer_Type, "pickle.PickleBuffer");
    INIT_TYPE(&PyCoro_Type, "coroutine");
    INIT_TYPE(&_PyCoroWrapper_Type, "coroutine wrapper");
    INIT_TYPE(&_PyInterpreterID_Type, "interpreter ID");
    return _PyStatus_OK();

#undef INIT_TYPE
}
```

