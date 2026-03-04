## PyMOL 

## PyMol概述
- Pymol名字的来源：“Py”表示该软件基于python计算机语言，“Mol”则是英文分子（molecule）的缩写，表示该软件用来显示分子结构
- 主要功能：创作高品质的小分子或是生物大分子（特别是蛋白质）的三维结构图像
- 软件的作者宣称，在所有正式发表的科学文献中的蛋白质结构图像中，有四分之一是使用PyMOL来制作
- 网站：http://www.pymol.org/
- DOC: https://pymol.org/dokuwiki/doku.php?id=command:zoom

### PyMol 语法

- Action
- Show
- Hide
- Label
- Color

### PymMol 操作

- 显示所有残基编号  
```bash
# 为每个氨基酸的α碳（CA）位置标记上该残基的编号
label name CA, resi

# 去除编号
label name CA, ""
```

- 选择选定的残基  
鼠标单击残基，在sele栏目选择L，选择residual


- 显示亲水和疏水氨基酸  
```bash
# 疏水氨基酸有9个：Gly,Ala,Val,Leu,Ile,Pro,Phe,Met,Trp
# 非电极性（亲水）氨基酸有6个：Ser,Thr,Cys,Asn,Gln,Tyr
# 命令行输入如下命令
color yellow, resn Gly+Ala+Val+Leu+Ile+Pro+Phe+Met+Trp
as sphere, resn Gly+Ala+Val+Leu+Ile+Pro+Phe+Met+Trp
as sphere,   resn Ser+Thr+Cys+Asn+Gln+Tyr+Asp+Glu+Arg+Lys+His
color blue,  resn Ser+Thr+Cys+Asn+Gln+Tyr+Asp+Glu+Arg+Lys+His
```

- 移动标签    
将模式切换至3-Button Editing, 按住Ctrl， 鼠标拖动标签字符即可

- 切换白色背景    
Display -> Background -> White
```bash
bg_color white
```

- 显示序列  
File -> Get PDB -> PDB ID : 1gtf -> Download
点击SEQ显示序列  

- 旋转图像   
对准图像的任意处点住鼠标左键然后移动鼠标

- 放大/缩小图像   
对准图像的任意处点住鼠标右键然后移动鼠标：向上是缩小，向下则是放大。

```bash
zoom  
zoom complete=1 
zoom 142/, animate=3 
zoom (chain A) 
```
- 移动图像    
对准图像的任意处点住鼠标中键或者滚轮，然后移动鼠标；或者 按住Ctrl 点击左键，移动即可。

- 显示表面     
show -> surface
Setting -> Transparency -> Surface -> 80%

- 发表模式     
Action -> present -> publication

- 直选中DNA
```bash
select dna, resn DA+DC+DG+DT
```
- 选中蛋白质
```bash
select protein_chain, chain A
```

- 精确选择并标记蛋白质 143 位残基
```bash
# 1. 选择所有蛋白质原子（排除DNA）
select protein, polymer.protein

# 2. 在蛋白质中选择143号残基（指定链ID更准确）
select res143_protein, resi 143 and polymer.protein

# 3. 标记该残基（显示残基编号和名称）
label res143_protein, "143-%s" % (resn)

# 4. 高亮显示该残基
show sticks, res143_protein
color red, res143_protein
```

- 图像保存
```bash
png saveID.png, 16.93cm, 16.93cm, dpi=300
save saveID.png
```



