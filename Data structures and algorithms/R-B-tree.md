### 红黑树
红黑树(Red-Black Tree，简称R-B Tree)，它一种特殊的二叉查找树。
红黑树是特殊的二叉查找树，意味着它满足二叉查找树的特征：任意一个节点所包含的键值，大于等于左孩子的键值，小于等于右孩子的键值。
除了具备该特性之外，红黑树还包括许多额外的信息。

红黑树的每个节点上都有存储位表示节点的颜色，颜色是红(Red)或黑(Black)。
红黑树的特性:

```ABAP
(1) 每个节点或者是黑色，或者是红色。
(2) 根节点是黑色。
(3) 每个叶子节点是黑色。 [注意：这里叶子节点，是指为空的叶子节点！]
(4) 如果一个节点是红色的，则它的子节点必须是黑色的。
(5) 对于每个节点，从该节点到其子孙节点的所有路径上包含相同数目的黑节点。
```
关于它的特性，需要注意的是：
```ABAP
第一，特性(3)中的叶子节点，是只为空(NIL或null)的节点。
第二，特性(5)，确保没有一条路径会比其他路径长出俩倍。因而，红黑树是相对是接近平衡的二叉树。
```
红黑树示意图如下：
<img src="../Data structures and algorithms/images-RB/1.png" style="zoom:67%;" />

#### 基本定义
红黑树的基本操作是**添加、删除和旋转**。在对红黑树进行添加或删除后，会用到**旋转**方法。为什么呢？道理很简单，添加或删除红黑树中的节点之后，红黑树就发生了变化，可能不满足红黑树的5条性质，也就不再是一颗红黑树了，而是一颗普通的树。而通过旋转，可以使这颗树重新成为红黑树。简单点说，旋转的目的是让树保持红黑树的特性。
旋转包括两种：左旋 和 右旋。下面分别对红黑树的基本操作进行介绍。

```c++
enum RBTColor{RED, BLACK};

template <class T>
class RBTNode{
    public:
        RBTColor color;    // 颜色
        T key;            // 关键字(键值)
        RBTNode *left;    // 左孩子
        RBTNode *right;    // 右孩子
        RBTNode *parent; // 父结点

        RBTNode(T value, RBTColor c, RBTNode *p, RBTNode *l, RBTNode *r):
            key(value),color(c),parent(),left(l),right(r) {}
};

template <class T>
class RBTree {
    private:
        RBTNode<T> *mRoot;    // 根结点

    public:
        RBTree();
        ~RBTree();

        //二叉树的遍历

        // (递归实现)查找"红黑树"中键值为key的节点
        RBTNode<T>* search(T key);
        // (非递归实现)查找"红黑树"中键值为key的节点
        RBTNode<T>* iterativeSearch(T key);

        // 查找最小结点：返回最小结点的键值。
        T minimum();
        // 查找最大结点：返回最大结点的键值。
        T maximum();

        // 找结点(x)的后继结点。即，查找"红黑树中数据值大于该结点"的"最小结点"。
        RBTNode<T>* successor(RBTNode<T> *x);
        // 找结点(x)的前驱结点。即，查找"红黑树中数据值小于该结点"的"最大结点"。
        RBTNode<T>* predecessor(RBTNode<T> *x);

        // 将结点(key为节点键值)插入到红黑树中
        void insert(T key);

        // 删除结点(key为节点键值)
        void remove(T key);

        // 销毁红黑树
        void destroy();

        // 打印红黑树
        void print();
    private:

        // (递归实现)查找"红黑树x"中键值为key的节点
        RBTNode<T>* search(RBTNode<T>* x, T key) const;
        // (非递归实现)查找"红黑树x"中键值为key的节点
        RBTNode<T>* iterativeSearch(RBTNode<T>* x, T key) const;

        // 查找最小结点：返回tree为根结点的红黑树的最小结点。
        RBTNode<T>* minimum(RBTNode<T>* tree);
        // 查找最大结点：返回tree为根结点的红黑树的最大结点。
        RBTNode<T>* maximum(RBTNode<T>* tree);

        // 左旋
        void leftRotate(RBTNode<T>* &root, RBTNode<T>* x);
        // 右旋
        void rightRotate(RBTNode<T>* &root, RBTNode<T>* y);
        // 插入函数
        void insert(RBTNode<T>* &root, RBTNode<T>* node);
        // 插入修正函数
        void insertFixUp(RBTNode<T>* &root, RBTNode<T>* node);
        // 删除函数
        void remove(RBTNode<T>* &root, RBTNode<T> *node);
        // 删除修正函数
        void removeFixUp(RBTNode<T>* &root, RBTNode<T> *node, RBTNode<T> *parent);

        // 销毁红黑树
        void destroy(RBTNode<T>* &tree);

        // 打印红黑树
        void print(RBTNode<T>* tree, T key, int direction);

#define rb_parent(r)   ((r)->parent)
#define rb_color(r) ((r)->color)
#define rb_is_red(r)   ((r)->color==RED)
#define rb_is_black(r)  ((r)->color==BLACK)
#define rb_set_black(r)  do { (r)->color = BLACK; } while (0)
#define rb_set_red(r)  do { (r)->color = RED; } while (0)
#define rb_set_parent(r,p)  do { (r)->parent = (p); } while (0)
#define rb_set_color(r,c)  do { (r)->color = (c); } while (0)
};
```
#### 左旋
<img src="../Data structures and algorithms/images-Rb/2.png" style="zoom:67%;" />
对x进行左旋，意味着"将x变成一个左节点"。

