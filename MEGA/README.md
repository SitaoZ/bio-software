
###  实现MEGA NJ树的scale
从这张图看，树文件有枝长：左下角显示 SBL = 9.31349865，说明枝长总和不为零。Scale 灰色大概率不是因为没有枝长。

在 MEGA X 的 Tree Explorer 里试试：  
1. 菜单栏选 View → Show/Hide → Scale Bar。MEGA X 的比例尺显示入口在这里；若菜单项可选，点击即可显示。MEGA X Tree Explorer 菜单示例  
3. 如果仍是灰色，先看 View → Topology Only 是否被勾选。取消它，再回到 View → Show/Hide → Scale Bar。  
4. 也可以点工具栏上的扳手图标打开选项，查看比例尺相关的绘图设置。  
你的树是从 TTLL_muscle.nwk 打开的。左下角的 SBL 证明文件里有枝长，但 MEGA 仍可能因当前显示模式或导入树的格式而禁用比例尺。如果取消 Topology Only 后仍不能选，建议重新从 MEGA 的 Neighbor-Joining Tree 分析结果窗口导出树，再试比例尺。比例尺显示的是序列替换距离，不是时间。
