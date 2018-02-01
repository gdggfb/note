## 红黑树
### 初衷
+ 网上的博客很多，然而我觉得根据前辈们的博哥是无法完全理解这个树的运作规则的，因为总有些细节问题，前辈们觉得不够重要并没把它们写出来，所以我自己抽取了java里的一个红黑树的代码实现去赏析。
+ 红黑树的定义是唯一的，写法可以有千万种，但我感觉精简后的写法应该都是形式相似的，就如下面的代码。
### 红黑树介绍
红黑树是一种近似平衡的二叉查找树，左右子树的高度差不会超过二者中较低者的一倍。
因为红黑树能保证分布的均匀，所以查找的稳定性比较好，又因为是二分查找，所以查找的时间消耗会随每次总数加一倍而加一。
### 红黑树的定义
1. 每个节点要么是红色，要么是黑色。
2. 根节点必须是黑色
3. 红色节点不能连续（也即是，红色节点的孩子和父亲都不能是红色）。
4. 对于每个节点，从该点至null（树尾端）的任何路径，都含有相同个数的黑色节点。
### 关于代码实现的理解
+ 为了满足定义3，发现父节点为红色就要平衡操作
+ 如果叔叔也为红，则不需要旋转，直接涂色即可，然后把冲突向爷爷转移
+ 如果叔叔为黑色，这导致不满足定义4，需要旋转以消除兄弟间颜色不统一的情况，然后将冲突向父亲转移
+ 多次平衡和转移后，会有到达root或满足父节点为黑的情况，这是平衡结束的标志
+ 因为插入的节点都是红色，所以向根方向转移时，遇到黑色就是到达一个小生态边界的标志
+ 删除节点会复杂很多，很多种树的形态都要做特殊处理。
+ 删除时如果替代节点是红色的，那么操作将是快速的，不用进入迭代平衡的，因为替代的节点可以直接移除而不破坏定义
+ 删除时，如果替代节点是黑色的，那么操作将是复杂的，因为平衡操作总是有可能破坏兄弟节点的平衡
+ 兄弟节点的操作会引起一系列的新的平衡操作，就像连锁反应，蝴蝶效应。
### treeMap里的单线程的红黑树的写法似乎别有一番风味，先沾到这里，日后再赏
```java
private void fixAfterInsertion(Entry<K,V> x) {
    x.color = RED;

    while (x != null && x != root && x.parent.color == RED) {
        if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
            Entry<K,V> y = rightOf(parentOf(parentOf(x)));
            if (colorOf(y) == RED) {
                setColor(parentOf(x), BLACK);
                setColor(y, BLACK);
                setColor(parentOf(parentOf(x)), RED);
                x = parentOf(parentOf(x));
            } else {
                if (x == rightOf(parentOf(x))) {
                    x = parentOf(x);
                    rotateLeft(x);
                }
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                rotateRight(parentOf(parentOf(x)));
            }
        } else {
            Entry<K,V> y = leftOf(parentOf(parentOf(x)));
            if (colorOf(y) == RED) {
                setColor(parentOf(x), BLACK);
                setColor(y, BLACK);
                setColor(parentOf(parentOf(x)), RED);
                x = parentOf(parentOf(x));
            } else {
                if (x == leftOf(parentOf(x))) {
                    x = parentOf(x);
                    rotateRight(x);
                }
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                rotateLeft(parentOf(parentOf(x)));
            }
        }
    }
    root.color = BLACK;
}

private void fixAfterDeletion(Entry<K,V> x) {
    while (x != root && colorOf(x) == BLACK) {
        if (x == leftOf(parentOf(x))) {
            Entry<K,V> sib = rightOf(parentOf(x));

            if (colorOf(sib) == RED) {
                setColor(sib, BLACK);
                setColor(parentOf(x), RED);
                rotateLeft(parentOf(x));
                sib = rightOf(parentOf(x));
            }

            if (colorOf(leftOf(sib))  == BLACK &&
                colorOf(rightOf(sib)) == BLACK) {
                setColor(sib, RED);
                x = parentOf(x);
            } else {
                if (colorOf(rightOf(sib)) == BLACK) {
                    setColor(leftOf(sib), BLACK);
                    setColor(sib, RED);
                    rotateRight(sib);
                    sib = rightOf(parentOf(x));
                }
                setColor(sib, colorOf(parentOf(x)));
                setColor(parentOf(x), BLACK);
                setColor(rightOf(sib), BLACK);
                rotateLeft(parentOf(x));
                x = root;
            }
        } else { // symmetric
            Entry<K,V> sib = leftOf(parentOf(x));

            if (colorOf(sib) == RED) {
                setColor(sib, BLACK);
                setColor(parentOf(x), RED);
                rotateRight(parentOf(x));
                sib = leftOf(parentOf(x));
            }

            if (colorOf(rightOf(sib)) == BLACK &&
                colorOf(leftOf(sib)) == BLACK) {
                setColor(sib, RED);
                x = parentOf(x);
            } else {
                if (colorOf(leftOf(sib)) == BLACK) {
                    setColor(rightOf(sib), BLACK);
                    setColor(sib, RED);
                    rotateLeft(sib);
                    sib = leftOf(parentOf(x));
                }
                setColor(sib, colorOf(parentOf(x)));
                setColor(parentOf(x), BLACK);
                setColor(leftOf(sib), BLACK);
                rotateRight(parentOf(x));
                x = root;
            }
        }
    }
    setColor(x, BLACK);
}
private static <K,V> boolean colorOf(Entry<K,V> p) {
    return (p == null ? BLACK : p.color);
}
private static <K,V> Entry<K,V> parentOf(Entry<K,V> p) {
    return (p == null ? null: p.parent);
}
private static <K,V> void setColor(Entry<K,V> p, boolean c) {
    if (p != null)
        p.color = c;
}
private static <K,V> Entry<K,V> leftOf(Entry<K,V> p) {
    return (p == null) ? null: p.left;
}
private static <K,V> Entry<K,V> rightOf(Entry<K,V> p) {
    return (p == null) ? null: p.right;
}
/** From CLR */
private void rotateLeft(Entry<K,V> p) {
    if (p != null) {
        Entry<K,V> r = p.right;
        p.right = r.left;
        if (r.left != null)
            r.left.parent = p;
        r.parent = p.parent;
        if (p.parent == null)
            root = r;
        else if (p.parent.left == p)
            p.parent.left = r;
        else
            p.parent.right = r;
        r.left = p;
        p.parent = r;
    }
}
/** From CLR */
private void rotateRight(Entry<K,V> p) {
    if (p != null) {
        Entry<K,V> l = p.left;
        p.left = l.right;
        if (l.right != null) l.right.parent = p;
        l.parent = p.parent;
        if (p.parent == null)
            root = l;
        else if (p.parent.right == p)
            p.parent.right = l;
        else p.parent.left = l;
        l.right = p;
        p.parent = l;
    }
}
```
### 红黑树代码实现，取自java8的ConcurrentHashMap的反编译结果
```java
/*    
* 向树里插入一个新node
* h 新node的哈希值
* k node的key
* v node的value
*/  
final TreeNode<K,V> putTreeVal(int h, K k, V v) {
    Class<?> kc = null;
    boolean searched = false;
    for (TreeNode<K,V> p = root;;) {
        // 第一次循环，p 为 root节点
        // dir 1 新节点要插入p的右分支中； -1 新节点要插入p的左分支中
        int dir, ph; K pk;
        // 情况1，这是一个空树，直接插入，TreeNode默认为黑色，结束
        if (p == null) {
            first = root = new TreeNode<K,V>(h, k, v, null, null);
            break;
        }
        else if ((ph = p.hash) > h)
            dir = -1;
        else if (ph < h)
            dir = 1;
        // 情况2，要插入的节点已经存在，直接将这个节点返回，调用这个方法的方法会给这个返回的p的value赋值
        else if ((pk = p.key) == k || (pk != null && k.equals(pk)))
            return p;
        // key 实现了比较器的情况，这里不深究
        else if ((kc == null &&
                    (kc = comparableClassFor(k)) == null) ||
                    (dir = compareComparables(kc, k, pk)) == 0) {
            if (!searched) {
                TreeNode<K,V> q, ch;
                searched = true;
                if (((ch = p.left) != null &&
                        (q = ch.findTreeNode(h, k, kc)) != null) ||
                    ((ch = p.right) != null &&
                        (q = ch.findTreeNode(h, k, kc)) != null))
                    return q;
            }
            dir = tieBreakOrder(k, pk);
        }
 
        TreeNode<K,V> xp = p;
        if ((p = (dir <= 0) ? p.left : p.right) == null) {
            // 如果找到了叶子节点，则将新节点插入
            TreeNode<K,V> x, f = first;
            first = x = new TreeNode<K,V>(h, k, v, f, xp);
            if (f != null)
                f.prev = x;
            // 根据dir将新节点加入左右孩
            if (dir <= 0)
                xp.left = x;
            else
                xp.right = x;
            if (!xp.red)
            // 情况3，父节点为黑，就将新节点染红，结束
                x.red = true;
            else {
            // 如果父节点为红，就进入插入平衡函数
                lockRoot();
                try {
                    root = balanceInsertion(root, x);
                } finally {
                    unlockRoot();
                }
            }
            break;
        }
    }
    return null;
}

final boolean removeTreeNode(TreeNode<K,V> p) {
    TreeNode<K,V> next = (TreeNode<K,V>)p.next;
    TreeNode<K,V> pred = p.prev;  // unlink traversal pointers
    TreeNode<K,V> r, rl;
    if (pred == null)
        first = next;
    else
        pred.next = next;
    if (next != null)
        next.prev = pred;
    if (first == null) {
        root = null;
        return true;
    }
    if ((r = root) == null || r.right == null || // too small
        (rl = r.left) == null || rl.left == null)
        return true;
    lockRoot();
    try {
        TreeNode<K,V> replacement;
        TreeNode<K,V> pl = p.left;
        TreeNode<K,V> pr = p.right;
        if (pl != null && pr != null) {
            // 大小儿子都存在
            TreeNode<K,V> s = pr, sl;
            // 找到大于p的最小节点s
            while ((sl = s.left) != null) // find successor
                s = sl;
            // s和p交换颜色，然后交换位置，等于颜色未变，值变
            boolean c = s.red; s.red = p.red; p.red = c; // swap colors
            TreeNode<K,V> sr = s.right;
            TreeNode<K,V> pp = p.parent;
            if (s == pr) { // p was s's direct parent
                p.parent = s;
                s.right = p;
            }
            else {
                TreeNode<K,V> sp = s.parent;
                if ((p.parent = sp) != null) {
                    if (s == sp.left)
                        sp.left = p;
                    else
                        sp.right = p;
                }
                if ((s.right = pr) != null)
                    pr.parent = s;
            }
            p.left = null;
            if ((p.right = sr) != null)
                sr.parent = p;
            if ((s.left = pl) != null)
                pl.parent = s;
            if ((s.parent = pp) == null)
                r = s;
            else if (p == pp.left)
                pp.left = s;
            else
                pp.right = s;
            if (sr != null)
                replacement = sr;
            else
                replacement = p;
        }
        else if (pl != null)
            // 只有小儿子存在
            replacement = pl;
        else if (pr != null)
            // 只有大儿子存在
            replacement = pr;
        else
            // 大小儿子都不存在
            replacement = p;
        if (replacement != p) {
            // 有儿子存在时
            TreeNode<K,V> pp = replacement.parent = p.parent;
            if (pp == null)
                r = replacement;
            else if (p == pp.left)
                pp.left = replacement;
            else
                pp.right = replacement;
            p.left = p.right = p.parent = null;
        }

        root = (p.red) ? r : balanceDeletion(r, replacement);

        if (p == replacement) {  // detach pointers
            TreeNode<K,V> pp;
            if ((pp = p.parent) != null) {
                if (p == pp.left)
                    pp.left = null;
                else if (p == pp.right)
                    pp.right = null;
                p.parent = null;
            }
        }
    } finally {
        unlockRoot();
    }
    return false;
}

static <K,V> TreeNode<K,V> rotateLeft(TreeNode<K,V> root,
                                        TreeNode<K,V> p) {
    TreeNode<K,V> r, pp, rl;
    if (p != null && (r = p.right) != null) {
        // 左旋：将大儿子变成自己的父亲
        if ((rl = p.right = r.left) != null)
            rl.parent = p;
        if ((pp = r.parent = p.parent) == null)
            (root = r).red = false;
        else if (pp.left == p)
            pp.left = r;
        else
            pp.right = r;
        r.left = p;
        p.parent = r;
    }
    return root;
}

static <K,V> TreeNode<K,V> rotateRight(TreeNode<K,V> root,
                                        TreeNode<K,V> p) {
    TreeNode<K,V> l, pp, lr;
    if (p != null && (l = p.left) != null) {
        // 右旋：将小儿子变成自己的父亲
        if ((lr = p.left = l.right) != null)
            lr.parent = p;
        if ((pp = l.parent = p.parent) == null)
            (root = l).red = false;
        else if (pp.right == p)
            pp.right = l;
        else
            pp.left = l;
        l.right = p;
        p.parent = l;
    }
    return root;
}

static <K,V> TreeNode<K,V> balanceInsertion(TreeNode<K,V> root,
                                            TreeNode<K,V> x) {
    // 节点染红
    x.red = true;
    for (TreeNode<K,V> xp, xpp, xppl, xppr;;) {
        // xp 父亲
        // xpp 爷爷
        // xppl 小叔
        // xppr 大叔
        
        if ((xp = x.parent) == null) {
            //情况4 自己是root节点，染黑，结束
            x.red = false;
            return x;
        }
        else if (!xp.red || (xpp = xp.parent) == null)
            // 父节点不为空下：父节点为黑，或者父节点为root
            //情况5 父节点存在且为黑，结束
            //情况6 父节点为root节点，结束
            return root;

        // 父亲不是root，父节点存在且为红色，自己不是root
        if (xp == (xppl = xpp.left)) {
            // 父亲是小叔
            if ((xppr = xpp.right) != null && xppr.red) {
                // 情况7 自己不是root，父亲不是root，父节点存在且为红色，大叔存在且为红色，
                // 操作：将大叔染黑，父亲染黑，爷爷染红，将爷爷设为当前节点，继续下一次平衡。
                xppr.red = false;
                xp.red = false;
                xpp.red = true;
                x = xpp;
            }
            else {
                // 大叔为空，或者大叔为黑色
                if (x == xp.right) {
                    // 情况8 自己不是root，父亲不是root，父节点存在且为红色，大叔不存在或为黑，自己是父亲的大儿子。
                    // 情况8-操作1：以自己的父亲为旋点进行左旋，将自己变成父亲的父亲。
                    root = rotateLeft(root, x = xp);
                    xpp = (xp = x.parent) == null ? null : xp.parent;
                }
                if (xp != null) {
                    // 情况8-操作2：以自己的父亲为旋点进行左旋，将自己变成父亲的父亲。此时自己和父亲都是红色，把父亲染黑，如果爷爷存在，将爷爷染红，再以爷爷为旋点进行右旋，因为大叔如果存在则为黑色。
                    xp.red = false;
                    if (xpp != null) {
                        xpp.red = true;
                        root = rotateRight(root, xpp);
                    }
                }
            }
        }
        else {
            // 父亲是大叔
            if (xppl != null && xppl.red) {
                // 情况9 自己不是root，父亲不是root，父节点存在且为红色，小叔存在且为红色，父亲是大叔
                // 操作：将小叔染黑，父亲染黑，爷爷染红，将爷爷设为当前节点，继续下一次平衡。
                xppl.red = false;
                xp.red = false;
                xpp.red = true;
                x = xpp;
            }
            else {
                // 情况10 自己不是root，父亲不是root，父节点存在且为红色，小叔不存在或小叔为黑色
                // 和操作8差不多
                if (x == xp.left) {
                    root = rotateRight(root, x = xp);
                    xpp = (xp = x.parent) == null ? null : xp.parent;
                }
                if (xp != null) {
                    xp.red = false;
                    if (xpp != null) {
                        xpp.red = true;
                        root = rotateLeft(root, xpp);
                    }
                }
            }
        }
    }
}

static <K,V> TreeNode<K,V> balanceDeletion(TreeNode<K,V> root,
                                            TreeNode<K,V> x) {
    for (TreeNode<K,V> xp, xpl, xpr;;)  {
        if (x == null || x == root)
            return root;
        else if ((xp = x.parent) == null) {
            x.red = false;
            return x;
        }
        else if (x.red) {
            x.red = false;
            return root;
        }
        else if ((xpl = xp.left) == x) {
            // x 为黑色，x为小儿子
            if ((xpr = xp.right) != null && xpr.red) {
                // 哥哥存在且为红色
                xpr.red = false;
                xp.red = true;
                root = rotateLeft(root, xp);
                xpr = (xp = x.parent) == null ? null : xp.right;
            }
            if (xpr == null)
            // 哥哥不存在，冲突转移给爸爸
                x = xp;
            else {
                // 哥哥存在
                TreeNode<K,V> sl = xpr.left, sr = xpr.right;
                if ((sr == null || !sr.red) &&
                    (sl == null || !sl.red)) {
                    // sl sr 为空或者为黑色时
                    // 哥哥的生态本来就是平衡的，要么都为空，要么都为黑色，冲突转移给爸爸
                    // 哥哥置红，以至于不影响弟弟的黑色个数
                    xpr.red = true;
                    x = xp;
                } else {
                    // sl sr 至少有一个存在且颜色为红
                    // 两个都存在，颜色有一个为红
                    if (sr == null || !sr.red) {
                        // 大侄 不存在， 或等于黑
                        if (sl != null)
                            // 如果小侄存在，置黑
                            sl.red = false;
                        // 兄弟置红
                        xpr.red = true;
                        // 右旋兄弟节点
                        root = rotateRight(root, xpr);
                        xpr = (xp = x.parent) == null ?
                            null : xp.right;
                    }
                    if (xpr != null) {
                        xpr.red = (xp == null) ? false : xp.red;
                        if ((sr = xpr.right) != null)
                            sr.red = false;
                    }
                    if (xp != null) {
                        xp.red = false;
                        root = rotateLeft(root, xp);
                    }
                    x = root;
                }
            }
        }
        else { // symmetric
             // x 为黑色，x为大儿子
            if (xpl != null && xpl.red) {
                xpl.red = false;
                xp.red = true;
                root = rotateRight(root, xp);
                xpl = (xp = x.parent) == null ? null : xp.left;
            }
            if (xpl == null)
                x = xp;
            else {
                TreeNode<K,V> sl = xpl.left, sr = xpl.right;
                if ((sl == null || !sl.red) &&
                    (sr == null || !sr.red)) {
                    xpl.red = true;
                    x = xp;
                }
                else {
                    if (sl == null || !sl.red) {
                        if (sr != null)
                            sr.red = false;
                        xpl.red = true;
                        root = rotateLeft(root, xpl);
                        xpl = (xp = x.parent) == null ?
                            null : xp.left;
                    }
                    if (xpl != null) {
                        xpl.red = (xp == null) ? false : xp.red;
                        if ((sl = xpl.left) != null)
                            sl.red = false;
                    }
                    if (xp != null) {
                        xp.red = false;
                        root = rotateRight(root, xp);
                    }
                    x = root;
                }
            }
        }
    }
}
```
### 参考
[史上最清晰的红黑树讲解](https://www.cnblogs.com/CarpenterLee/p/5503882.html)