左旋的实现代码
```c++
/* 
 * 对红黑树的节点(x)进行左旋转
 *
 * 左旋示意图(对节点x进行左旋)：
 *      px                              px
 *     /                               /
 *    x                               y                
 *   /  \      --(左旋)-->           / \                #
 *  lx   y                          x  ry     
 *     /   \                       /  \
 *    ly   ry                     lx  ly  
 *
 *
 */
template <class T>
void RBTree<T>::leftRotate(RBTNode<T>* &root, RBTNode<T>* x)
{
    // 设置x的右孩子为y
    RBTNode<T> *y = x->right;

    // 将 “y的左孩子” 设为 “x的右孩子”；
    // 如果y的左孩子非空，将 “x” 设为 “y的左孩子的父亲”
    x->right = y->left;
    if (y->left != NULL)
        y->left->parent = x;

    // 将 “x的父亲” 设为 “y的父亲”
    y->parent = x->parent;

    if (x->parent == NULL)
    {
        root = y;            // 如果 “x的父亲” 是空节点，则将y设为根节点
    }
    else
    {
        if (x->parent->left == x)
            x->parent->left = y;    // 如果 x是它父节点的左孩子，则将y设为“x的父节点的左孩子”
        else
            x->parent->right = y;    // 如果 x是它父节点的左孩子，则将y设为“x的父节点的左孩子”
    }
    
    // 将 “x” 设为 “y的左孩子”
    y->left = x;
    // 将 “x的父节点” 设为 “y”
    x->parent = y;
}
```
理解左旋之后，看看下面一个更鲜明的例子

<img src="../Data structures and algorithms/images-RB/3.png" style="zoom:67%;" />

#### 右旋

<img src="../Data structures and algorithms/images-RB/4.png" style="zoom:67%;" />
对y进行左旋，意味着"将y变成一个右节点"。

右旋的实现代码
```c++
/* 
 * 对红黑树的节点(y)进行右旋转
 *
 * 右旋示意图(对节点y进行左旋)：
 *            py                               py
 *           /                                /
 *          y                                x                  
 *         /  \      --(右旋)-->            /  \                     #
 *        x   ry                           lx   y  
 *       / \                                   / \                   #
 *      lx  rx                                rx  ry
 * 
 */
template <class T>
void RBTree<T>::rightRotate(RBTNode<T>* &root, RBTNode<T>* y)
{
    // 设置x是当前节点的左孩子。
    RBTNode<T> *x = y->left;

    // 将 “x的右孩子” 设为 “y的左孩子”；
    // 如果"x的右孩子"不为空的话，将 “y” 设为 “x的右孩子的父亲”
    y->left = x->right;
    if (x->right != NULL)
        x->right->parent = y;

    // 将 “y的父亲” 设为 “x的父亲”
    x->parent = y->parent;

    if (y->parent == NULL) 
    {
        root = x;            // 如果 “y的父亲” 是空节点，则将x设为根节点
    }
    else
    {
        if (y == y->parent->right)
            y->parent->right = x;    // 如果 y是它父节点的右孩子，则将x设为“y的父节点的右孩子”
        else
            y->parent->left = x;    // (y是它父节点的左孩子) 将x设为“x的父节点的左孩子”
    }

    // 将 “y” 设为 “x的右孩子”
    x->right = y;

    // 将 “y的父节点” 设为 “x”
    y->parent = x;
}
```
理解右旋之后，看看下面一个更鲜明的例子

