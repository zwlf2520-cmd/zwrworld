# 数组和链表在 Java 中的区别是什么？

## 回答重点


**在存储结构方面**：

数组基于连续的内存块，大小是固定的，需要重新分配内存来改变数组大小，内存使用紧凑但容易浪费空间。

链表是基于节点的结构，在内存中不需要连续存储，可以动态变化大小和插入删除节点，内存不连续但可以动态扩展。

**在访问速度方面**：

数组支持 O(1) 时间的随机访问，可以通过索引直接访问任何元素。

<pre class="notranslate"><sider-code-explain id="sider-code-explain" data-gpts-theme="light"></sider-code-explain><sider-code-explain id="sider-code-explain" data-gpts-theme="light"></sider-code-explain><sider-code-explain id="sider-code-explain" data-gpts-theme="light"><div class="chat-gpt-quick-query-container"><div class="css-1d6rho5 ant-app"><div class="sider-code-explain-button-wrapper-common"><div class="absolute right-0 top-2 pr-2 h-[28px]"><div class="sider-code-explain-button"><div class="button explain-button"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 12 12" fill="none" color="var(--gpts-primary-color5)" class="cursor-move"><defs><linearGradient id="paint0_linear_27482_345" x1="10.2109" y1="11.204" x2="1.01089" y2="2.40599" gradientUnits="userSpaceOnUse"></linearGradient><linearGradient id="paint1_linear_27482_345" x1="10.2109" y1="11.204" x2="1.01089" y2="2.40599" gradientUnits="userSpaceOnUse"></linearGradient></defs></svg></div></div></div></div></div></div></sider-code-explain><code>+----+----+----+----+----+----+
| 10 | 20 | 30 | 40 | 50 | 60 |
+----+----+----+----+----+----+
^
|
Index
</code></pre>

而链表访问特定元素需要线性时间O(n)，因为节点在内存中不一定连续，访问效率受限于链表的结构。

<pre class="notranslate"><sider-code-explain id="sider-code-explain" data-gpts-theme="light"></sider-code-explain><sider-code-explain id="sider-code-explain" data-gpts-theme="light"><div class="chat-gpt-quick-query-container"><div class="css-1d6rho5 ant-app"><div class="sider-code-explain-button-wrapper-common"><div class="absolute right-0 top-2 pr-2 h-[28px]"><div class="sider-code-explain-button"><div class="button explain-button"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 12 12" fill="none" color="var(--gpts-primary-color5)" class="cursor-move"><defs><linearGradient id="paint0_linear_27482_345" x1="10.2109" y1="11.204" x2="1.01089" y2="2.40599" gradientUnits="userSpaceOnUse"></linearGradient><linearGradient id="paint1_linear_27482_345" x1="10.2109" y1="11.204" x2="1.01089" y2="2.40599" gradientUnits="userSpaceOnUse"></linearGradient></defs></svg></div></div></div></div></div></div></sider-code-explain><code>Head
 |
 v
+----+----+     +----+----+     +----+----+
| 10 | *----> | 20 | *----> | 30 | *----> NULL
+----+----+     +----+----+     +----+----+
     ^               ^               ^
     |               |               |
   Node             Node             Node
</code></pre>

**在操作方面**：

数组插入和删除需要移动数据，时间复杂度为 O(n)，而链表则很灵活，可在 O(1) 时间插入和修改指定位置元素。

**在适用场景方面**：

数组适合需要快速随机访问且大小固定的场景，如实现缓存、表格等数据结构。

链表适合需要频繁插入和删除操作且大小不确定的场景，如队列、栈、链表等
