编写代码
启动VSCode编辑器
双击桌面的Visual Studio Code图标，打开代码编辑器

图片#100% #auto

打开软件后，选择File->Open Folder打开工作目录

图片#100% #auto

选择Desktop下的workspace目录作为工作目录并确认

图片#100% #auto

并信任该路径（信任工作路径以便VSCode编辑器功能运行更完善）图片#100% #auto
初入代码
在workspace目录下创建python文件main.py，选择File->New File创建，并选择为python文件

图片#100% #auto

图片#100% #auto

使用Ctrl+S快捷键保存文件，并对其重命名

图片#100% #auto

接下来，我们编辑文件内容

py2neo是一个客户端库和工具包，可通过Python应用程序内部和命令行使用Neo4J。

在连接数据库时，请将密码替换为修改后的密码。

在main.py中编辑以下内容：

# 导入图形数据库
from py2neo import Graph

# 连接到本地的Neo4j数据库
graph = Graph("bolt://localhost:7687", auth=("neo4j", "pwd123qaz")) # 请替换为你的密码

# 验证连接
print(graph)
以上代码使用账户和密码通过代码链接Neo4J数据库，接下来选择Python解释器并运行改代码

选择View->Command Palette打开命令面板功能，搜索Python Select Interpreter

图片#100% #auto

选择Miniconda创建的base环境的Python内核

注意：选择其他内核会导致实验代码无法运行
图片#100% #auto
选择完成后，通过编辑器右下角信息栏确认环境

图片#100% #auto

选择右上角按钮运行当前代码

图片#100% #auto

正常输出连接信息没有报错即表明运行成功

构建知识图谱
首先，将实体和关系数据通过代码写入，并写入数据库

注意：本节的代码内容追加到之前的代码后面
# 清空数据库，确保每次运行时都是一个全新的知识图谱
graph.run("MATCH (n) DETACH DELETE n")

# 定义数据：知识模块和概念
knowledge_graph = {
    "Module": [
        "基础语法", "控制结构", "数据结构", "函数与模块", "面向对象编程", "文件操作", "异常处理"
    ],
    "Concept": [
        "变量", "数据类型", "运算符", "条件语句", "循环", "列表", "字典", "集合", "元组", "函数",
        "递归", "类", "对象", "继承", "多态", "文件读写", "异常捕获"
    ]
}

# 定义关系（格式：概念A, 关系, 概念B）
relations = [
    "变量,属于,基础语法",
    "数据类型,属于,基础语法",
    "运算符,属于,基础语法",
    "条件语句,属于,控制结构",
    "循环,属于,控制结构",
    "列表,属于,数据结构",
    "字典,属于,数据结构",
    "集合,属于,数据结构",
    "元组,属于,数据结构",
    "函数,属于,函数与模块",
    "递归,属于,函数与模块",
    "类,属于,面向对象编程",
    "对象,属于,面向对象编程",
    "继承,属于,面向对象编程",
    "多态,属于,面向对象编程",
    "文件读写,属于,文件操作",
    "异常捕获,属于,异常处理",
    "循环,依赖,条件语句",
    "递归,扩展,函数",
    "继承,扩展,类",
    "多态,扩展,继承",
    "基础语法,相关,控制结构",
    "控制结构,相关,数据结构",
    "数据结构,相关,函数与模块",
    "函数与模块,相关,面向对象编程"
]

# 创建知识模块（Module）节点
for module in knowledge_graph["Module"]:
    graph.run(f"CREATE (:Module {{name: '{module}'}})")

# 创建知识点（Concept）节点
for concept in knowledge_graph["Concept"]:
    graph.run(f"CREATE (:Concept {{name: '{concept}'}})")

# 创建关系（包括概念与模块、模块之间的关系）
for relation in relations:
    parts = relation.split(",")  # 以逗号分隔每个关系中的元素
    from_node = parts[0]  # 起始节点
    relation_type = parts[1]  # 关系类型
    to_node = parts[2]  # 目标节点
    
    # 通过MATCH语句匹配数据库中的对应节点，并创建关系
    graph.run(f"""
        MATCH (from {{name: '{from_node}'}}), (to {{name: '{to_node}'}})
        CREATE (from)-[:{relation_type}]->(to)
    """)
	
接下来，通过代码编写CQL语句查询写入的数据内容

# 查询所有知识模块
print("\n所有知识模块：")
modules = graph.run("MATCH (n:Module) RETURN n.name AS name")
for module in modules:
    print(f"模块名称: {module['name']}")

# 查询所有知识点
print("\n所有知识点：")
concepts = graph.run("MATCH (n:Concept) RETURN n.name AS name")
for concept in concepts:
    print(f"知识点: {concept['name']}")

# 查询所有关系
print("\n所有关系：")
relations_query = graph.run("MATCH (n)-[r]->(m) RETURN n.name AS from_name, r, m.name AS to_name")
for relation in relations_query:
    print(f"从 '{relation['from_name']}' 到 '{relation['to_name']}' 的关系: {relation['r']}")
编写完成后，运行程序

图片#100% #auto

运行完成后，使用浏览器访问localhost:7474，并输入账户密码进行登录

依次点击以下按钮便可查看知识图谱。

图片#100% #auto

注意：默认查看仅显示25条数据
 

重要的文件建议保存在工作路径：/mnt/cgshare下，如果容器出现故障，桌面还原之后，点击 “更多” / “工作目录文件浏览器” 找回。