<img src="../Data structures and algorithms/images-RB/5.png" style="zoom:67%;" />

#### 区分 左旋 和 右旋
仔细观察上面"左旋"和"右旋"的示意图。我们能清晰的发现，它们是对称的。无论是左旋还是右旋，被旋转的树，在旋转前是二叉查找树，并且旋转之后仍然是一颗二叉查找树。

<img src="../Data structures and algorithms/images-RB/6.png" style="zoom:67%;" />

##### 左旋示例图(以x为节点进行左旋)：
```c++
                               z
   x                          /                  
  / \      --(左旋)-->       x
 y   z                      /
                           y
```
对x进行左旋，意味着，将“x的右孩子”设为“x的父亲节点”；即，将 x变成了一个左节点(x成了为z的左孩子)！。 因此，**左旋中的“左”，意味着“被旋转的节点将变成一个左节点”**。
##### 右旋示例图(以x为节点进行右旋)：
```c++
                               y
   x                            \                 
  / \      --(右旋)-->           x
 y   z                            \
                                   z
```
对x进行右旋，意味着，将“x的左孩子”设为“x的父亲节点”；即，将 x变成了一个右节点(x成了为y的右孩子)！ 因此，**右旋中的“右”，意味着“被旋转的节点将变成一个右节点”**。
#### 红黑树的添加
将一个节点插入到红黑树中，需要执行哪些步骤呢？**首先，将红黑树当作一颗二叉查找树，将节点插入；然后，将节点着色为红色；最后，通过旋转和重新着色等方法来修正该树，使之重新成为一颗红黑树**。详细描述如下：

**第一步: 将红黑树当作一颗二叉查找树，将节点插入**。
```ABAP
红黑树本身就是一颗二叉查找树，将节点插入后，该树仍然是一颗二叉查找树。也就意味着，
树的键值仍然是有序的。此外，无论是左旋还是右旋，若旋转之前这棵树是二叉查找树，旋转之后它一定还是二叉查找树。
这也就意味着，"任何的旋转和重新着色操作，都不会改变它仍然是一颗二叉查找树的事实"。

那接下来，想方设法的旋转以及重新着色，使这颗树重新成为红黑树！
```
**第二步：将插入的节点着色为"红色"。**