### AlphaFold3   
AlphaFold3预测的蛋白质之间的互作，使用PyMOL进行查看[example](https://www.bilibili.com/video/BV1Qt8iesE4e/?vd_source=16694f427952f2c01f3659ee0722320a)


- step 1  AlphaFold3预测     
[AlphaFold3](https://alphafoldserver.com/) 预测互作，产生cif文件，一般选择model_0.cif(互作效果最好的)

- step 2  导入文件    
[PyMOL](https://pymol.org/)导入cif文件，更改背景色 `Display` -> `Background` -> `White`

- step 3 更名和改色     
`Mouse` -> `Selection Mode` -> `Chains`
点选其中任意一条蛋白质链，选择之后，进行更名 `A` -> `rename selection` ,输入自己蛋白质的名字，紧接着改色`C` -> `color` -> `blue` (任意选一色)
将图层的状态变成非选；接着点击第二条链，重复 `A` -> `rename selection`，输入自己蛋白质的名字，紧接着改色`C` -> `color` -> `cyans` -> `cyan` (任意选一色)

- step 4 选择氢键    
对任意（蛋白链二选一）图层操作即可，`A` -> `Find` -> `polar contacts` -> `to other atoms in object`
出现淡黄色的小棒，即氢键；接着标出氢键，首先显示出棍状结构，点击其中一个蛋白质的图层，选`S` -> `show sticks`，
接着选第二个蛋白图层，选`S` -> `show sticks`；然后将Chanins变成氨基酸残基Residuals, `Mouse` -> `Selection Mode` -> `Residuals`
;接着，放大，调整位置，选择氢键连接的末端的残基，放大缩小仔细查看，标记全部的氢键。最后隐藏两者的棍棒结构，`H` -> `hide sticks`, 两个图层均完同样的`hide sticks`;
发现Cartoon结构干扰视觉判断，选择`Setting` -> `Transparency` -> `Cartoon` -> `80%`,将透明度设为80%。选择氢键那个图层，选择`S` -> `sticks`

- step 5 标记位点    
选择氢键图层，`L` -> `residuals`; 点击编辑操作`3-Button Viewing` -> `3-Button Editing`, 按住`Ctrl`键，长按鼠标左键可以对标签进行拖拉操作，
`Ctrl + z`取消操作。移动好之后，可以添加连接线`Setting` -> `Label` -> `Show Connectors`,这时候互作位点图片就做好了

- step 6 图片保存   
全局图，局部图，变化角度保存即可



### 查看AlphaFold3预测的两个蛋白质互作结果
[refer](https://zhuanlan.zhihu.com/p/662394976)

- 1.打开名字为0.cif的文件,点击右侧的S按钮可以改变显示的模式   
- 2.菜单栏Displaced->sequence，显示sequence   
- 3.在命令行键入命令，修改链的颜色（如果是两条蛋白序列，默认的名称是A和B）  
```bash
sele Chain A                   #select Chain A
set_name sele,seleA            #rename Chain A

sele Chain B                   #select Chain B
set_name sele,seleB            #rename Chain B    
```
之后，右侧面板会增加两个object，seleA，seleB    
点击C按钮，更改seleA和seleB的颜色，分别改为green和blue  

- 4.查看互作区域
下载这个脚本保存到本地，命名为InterfaceResidues.py  
```bash
from pymol import stored
 
def interfaceResidues(cmpx, cA='c. A', cB='c. B', cutoff=1.0, selName="interface"):
	"""
	interfaceResidues -- finds 'interface' residues between two chains in a complex.
 
	PARAMS
		cmpx
			The complex containing cA and cB
 
		cA
			The first chain in which we search for residues at an interface
			with cB
 
		cB
			The second chain in which we search for residues at an interface
			with cA
 
		cutoff
			The difference in area OVER which residues are considered
			interface residues.  Residues whose dASA from the complex to
			a single chain is greater than this cutoff are kept.  Zero
			keeps all residues.
 
		selName
			The name of the selection to return.
 
	RETURNS
		* A selection of interface residues is created and named
			depending on what you passed into selName
		* An array of values is returned where each value is:
			( modelName, residueNumber, dASA )
 
	NOTES
		If you have two chains that are not from the same PDB that you want
		to complex together, use the create command like:
			create myComplex, pdb1WithChainA or pdb2withChainX
		then pass myComplex to this script like:
			interfaceResidues myComlpex, c. A, c. X
 
		This script calculates the area of the complex as a whole.  Then,
		it separates the two chains that you pass in through the arguments
		cA and cB, alone.  Once it has this, it calculates the difference
		and any residues ABOVE the cutoff are called interface residues.
 
	AUTHOR:
		Jason Vertrees, 2009.		
	"""
	# Save user's settings, before setting dot_solvent
	oldDS = cmd.get("dot_solvent")
	cmd.set("dot_solvent", 1)
 
	# set some string names for temporary objects/selections
	tempC, selName1 = "tempComplex", selName+"1"
	chA, chB = "chA", "chB"
 
	# operate on a new object & turn off the original
	cmd.create(tempC, cmpx)
	cmd.disable(cmpx)
 
	# remove cruft and inrrelevant chains
	cmd.remove(tempC + " and not (polymer and (%s or %s))" % (cA, cB))
 
	# get the area of the complete complex
	cmd.get_area(tempC, load_b=1)
	# copy the areas from the loaded b to the q, field.
	cmd.alter(tempC, 'q=b')
 
	# extract the two chains and calc. the new area
	# note: the q fields are copied to the new objects
	# chA and chB
	cmd.extract(chA, tempC + " and (" + cA + ")")
	cmd.extract(chB, tempC + " and (" + cB + ")")
	cmd.get_area(chA, load_b=1)
	cmd.get_area(chB, load_b=1)
 
	# update the chain-only objects w/the difference
	cmd.alter( "%s or %s" % (chA,chB), "b=b-q" )
 
	# The calculations are done.  Now, all we need to
	# do is to determine which residues are over the cutoff
	# and save them.
	stored.r, rVal, seen = [], [], []
	cmd.iterate('%s or %s' % (chA, chB), 'stored.r.append((model,resi,b))')
 
	cmd.enable(cmpx)
	cmd.select(selName1, None)
	for (model,resi,diff) in stored.r:
		key=resi+"-"+model
		if abs(diff)>=float(cutoff):
			if key in seen: continue
			else: seen.append(key)
			rVal.append( (model,resi,diff) )
			# expand the selection here; I chose to iterate over stored.r instead of
			# creating one large selection b/c if there are too many residues PyMOL
			# might crash on a very large selection.  This is pretty much guaranteed
			# not to kill PyMOL; but, it might take a little longer to run.
			cmd.select( selName1, selName1 + " or (%s and i. %s)" % (model,resi))
 
	# this is how you transfer a selection to another object.
	cmd.select(selName, cmpx + " in " + selName1)
	# clean up after ourselves
	cmd.delete(selName1)
	cmd.delete(chA)
	cmd.delete(chB)
	cmd.delete(tempC)
	# show the selection
	cmd.enable(selName)
 
	# reset users settings
	cmd.set("dot_solvent", oldDS)
 
	return rVal
 
cmd.extend("interfaceResidues", interfaceResidues)
```
点击菜单栏file->run script -> InterfaceResidues.py, 运行脚本。    
之后，在命令行键入命令(your_projec_name替换成你的projectname)    
这里换成fold_2025_04_12_14_54_ampka_hspa9   
```bash
interfaceResidue your_projec_name, Chain A, Chain B
```
之后，右侧面板多了一个interface object, 这个里面是互作区域。  
修改个interface object的颜色为red, 可以看到红色的为互作区域    

修改显示模式，点击all->H->hide everything，all->S->sticks  
或者
修改显示模式，点击all->H->hide everything，all->S->spheres, 红色为互作区域   
在sequence panel可以看到具体哪些氨基酸互作（红色的为互作氨基酸）   

- 5.查看极性共价键     
修改显示模式为sticks，点击all->H->hide everything，all->S->sticks  

点击其中一条链的A->find->polor contacts->to any atoms, 可以看查看极性键，  

点击S->lables, 可以显示共价键的键长，单位是Å。  


### pymol 显示PDB结构，显示颜色
1.使用 spectrum 命令  
pymol命令行输入
```bash
# 从UniProt/AlphaFold下载的PDB文件，其pLDDT分数存储在B-factor列
# 这行命令会按照B-factor值从低到高，用蓝-白-红的渐变给蛋白质上色
spectrum b, rainbow_rev, minimum=50, maximum=90
```

解释：  

`spectrum b`：指定使用B-factor列的数据来进行着色。  
`rainbow_rev`：这是颜色方案。AlphaFold通常用蓝色表示高置信度（高分），红色表示低置信度（低分）。rainbow_rev 正好是蓝-白-红的渐变，与之一致。你也可以用 blue_white_red 。  
`minimum=50, maximum=90`：这是pLDDT分数的范围。设置这个范围后，得分高于90的都会显示为深蓝色，低于50的显示为深红色，中间则平滑过渡。这个范围是根据AlphaFold的评分标准来的，能让着色效果和UniProt网站上看到的非常接近。

2.PDB插件

PyMOL有一个官方的PDB插件，它可以自动从PDB数据库获取结构，并应用一些常见的着色方案，比如显示不同的分子或结构域。

不过，这个插件主要是为PDB数据库中的实验结构设计的，它不一定能完美再现UniProt上为AlphaFold结构定制的pLDDT着色。如果你的目标是通用着色，方法一更直接有效；如果你想探索蛋白质的不同结构域，可以试试这个插件：

点击PyMOL菜单栏的 Plugin -> Plugin Manager。

在 Repositories 或 Installed 标签页中找到并加载 PDB 插件。

之后在 Plugin 菜单下就会出现 PDB Analysis 的相关选项，你可以输入PDB ID进行探索
