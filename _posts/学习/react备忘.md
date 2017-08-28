1. 两个相同组件产生类似的DOM结构，不同的组件产生不同的DOM结构；
    1. React认为：通过同一个ReactComponet构造器产生的组件实例，将会产生类似的DOM结构，即使我们通过代码将实际产生的DOM编写地完全不同，它也认为它们相似，这两个Virtual DOM的状态变化将被列入Diff算法中进行比较。
    2. React认为：通过不同的ReactComponent构造器产生的组件实例，它们产生的DOM完全不相似，即使这两个组件的源代码完全一样，它也认为它们不同。这两者的状态变化将不被列入Diff算法中比较，直接销毁状态前的DOM再创建新的DOM。
    3. React通过比较组件构造器的指针，可以很方便的指出两个组件是否是相似的。
2. 对于同一层次的一组子节点，它们可以通过唯一的id进行区分。
    1. React采用全局刷新的思路，即无关乎状态前后的DOM是否有关系。
    2. React允许动态生成Virtual DOM，但它指出，需要分配给每个Virtual DOM一个ID，这个ID帮助它更有效率地操作DOM。
    3. 若不给Virtual DOM指定ID，则React认为状态改变前后所有的DOM都无关，并且将全部销毁所有DOM再创建新的DOM。
3. React对待HTML元素和ReactComponent完全一致。