为什么着色成红色，而不是黑色呢？为什么呢？在回答之前，我们需要重新温习一下红黑树的特性：
```ABAP
(1) 每个节点或者是黑色，或者是红色。
(2) 根节点是黑色。
(3) 每个叶子节点是黑色。 [注意：这里叶子节点，是指为空的叶子节点！]
(4) 如果一个节点是红色的，则它的子节点必须是黑色的。
"(5) 从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点"。

将插入的节点着色为红色，不会违背"特性(5)"！少违背一条特性，就意味着我们需要处理的情况越少。
接下来，让这棵树满足其它性质即可；满足了的话，它就又是一颗红黑树了
```
**第三步: 通过一系列的旋转或着色等操作，使之重新成为一颗红黑树。**
```ABAP
第二步中，将插入节点着色为"红色"之后，不会违背"特性(5)"。那它到底"会违背哪些特性呢？"
对于"特性(1)"，显然不会违背了。因为我们已经将它涂成红色了。
对于"特性(2)"，显然也不会违背。在第一步中，我们是将红黑树当作二叉查找树，然后执行的插入操作。
而根据二叉查找数的特点，"插入操作不会改变根节点。所以，根节点仍然是黑色"。
对于"特性(3)"，显然不会违背了。这里的"叶子节点是指的空叶子节点，插入非空节点并不会对它们造成影响"。
对于"特性(4)"，是有可能违背的！
那么想办法使之"满足特性(4)"，就可以将树重新构造成红黑树了。
```
##### 代码实现
```c++
/* 
 * 将结点插入到红黑树中
 *
 * 参数说明：
 *     root 红黑树的根结点
 *     node 插入的结点        // 对应《算法导论》中的node
 */
template <class T>
void RBTree<T>::insert(RBTNode<T>* &root, RBTNode<T>* node)
{
    RBTNode<T> *y = NULL;
    RBTNode<T> *x = root;

    // 1. 将红黑树当作一颗二叉查找树，将节点添加到二叉查找树中。
    while (x != NULL)
    {
        y = x;
        if (node->key < x->key)
            x = x->left;
        else
            x = x->right;
    }

    node->parent = y;
    if (y!=NULL)
    {
        if (node->key < y->key)
            y->left = node;
        else
            y->right = node;
    }
    else
        root = node;

    // 2. 设置节点的颜色为红色
    node->color = RED;

    // 3. 将它重新修正为一颗二叉查找树
    insertFixUp(root, node);
}
```
**添加修正操作`insertFixUp`**

```c++
/*
 * 红黑树插入修正函数
 *
 * 在向红黑树中插入节点之后(失去平衡)，再调用该函数；
 * 目的是将它重新塑造成一颗红黑树。
 *
 * 参数说明：
 *     root 红黑树的根
 *     node 插入的结点        // 对应《算法导论》中的z
 */
template <class T>
void RBTree<T>::insertFixUp(RBTNode<T>* &root, RBTNode<T>* node)
{
    RBTNode<T> *parent, *gparent;

    // 若“父节点存在，并且父节点的颜色是红色”
    while ((parent = rb_parent(node)) && rb_is_red(parent))
    {
        gparent = rb_parent(parent);

        //若“父节点”是“祖父节点的左孩子”
        if (parent == gparent->left)
        {
            // Case 1条件：叔叔节点是红色
            {
                RBTNode<T> *uncle = gparent->right;
                if (uncle && rb_is_red(uncle))
                {
                    rb_set_black(uncle);
                    rb_set_black(parent);
                    rb_set_red(gparent);
                    node = gparent;
                    continue;
                }
            }

            // Case 2条件：叔叔是黑色，且当前节点是右孩子
            if (parent->right == node)
            {
                RBTNode<T> *tmp;
                leftRotate(root, parent);
                tmp = parent;
                parent = node;
                node = tmp;
            }

            // Case 3条件：叔叔是黑色，且当前节点是左孩子。
            rb_set_black(parent);
            rb_set_red(gparent);
            rightRotate(root, gparent);
        } 
        else//若“z的父节点”是“z的祖父节点的右孩子”
        {
            // Case 1条件：叔叔节点是红色
            {
                RBTNode<T> *uncle = gparent->left;
                if (uncle && rb_is_red(uncle))
                {
                    rb_set_black(uncle);
                    rb_set_black(parent);
                    rb_set_red(gparent);
                    node = gparent;
                    continue;
                }
            }

            // Case 2条件：叔叔是黑色，且当前节点是左孩子
            if (parent->left == node)
            {
                RBTNode<T> *tmp;
                rightRotate(root, parent);
                tmp = parent;
                parent = node;
                node = tmp;
            }

            // Case 3条件：叔叔是黑色，且当前节点是右孩子。
            rb_set_black(parent);
            rb_set_red(gparent);
            leftRotate(root, gparent);
        }
    }

    // 将根节点设为黑色
    rb_set_black(root);
}
```
##### 插入策略
**插入情景**

<img src="../Data structures and algorithms/images-RB/1.jpg" style="zoom:67%;" />
根据被插入节点的父节点的情况，可以将"当节点z被着色为红色节点，并插入二叉树"划分为三种情况来处理。

```ABAP
1. 情况说明：被插入的节点是根节点。
   处理方法：直接把此节点涂为黑色。
2. 情况说明：被插入的节点的父节点是黑色。
   处理方法：什么也不需要做。节点被插入后，仍然是红黑树。
3. 情况说明：被插入的节点的父节点是红色。
   处理方法：那么，该情况与红黑树的“特性(4)”相冲突。这种情况下，被插入节点是
   一定存在非空祖父节点的；进一步的讲，"被插入节点也一定存在叔叔节点(即使叔叔节点为空，
   我们也视之为存在，空节点本身就是黑色节点")。理解这点之后，我们依据"叔叔节点的情况"，
   将这种情况进一步划分为3种情况(Case)。
```

| --     | 现象说明                                                     | 处理策略                                                     |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Case 1 | 当前节点的父节点是红色，且当前节点的祖父节点的另一个子节点（叔叔节点）也是红色。 | (01) 将“父节点”设为黑色。(02) 将“叔叔节点”设为黑色。(03) 将“祖父节点”设为“红色”。(04) 将“祖父节点”设为“当前节点”(红色节点)；即，之后继续对“当前节点”进行操作。 |
| Case 2 | 当前节点的父节点是红色，叔叔节点是黑色，且当前节点是其父节点的右孩子 | (01) 将“父节点”作为“新的当前节点”。(02) 以“新的当前节点”为支点进行左旋。 |
| Case 3 | 当前节点的父节点是红色，叔叔节点是黑色，且当前节点是其父节点的左孩子 | (01) 将“父节点”设为“黑色”。(02) 将“祖父节点”设为“红色”。(03) 以“祖父节点”为支点进行右旋。 |
上面三种情况(Case)处理问题的**核心思路都是：将红色的节点移到根节点；然后，将根节点设为黑色**
###### (Case 1)叔叔是红色
- 现象说明

当前节点(即，被插入节点)的父节点是红色，且当前节点的祖父节点的另一个子节点（叔叔节点）也是红色。
- 处理策略
    - 将“父节点”设为黑色。
    - 将“叔叔节点”设为黑色。
    - 将“祖父节点”设为“红色”。
    - 将“祖父节点”设为“当前节点”(红色节点)；即，之后继续对“当前节点”进行操作。

**为什么要这样处理。(通过下面的图进行对比)**
```ABAP
"当前节点"和"父节点"都是红色，违背"特性(4)"。所以，将"父节点"设置"黑色"以解决这个问题。
但是，将"父节点"由"红色"变成"黑色"之后，违背了"特性(5)"：因为，包含
"父节点"的分支的黑色节点的总数增加了1。  解决这个问题的办法是：将"祖父节点"由"黑色"变成红色，
同时，将"叔叔节点"由"红色"变成"黑色"。
关于这里，说明几点：
第一，为什么"祖父节点"之前是黑色？这个应该很容易想明白，因为在变换操作之前，该树是红黑树，
"父节点"是红色，那么"祖父节点"一定是黑色。
第二，为什么将"祖父节点"由"黑色"变成红色，同时，将"叔叔节点"由"红色"变成"黑色"；
能解决"包含'父节点'的分支的黑色节点的总数增加了1"的问题。这个道理也很简单。
"包含‘父节点’的分支的黑色节点的总数增加了1"同时也意味着 "包含‘祖父节点’的分支的黑色节点的总数增加了1"，
既然这样，我们通过将"祖父节点"由"黑色"变成"红色"以解决"包含‘祖父节点’的分支的黑色节点的总数增加了1"的问题；
但是，这样处理之后又会引起另一个问题"包含‘叔叔’节点的分支的黑色节点的总数减少了1"，
现在我们已知"叔叔节点"是"红色"，将"叔叔节点"设为"黑色"就能解决这个问题。 
所以，将"祖父节点"由"黑色"变成红色，同时，将"叔叔节点"由"红色"变成"黑色"；就解决了该问题。
按照上面的步骤处理之后：当前节点、父节点、叔叔节点之间都不会违背红黑树特性，但祖父节点却不一定。
若此时，祖父节点是根节点，直接将祖父节点设为"黑色"，那就完全解决这个问题了；
若祖父节点不是根节点，那我们需要将"祖父节点"设为"新的当前节点"，接着对"新的当前节点"进行分析。
```
红黑树由之前的：

<img src="../Data structures and algorithms/images-RB/7.png" style="zoom:67%;" />
变化后如下图所示：

<img src="../Data structures and algorithms/images-RB/8.png" style="zoom:67%;" />
**于是，Case1转换成了Case2**

###### (Case 2)叔叔是黑色，且当前节点是右孩子
- 现象说明
当前节点(即，被插入节点)的父节点是红色，叔叔节点是黑色，且当前节点是其父节点的右孩子
- 处理策略
```ABAP
(01)将“父节点”作为“新的当前节点”。
(02)以“新的当前节点”为支点进行左旋。
```
**为什么要这样处理**。(通过下面的图进行对比)
```ABAP
首先，将"父节点"作为"新的当前节点"；接着，以"新的当前节点"为支点进行左旋。 为了便于理解，
我们先说明第(02)步，再说明第(01)步；为了便于说明，我们设置"父节点"的代号为F(Father)，
"当前节点"的代号为S(Son)。
为什么要"以F为支点进行左旋"呢？根据已知条件可知：S是F的右孩子。而之前我们说过，
我们处理红黑树的核心思想：将红色的节点移到根节点；然后，将根节点设为黑色。既然是"将红色的节点移到根节点"，
那就是说要不断的将破坏红黑树特性的红色节点上移(即向根方向移动)。 而S又是一个右孩子，因此，我们可以通过"左旋"来将S上移！
按照上面的步骤(以F为支点进行左旋)处理之后：若S变成了根节点，那么直接将其设为"黑色"，就完全解决问题了；
若S不是根节点，那我们需要执行步骤(01)，即"将F设为‘新的当前节点’"。那为什么不继续以S为新的当前节点继续处理，
而需要以F为新的当前节点来进行处理呢？这是因为"左旋"之后，F变成了S的"子节点"，
即S变成了F的父节点；而我们处理问题的时候，需要从下至上(由叶到根)方向进行处理；也就是说，
必须先解决"孩子"的问题，再解决"父亲"的问题；所以，我们执行步骤(01)：将"父节点"作为"新的当前节点"。
```
所以红黑树由之前的：

<img src="../Data structures and algorithms/images-RB/9.png" style="zoom:67%;" />
变化成：

<img src="../Data structures and algorithms/images-RB/10.png" style="zoom:67%;" />
**从而Case2转换成了Case3**

###### (Case 3)叔叔是黑色，且当前节点是左孩子
- 现象说明
当前节点(即，被插入节点)的父节点是红色，叔叔节点是黑色，且当前节点是其父节点的左孩子

- 处理策略
```ABAP
(01) 将“父节点”设为“黑色”。
(02) 将“祖父节点”设为“红色”。
(03) 以“祖父节点”为支点进行右旋。
```
**下面谈谈为什么要这样处理**。(通过下面的图进行对比)
```ABAP
为了便于说明，我们设置"当前节点"为S(Original Son)，"兄弟节点"为B(Brother)，"叔叔节点"为U(Uncle)，
"父节点"为F(Father)，祖父节点为G(Grand-Father)。
S和F都是红色，违背了红黑树的"特性(4)"，我们可以将F由"红色"变为"黑色"，就解决了"违背‘特性(4)’"的问题；
但却引起了其它问题：违背特性(5)，因为将F由红色改为黑色之后，所有经过F的分支的黑色节点的
个数增加了1。那我们如何解决"所有经过F的分支的黑色节点的个数增加了1"的问题呢？
我们可以通过"将G由黑色变成红色"，同时"以G为支点进行右旋"来解决。
```
最后，把根结点涂为黑色，整棵红黑树便重新恢复了平衡。所以红黑树由之前的：

<img src="../Data structures and algorithms/images-RB/11.png" style="zoom:67%;" />
变化成：

<img src="../Data structures and algorithms/images-RB/12.png" style="zoom:67%;" />