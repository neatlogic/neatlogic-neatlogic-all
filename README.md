![NeaLogic](README_IMAGES/logo.png)

<p align="left">
    <a href="https://opensource.org/licenses/Apache-2.0" alt="License">
        <img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" /></a>
<a target="_blank" href="https://join.slack.com/t/neatlogichome/shared_invite/zt-1w037axf8-r_i2y4pPQ1Z8FxOkAbb64w">
<img src="https://img.shields.io/badge/Slack-Neatlogic-orange" /></a>
</p>

中文 / [English](README.en.md)

## 关于

NeatLogic 是一套渐进式 ITOM 平台，致力为不同类型不同规模用户提供 ITOM 解决方案。除了开源版本，我们也为企业客户提供二次开发、咨询等服务，邮件咨询请联系<a href="mailto:zhangzm@neatlogic.com">zhangzm@neatlogic.com</a>，或者进入企业微信交流。
![index](README_IMAGES/index.png)

NeatLogic 原生支持多租户（中间件共享，数据库分租户模式），模块化扩展，已包含流程引擎、表单引擎、报表引擎、大屏、仪表板等关键核心功能，对数据规模敏感的功能，例如 CMDB 在设计上做了充分考虑，同时兼顾报表和日常使用的性能要求。仅使用基本功能情况下，仅需要 Tomcat、Nginx 和 Mysql8+，部分高级功能，例如自动采集需要使用 MongoDb。\
NeatLogic 提供 docker 镜像部署模式和安装包部署模式，自带所有数据库和中间件，所有关键服务都封装了启停命令，中间件、软件包和配置文件都做了分离，方便用户日常维护。

## 运行环境
操作系统：Linux，java 版本：8+，进程内存：4G 以上，建议 8G。

## 依赖组件

|组件|版本|必要|说明|
|---|---|---|---|
|[Tomcat](https://tomcat.apache.org/)|9.0.75|✅|Servlet容器|
|[Mysql](https://www.mysql.com/)|8.0.27|✅|数据库|
|[Nginx](https://www.nginx-cn.net/)|1.16.1|✅|前端服务器|
|[MongoDb](https://www.mongodb.com/)|4.4.3|✖️|自动采集临时数据库，如果使用CMDB自动采集功能必须使用|
|[MinIO](https://www.minio.org.cn/)|5.0.6(2020-05-08T02:40:49Z)|✖️|附件存储，如果不部署自动切换成本地存储模式，多服务共享附件时需要把上传目录设到共享目录上，例如Nas|
|[Nacos](https://nacos.io/zh-cn/)|2.1.0|✖️|配置统一管理，如果不部署nacos，配置改为读取本地config.properties文件|
|[ActiveMQ Artemis](https://activemq.apache.org/components/artemis/)|2.17.0|✖️|消息队列，没有第三方系统消费消息可以不部署|

## 项目组织

NeatLogic 采用模块化方式管理代码。

- 🌎 neatlogic-itom-all 方便用户一次性获取所有代码。
- neatlogic-parent 用于管理全局 pom 文件，管理所有公共第三方包引用。
- neatlogic-webroot 用于生成 war 包和管理子模块引用，可根据需要修改 pom 文件加载不同子模块生成最终的 war 包。
- neatlogic-framework 是基础模块，所有子模块都需要引用 neatlogic-framework，neatlogic-framework 的 bean 所有子模块均可织入。
- neatlogic-tenant 是基础功能的 api 部分 bean，其他子模块不可见。
- neatlogic-web 是前端代码。
- ❌【暂不开源】neatlogic-master是租户管理模块后端代码，用于管理 neatlogic 的租户信息。
- ❌【暂不开源】neatlogic-master-web是租户管理模块前端代码，部署时需要和 neatlogic-web 分开部署。
- 其余每个功能模块都需要分拆成两个代码模块，分别是 neatlogic-xxx 和 neatlogic-xxx-base。由于 maven 不支持交叉引用，neatlogic-xxx-base 主要用于被其他模块引用，主要放 pojo 和一些底层接口。neatlogic-xxx 需要引用 neatlogic-xxx-base，主要放自身独占的业务 bean。

## 服务架构图
![架构图](README_IMAGES/inf.jpeg)

## 内部插件
为了提升研发效率，我们自研了以下IDE插件协助开发。
- 🚀 [i18nhelper-idea](../../../i18nhelper-idea/blob/main/README.md) 用于自动替换i18n键值和文本翻译，intellij IDEA专用，neatlogic后端的国际化没有采用spring 5官方的properties格式方案，而是改成了和前端一致的json格式方案。
- 🚀 [i18nhelper-vscode](../../../i18nhelper-vscode/blob/main/README.md) 用于自动替换i18n键值和文本翻译，vscode专用。

## 分支说明
- 📌 develop x.x.x：研发分支，还没有在重点客户中完成部署并结项，主线功能基本稳定，功能持续增加中，部分功能可能有调整但不影响使用。
- 📌 release x.x.x：发布分支，已经在重点客户中完成部署并结项，核心功能不会有大调整，仅做功能调整和缺陷修复。
- 🏁 (标签)x.x.x：release每次版本调整都会打出对应tag，一般是功能调整过或重大缺陷修复后产生。

## 版本说明
Neatlogic为研发和发布两个分支的版本号划分了两个区间，研发使用[0,1)区间，发布分支使用[1,)区间，这样做的用意是如果想使用版本区间的方式关联模块，不会造成版本冲突。
- 🔢 `develop`分支版本号以0开头，如0.3.1.0-SNAPSHOT。
- 🔢 `release`分支版本号在封板后去掉前面的0.和后面的-SNAPSHOT，如3.1.0。缺陷修复在第3位+1，如3.1.1，功能改进在第2位+1，如3.2.0。

## Maven Profile说明
Neatlogic通过maven profile管理不同分支的版本号以及社区版和商业版的模块依赖。
- 📇 `develop`：研发分支版本profile，如果当前在`develop`分支，请勾选此profile，所有依赖包版本号都以-SNAPSHOT结尾。
- 📇 `release`：发布分支版本profile，如果当前在`release`分支，请勾选此profile，所有依赖包都是正式的版本号。
- 📇 `commercial`：商业版专用profile，如果使用社区版进行二次开发，不要勾选此profile，这样不会依赖任何商业版的模块。

    __❗注意：__ develop和release只能二选一，commercial可以和develop或release一起勾选。


## 功能清单

### ⭐️ 基础功能
<table><tr><td>编号</td><td>分类</td><td>功能点</td><td>说明</td></tr><tr><td>1</td><td rowspan="11">基础</td><td rowspan="5">系统架构</td><td>平台采用前后端解耦架构，纯B/S架构无需安装任何插件，支持常用浏览器http/https访问。</td></tr><tr><td>2</td><td>支持前端界面和后端服务、以及执行节点高可用、分布式部署。</td></tr><tr><td>3</td><td>平台采用底层框架和诸多功能模块组成，由底层框架统一调度、驱动加载功能模块，形成完整的ITOM解决方案。</td></tr><tr><td>4</td><td>平台具备常用功能配置扩展能力，如IT服务的管理流程、表单、数据矩阵等功能点。</td></tr><tr><td>5</td><td>平台采用模块式架构，客户差异或定制需求形成交付的定制模块，<font class="font6">用户可基于定制模块进行二次开发</font><font class="font5">，包括：新增模块、新增流程组件、接口等功能。</font></td></tr><tr><td>6</td><td rowspan="2">用户、组织架构、角色管理</td><td>支持用户、组织架构、角色增删改查管理操作，以及用户与组关系、用户与角色关系、角色与组织架构权限关联定义。</td></tr><tr><td>7</td><td>不限制组织架构层级，支持不同层级、分组领导、岗位设置。</td></tr><tr><td>8</td><td>功能权限管理</td><td>支持功能权限赋权给角色、人员、组织架构，限制用户查看、操作等权限设置。</td></tr><tr><td>9</td><td>服务窗口</td><td>支持多维度服务窗口定义，支持工作日、上班时间段定义。</td></tr><tr><td>10</td><td>日志审计</td><td>平台上所有的管理操作记录到审计日志，可按操作对象、时间追溯。</td></tr><tr><td>11</td><td>快速贴图</td><td>支持系统的富文本框内快速贴图，方便用户操作。</td></tr><tr><td>12</td><td rowspan="6">仪表板</td><td rowspan="6">仪表板管理</td><td>支持用户定义系统层面、个人层面的仪表数据面板。</td></tr><tr><td>13</td><td>支持用户新增、修改、删除、复制、导入、导出仪表板。</td></tr><tr><td>14</td><td>仪表板支持文本、数据、表格、饼图、雷达图、柱形图、折线图、面积图、仪表盘、堆叠图、散点图、热力图等常用呈现组件</td></tr><tr><td>15</td><td>仪表板支持用户基于选择的呈现组件和对应的数据源，通过配置快速的呈现数据。</td></tr><tr><td>16</td><td>仪表板支持用户拖拽布局。</td></tr><tr><td>17</td><td>系统层面的仪表板支持授权到用户、角色、组织架构，具备授权的用户可查看对应的系统层面仪表板。</td></tr><tr><td>18</td><td rowspan="7">数据源</td><td rowspan="7">数据矩阵</td><td>支持用户自定义数据矩阵增删改查、导入、导出管理。</td></tr><tr><td>19</td><td>支持用户自定义静态数据源，自定义表头，配置数据，作为表单、报表等场景的下拉框、表格选择器等组件的数据源。</td></tr><tr><td>20</td><td>支持用户配置接口查询第三方数据，作为表单、报表等场景的下拉框、表格选择器等组件的数据源。</td></tr><tr><td>21</td><td>支持用户配置本系统CMDB视图数据，作为表单、报表等场景的下拉框、表格选择器等组件数据源。</td></tr><tr><td>22</td><td>支持用户配置SQL，创建查询视图，可查询系统内所有数据表数据，作为表单、报表等场景的下拉框、表格选择器等组件的数据源。</td></tr><tr><td>23</td><td>可复制现有矩阵，用于创建与原矩阵相似的新矩阵。</td></tr><tr><td>24</td><td>矩阵支持导出、导入，可用于在不同环境的环境迁移矩阵。</td></tr><tr><td>25</td><td rowspan="7">数据仓库</td><td rowspan="7">数据仓库</td><td>支持用户自定义数据数据仓库增删改查、导入、导出管理。</td></tr><tr><td>26</td><td>支持用户自定义数仓把基础过程数据表转换为统计类的高阶管理对象。</td></tr><tr><td>27</td><td>支持用户自定义数仓的数据源。</td></tr><tr><td>28</td><td>支持用户自定义数仓的数据对象，过滤条件</td></tr><tr><td>29</td><td>支持用户自定义数仓的数据模式，如全量替换或增量追加。</td></tr><tr><td>30</td><td>支持用户自定义人工同步数据、定时同步数据。</td></tr><tr><td>31</td><td>用户自定义数据源可用于大屏、仪表板、报表等消费场景。</td></tr><tr><td>32</td><td rowspan="8">集成</td><td rowspan="3">接口集成</td><td>支持用户自定义系统内部或系统外部rest接口的集成配置管理，包括接口的提交方式、认证方式、参数输出格式转换、参数输入格式转换。</td></tr><tr><td>33</td><td>支持用户自定义集成接口的入参和出参在线帮助字典，且支持直接在线接口调用测试。</td></tr><tr><td>34</td><td>支持集成接口清单的导入、导出、调用记录审计。</td></tr><tr><td>35</td><td rowspan="2">消息订阅</td><td>支持用户按消息类型订阅或取消订阅。</td></tr><tr><td>36</td><td>支持消息类型的临时订阅和持续订阅。</td></tr><tr><td>37</td><td rowspan="3">接口管理</td><td>平台采用前后端解耦架构，数据交互基于标准rest接口，接口分为内部接口和外部接口。在接口管理内可以查看接口的入参、出参、帮助、认证方式等说明。</td></tr><tr><td>38</td><td>接口支持开启调用记录，也关闭调用记录，防止高频调用接口记的录造成存储空间浪费</td></tr><tr><td>39</td><td>支持外部接口配置多个调用实例，以及认证方式、认证用户、认证密码、有效时间等配置。</td></tr><tr><td>40</td><td rowspan="11">报表</td><td rowspan="6">报表模板</td><td>支持用户新增、复制、导入、导出报表模板。</td></tr><tr><td>41</td><td>支持用户自定义报表模板的显示界面、过滤条件、以及数据源配置。</td></tr><tr><td>42</td><td>报表条件支持常用的HTML组件，如：文本框、下拉框、多选、单选、日期等控件，以及绑定控件数据源设置。</td></tr><tr><td>43</td><td>报表呈现支持常用的表格、曲线图、饼图、柱状图等常用控件。</td></tr><tr><td>44</td><td>报表数据源支持标准的TSQL语句和rest接口。</td></tr><tr><td>45</td><td>支持报表设置访问权限。</td></tr><tr><td>46</td><td rowspan="5">报表管理</td><td>支持报表基于模板快速生成不同维度的管理报表。</td></tr><tr><td>47</td><td>支持报表按权限划分，不同权限的用户可见不同的报表。</td></tr><tr><td>48</td><td>支持报表在线实时修改配置、更新配置，无需重启应用服务。</td></tr><tr><td>49</td><td>支持报表导出功能，导出的附件格式支持：Word、Execl、PDF。</td></tr><tr><td>50</td><td>支持配置报表定时发送策略。</td></tr></table>

### ⭐️ ITSM
<table><tr><td>编号</td><td>分类</td><td>功能点</td><td>说明</td></tr><tr><td>1</td><td rowspan="13">流程</td><td rowspan="2">流程组件</td><td>根据平台加载的模块，自动加载对应的模块内的流程组件，如：事件、问题、变更、发布、自动化、自定义发布、接口调用组件等。</td></tr><tr><td>2</td><td>支持用户二次开发定义流程组件，如OA系统的审批、堡垒机授权等场景。</td></tr><tr><td>3</td><td rowspan="11">流程管理</td><td>支持用户自定义运维管理服务流程，支持图形化拖拉拽布局设计。</td></tr><tr><td>4</td><td>流程支持导出、导入，可用于流程在不同环境迁移。</td></tr><tr><td>5</td><td>支持复制现有流程，用于创建与原流程相似的新流程。</td></tr><tr><td>6</td><td>流程支持并行、串行、条件节点，可做汇聚、分流，回退流转路径。</td></tr><tr><td>7</td><td>流程中的节点支持自动开始处理、自动流转设置。</td></tr><tr><td>8</td><td>流程支持用户自定义个性化表单，且支持不同的流程步骤隐藏或禁用部分表单数据。</td></tr><tr><td>9</td><td>流程步骤支持用户自定义个性化的动作，如催办、取消、修改上报内容等配置。</td></tr><tr><td>10</td><td>流程步骤支持用户自定义步骤的通知策略、通知动作、通知内容。</td></tr><tr><td>11</td><td>流程步骤支持用户自定义步骤的外部调用动作，如流程状态变化，实时同步给第三方系统。</td></tr><tr><td>12</td><td>流程提供自动处理节点，用于在编排中调用第三方接口。</td></tr><tr><td>13</td><td>流程提供自动化节点、CMDB节点，可实现平台产品数据和场景闭环。</td></tr><tr><td>14</td><td rowspan="9">表单</td><td rowspan="9">表单管理</td><td>支持用户自定义表单，支持拖拉拽方式的表单布局。</td></tr><tr><td>15</td><td>提供丰富的组件，包括：文本、富文本、时间、密码、下拉、级联下拉、下拉树、超链接、附件上传、用户选择器、表格选择器、表格输入等组件。</td></tr><tr><td>16</td><td>支持同一个表单，在不同的流程节点上，支持设置不同的查看，编辑权限。</td></tr><tr><td>17</td><td>支持表单数据联动，如：表单控件A的值发生改变后，可自动对另一个控件B做显示、隐藏、禁用、赋值、过滤等操作。</td></tr><tr><td>18</td><td>表单设计过程中，支持预览。</td></tr><tr><td>19</td><td>支持表单的表格化布局方式，支持合并行、列等操作。</td></tr><tr><td>20</td><td>支持表单的版本管理，可快速切换指定版本，支持记录版本信息。</td></tr><tr><td>21</td><td>支持复制现有表单，用于创建与原表单类似的新表单。</td></tr><tr><td>22</td><td>表单支持导出、导入，可用于在不同环境的迁移表单。</td></tr><tr><td>23</td><td rowspan="11">服务目录</td><td rowspan="3">服务类型</td><td>支持用户新增、修改、删除服务类型。</td></tr><tr><td>24</td><td>支持常用的服务类型定义，如：事件、问题、变更、发布等。</td></tr><tr><td>25</td><td>支持不同服务类型工单自定义显示颜色、工单序列号生成规则，如：类型+年月日+当天同类型的工单量。</td></tr><tr><td>26</td><td rowspan="2">服务目录</td><td>支持用户新增、修改、删除服务目录。</td></tr><tr><td>27</td><td>支持无限制的服务目录层级，支持服务目录设置权限、自定义服务目录的上报帮助。</td></tr><tr><td>28</td><td rowspan="6">服务通道</td><td>支持用户新增、修改、删除服务通道。</td></tr><tr><td>29</td><td>支持多个服务通道可对应一个管理服务流程。</td></tr><tr><td>30</td><td>支持服务通道优先级、服务类型、服务窗口、服务范围(权限)、服务通道帮助等配置。</td></tr><tr><td>31</td><td>支持服务通道指定是否支持移动端使用。</td></tr><tr><td>32</td><td>支持根据服务通道、服务窗口、优先级动态匹配SLA策略。</td></tr><tr><td>33</td><td>支持服务通道快速搜索、收藏。</td></tr><tr><td>34</td><td rowspan="5">工单面板</td><td rowspan="5">工单中心</td><td>支持自定义用户角度的个人代办分类，如：我的上报、待我处理、我所在组待处理、我参与工单等类型。</td></tr><tr><td>35</td><td>支持按工单类型，配置工单查看权限。</td></tr><tr><td>36</td><td>同时支持卡片、列表方式展示工单数据，超时工单提供红色超时标识。</td></tr><tr><td>37</td><td>支持工单的简单查询条件和复杂的组合条件检索。</td></tr><tr><td>38</td><td>用户可根据上报人、处理人、上报通道、是否超时等条件，创建个人工单分类，在工单中心生成固定菜单。</td></tr><tr><td>39</td><td rowspan="5">任务分派</td><td rowspan="3">人工指派</td><td>支持指定分派到人员、组织、角色、干系人（例如上报人、待报人）。</td></tr><tr><td>40</td><td>支持服务台或具备权限用户人工干预转派工单的处理人、组、角色。</td></tr><tr><td>41</td><td>支持由前置步骤处理人指定下游节点处理人。</td></tr><tr><td>42</td><td rowspan="2">自动分派</td><td>支持根据表单值动态分派到处理人、组、角色。</td></tr><tr><td>43</td><td>支持复杂分派器动态分派处理人。例如，按工作量平均分配，分派至上报人部门领导等。</td></tr><tr><td>44</td><td rowspan="3">用户报障</td><td rowspan="3">服务上报</td><td>支持用户PC端、移动端快速发起故障上报。</td></tr><tr><td>45</td><td>支持具备权限用户通过导入文件方式的批量上报，事后补单。</td></tr><tr><td>46</td><td>支持具备权限用户或服务台代替他人发起问题上报。</td></tr><tr><td>47</td><td rowspan="8">工单处理</td><td rowspan="8">工单流转</td><td>支持工单的流转、驳回、取消、管理干预等操作。</td></tr><tr><td>48</td><td>支持工单上报、处理过程、活动日志等操作审计。</td></tr><tr><td>49</td><td>支持在工单处理过程中转交、请求协助、咨询流转操作。</td></tr><tr><td>50</td><td>支持工单内容同步到知识库，生成知识库文档。</td></tr><tr><td>51</td><td>支持工单在处理过程中关联、转报其他服务工单。</td></tr><tr><td>52</td><td>支持记录工单生命周期的操作日志，包括表单修改、回复、附件上传、工单流转、回退等生命周期的所有变化均记录在案。</td></tr><tr><td>53</td><td>支持查看工单流程图，全局查看工单进度。</td></tr><tr><td>54</td><td>处理人视角支持工作台模式，快速切换当前用户的多个待处理任务。</td></tr><tr><td>55</td><td rowspan="5">时效</td><td rowspan="5">SLA时效</td><td>支持精确到工单的时效策略，以工单为单位统计时效。</td></tr><tr><td>56</td><td>支持精确到节点的时效策略以单节点或多节点为单位统计时效。</td></tr><tr><td>57</td><td>支持根据优先级、影响范围、上报人是否VIP等设置动态时效，可根据优先级、影响范围、上报人是否VIP等设置动态时效。</td></tr><tr><td>58</td><td>支持任务超时或临期通知，可配置超时自动通知，通知时间点可自定义，如超时前N分钟通知，或超时N分钟后通知；通知接收人可自定义。</td></tr><tr><td>59</td><td>支持任务超时或临期自动转派，可配置超时自动转派策略，转派时间可自定义，如超时前N分钟转派，或超时N分钟后转派；转派对象可自定义。</td></tr><tr><td>60</td><td rowspan="5">通知</td><td rowspan="2">通知模板</td><td>通知内容支持设置通知模板，通知模板可引用工单信息（包括但不限于工单号、工单标题、服务路径、上报内容、上报人、工单状态、优先级等）。</td></tr><tr><td>61</td><td>通知对象可以设置为工单的干系人（如上报人、处理人等），也可指定用户，角色。</td></tr><tr><td>62</td><td rowspan="3">通知策略</td><td>支持通知途径包括电话、短信等，通知插件支持用户二次开发。</td></tr><tr><td>63</td><td>支持用户自定义通知动作点，如：流程步骤激活、转交、完成、回退等。</td></tr><tr><td>64</td><td>支持代办任务定时催办通知，如我的代办事项、我所在组的代办事项催办通知等。</td></tr><tr><td>65</td><td rowspan="2">满意度评价</td><td>评分模板</td><td>支持用户自定义评分模板，设置评分维度，并与流程关联。实现不同流程采用不同的评分维度</td></tr><tr><td>66</td><td>自动评分</td><td>支持用户在一定的时间窗口内评分，超过时间窗口流程步骤自动关闭。</td></tr><tr><td>67</td><td rowspan="3">移动端</td><td>服务上报</td><td>支持服务端用户快速发起对应的服务通道问题上报登记。</td></tr><tr><td>68</td><td>工单中心</td><td>移动端与PC端操作界面一致，支持数据检索、工单分类、工单流转。</td></tr><tr><td>69</td><td>工单流转</td><td>移动端与PC端操作界面一致，支持转交、回退、完成等常规流转操作。</td></tr><tr><td>70</td><td rowspan="6">知识库</td><td>知识库模板</td><td>支持知识库模板定义，简化和约束添加知识的规范。</td></tr><tr><td>71</td><td rowspan="4">知识库</td><td>支持知识库的基础管理功能，以及对应知识库类型发布审批、版本管理功能。</td></tr><tr><td>72</td><td>支持知识库的版本比对，高亮显示版本差异。</td></tr><tr><td>73</td><td>支持markdown语法、代码块等高亮展示。</td></tr><tr><td>74</td><td>支持工单直接生成知识。</td></tr><tr><td>75</td><td>知识权限</td><td>支持自定义知识圈、自定义知识圈成员，审批成员，具备对应的权限可访问对应的知识库。</td></tr></table>

### ⭐️ CMDB
<table><tr><td>编号</td><td>分类</td><td>功能点</td><td>说明</td></tr><tr><td>1</td><td rowspan="11">模型管理</td><td>支持动态定义配置模型</td><td>配置模型支持动态定义，支持模型的属性、关系、显示名、唯一规则、校验规则、属性和关系分组等设置。</td></tr><tr><td>2</td><td>支持可选模型的字段类型</td><td>配置模型内属性设置，包括属性类型、校验、是否自动采集配置，常用的属性字段类型，包括：文本框、数字、文本域、下拉框、日期、时间、时间范围、密码、附件、表格、表达式、链接。</td></tr><tr><td>3</td><td>支持模型继承关系</td><td>支持定义抽象模型、模型继承，简化配置模型的属性、关系的维护。</td></tr><tr><td>4</td><td>支持模型关系类型定义</td><td>支持模型关系类型定义，包括关系的展示规则、和模型关系分组。</td></tr><tr><td>5</td><td>支持模型关系定义</td><td>模型关系支持上、下游引用和模型自我引用，一个关系关联多个目标等设置，支持单选和多选两种关联方式，支持唯一性校验，包括模型内部和全局范围的唯一性。</td></tr><tr><td>6</td><td>支持模型属性使用不同的校验规则</td><td>模型属性可以使用不同的校验规则，除了正则表达式，还支持调用第三方系统或通过定制的方式进行复杂的规则校验。</td></tr><tr><td>7</td><td>支持多个属性进行组合唯一判断</td><td>支持多个属性进行组合唯一判断，例如IP和端口的组合唯一，但允许出现相同IP和相同端口。</td></tr><tr><td>8</td><td>支持有完善的审计功能</td><td>有完善的审计功能，能详细记录发生变化的属性和关系。</td></tr><tr><td>9</td><td>授权管理</td><td>通过授权给不同的角色授予管理或查看的权限。</td></tr><tr><td>10</td><td>支持模型配置项数据合规规则定义</td><td>支持根据管理的要求对模型内的数据设置数据合规检查设置。</td></tr><tr><td>11</td><td>支持模型自动采集频率设置</td><td>支持设置模型的自动采集频率、数据范围。</td></tr><tr><td>12</td><td rowspan="9">配置项管理</td><td>支持数据事务</td><td>支持对模型内的配置项的事务操作，对只有提交事务权限的数据进行预览入库审核。事务包括：新增、修改、删除3种操作类型。</td></tr><tr><td>13</td><td>支持数据入库方式</td><td>支持对模型的配置页面操作、下载Execl模板批量导入、自动发现、接口操作方式。</td></tr><tr><td>14</td><td>支持数据拓扑展示</td><td>支持配置项数据根据模型定义的关系进行拓扑展示和展示规则设置。</td></tr><tr><td>15</td><td>支持明细界面配置项关系展示</td><td>支持配置项关系表格或列表方式展示。</td></tr><tr><td>16</td><td>支持数据级授权</td><td>支持数据级授权，根据配置项属性值配置授权规则。</td></tr><tr><td>17</td><td>合规检查</td><td>自定义规则的合规检查。</td></tr><tr><td>18</td><td>全局检索</td><td>根据关键字分词全文检索。</td></tr><tr><td>19</td><td>支持配置关系数据老化</td><td>对于自动采集的关系数据，可设置关系数据在一定的时间内老化清理。</td></tr><tr><td>20</td><td>团体管理</td><td>自定义配置模型数据只读、维护权限，支持对应模型配置项数据条件规则定义。</td></tr><tr><td>21</td><td rowspan="6">配置视图</td><td>支持配置展示跨模型属性和关系的自定义查询视图</td><td>支持根据模型之间的关联关系、配置展示出跨模型属性和关系的自定义查询视图。</td></tr><tr><td>22</td><td>支持视图属性可由用户自行配置和检索</td><td>视图属性可由用户自行配置，所有属性都支持排序和作为检索条件进行检索。</td></tr><tr><td>23</td><td>支持通过属性进行多重归并分组和总数计算</td><td>支持通过属性进行多重归并分组，并自动计算视图所查询出的配置项总数。</td></tr><tr><td>24</td><td>支持导出视图查询结果</td><td>视图查询结果支持导出。</td></tr><tr><td>25</td><td>支持授权控制视图的管理和查阅</td><td>支持通过授权控制视图的管理和查阅。</td></tr><tr><td>26</td><td>支持视图数据结构化展示</td><td>对视图数据进行逻辑组合分层展示。</td></tr><tr><td>27</td><td rowspan="4">资源中心</td><td>应用资源中心</td><td>以应用为角度的应用资源展示和消费，包括应用的模块、中间件、数据库、操作系统信息。</td></tr><tr><td>28</td><td>资产资源中心</td><td>以资源/职能岗位为角度的资源中心，包括：应用、应用实例、硬件等信息。</td></tr><tr><td>29</td><td>账号设置</td><td>支持资产绑定对应的公共账号、私有账号。</td></tr><tr><td>30</td><td>标签管理</td><td>支持资产标记不同的标签，且支持标签、类型进行数据查询。</td></tr><tr><td>31</td><td rowspan="3">自动发现</td><td>网段扫描</td><td>支持根据网段、端口、资产特征进行网段扫描。</td></tr><tr><td>32</td><td>资产特征</td><td>支持新增、导入资产特征。</td></tr><tr><td>33</td><td>未知设备</td><td>支持未知设备在线标记特征且自动导入到特征库。</td></tr><tr><td>34</td><td rowspan="8">数据采集</td><td>操作系统采集</td><td>包括主流的Windows服务器发行版本、Linux发行版本、AIX发行版本操作系统，支持无Agent和有Agent数据发现。</td></tr><tr><td>35</td><td>中间件采集</td><td>主流发行版本Tomcat、Nginx、WebSphere、WebLogic、Redis、Resin、Java进程、WebSphere MQ、ActiveMQ、RabbitMQ、Apache、IIS、JBoss、KeepAlive、Lighttpd、Python进程、Tuxedo、ZooKeeper、Memcached数据采集和关系发现。</td></tr><tr><td>36</td><td>数据库采集</td><td>包括主流发行版本MySql、Oracle、DB2、MSSQLServer、MongoDB、Elasticsearch、Hadoop、Sybase、PostgreSQL、Informix数据采集和关系发现。</td></tr><tr><td>37</td><td>网络设备采集</td><td>包括主流的负载均衡设备：F5、A10，各厂商交换机、路由器、防火墙数据采集和关系发现。</td></tr><tr><td>38</td><td>虚拟化采集</td><td>支持vSphere 6.0+、SMTX 、华为FusionCompute数据采集和关系计算。</td></tr><tr><td>39</td><td>服务器硬件采集</td><td>支持对人工导入的硬件设备进行数据补充和关系计算。</td></tr><tr><td>40</td><td>光交数据采集</td><td>支持主流的光交版本，对光交资产数据进行数据补充和关系计算。</td></tr><tr><td>41</td><td>存储设备采集</td><td>存储设备需因具体的客户现场环境而定，包括：IBM DS系列、IBM Flash系列、IBM V7000系列、IBM SVC、IBM FlashSystem 900、EMC RPA、EMC VNX、NetApp、HDS VSP系列、HDS AMS系列的数据采集和关系计算。</td></tr><tr><td>42</td><td rowspan="2">数据消费</td><td>RESTful接口</td><td>CMDB所有的操作支持RESTful接口管理，支持自定义接口认证方式、访问频率、访问时间等，包括常规的：模型管理、配置项管理、视图查询等操作。</td></tr><tr><td>43</td><td>消息订阅</td><td>CMDB的配置项操作推送数据到MQ，包括：新增、修改、删除操作。</td></tr></table>

### ⭐️ 运维自动化
<table><tr><td>编号</td><td>分类</td><td>功能点</td><td>说明</td></tr><tr><td>1</td><td rowspan="5">参数</td><td rowspan="2">全局参数</td><td>支持自动化作业全局参数的增删改查基础管理。</td></tr><tr><td>2</td><td>支持全局参数字段文本、密码、日期、文本域等类型。</td></tr><tr><td>3</td><td rowspan="3">预设参数</td><td>支持自动化作业预设参数的增删改查基础管理。</td></tr><tr><td>4</td><td>支持按工具库、原子操作预设参数集。</td></tr><tr><td>5</td><td>支持预设参数集引用全局参数。</td></tr><tr><td>6</td><td rowspan="5">分类</td><td rowspan="3">工具分类</td><td>支持工具分类的增删改查基础管理。</td></tr><tr><td>7</td><td>支持查看统计分类下面的工具库、自定义原子操作、关联编排的数量。</td></tr><tr><td>8</td><td>支持工具库按类型设置工具库的权限。</td></tr><tr><td>9</td><td rowspan="2">工具目录</td><td>支持工具目录的增删改查基础管理。</td></tr><tr><td>10</td><td>支持工具库按目录设置权限。</td></tr><tr><td>11</td><td rowspan="3">场景</td><td rowspan="3">编排场景</td><td>支持自动化场景的增删改查基础管理。</td></tr><tr><td>12</td><td>支持按类型、按场景、按岗位职能定义自动化场景分类。</td></tr><tr><td>13</td><td>支持在自动化的组合编排内，设置不同的使用场景，不同的使用场景包含了编排中的不同执行阶段。场景可以实现一个编排，多种使用场景的需求。</td></tr><tr><td>14</td><td rowspan="15">工具库</td><td rowspan="4">内置工具库</td><td>平台内置常用的基础工具库，包括基础工具、文件操作、配置备份等，不同自动化模块包括模块工具库，如：服务启停、灾备切换、软件安装、SQL处理、巡检、备份、自动采集。</td></tr><tr><td>15</td><td>支持内置工具在线测试。</td></tr><tr><td>16</td><td>支持内置工具库在线帮助，如输入参数、输出参数、执行方式、风险等级。</td></tr><tr><td>17</td><td>支持内置工具库关联自定义展示模板。</td></tr><tr><td>18</td><td rowspan="11">自定义原子操作</td><td>支持常见的脚本语言，包括Python、Ruby、VBScript、Perl、PowerShell、CMD、Bash、csh、ksh、sh、JavaScript。</td></tr><tr><td>19</td><td>支持丰富的输入、输出参数类型，包括文本、文本域、密码、文件、时间、日期、单选、多选、开关、账号、JSON对象、节点信息，参数支持设置默认值。</td></tr><tr><td>20</td><td>支持命令行参数,可指定或不指定命令行参数数量。</td></tr><tr><td>21</td><td>支持自定义工具可设置风险等级。</td></tr><tr><td>22</td><td>支持自定义工具可绑定工具目录。</td></tr><tr><td>23</td><td>支持基于git版本管理，支持基于从Git导入、导出原子操作。</td></tr><tr><td>24</td><td>支持自定义工具支持版本审核发布。</td></tr><tr><td>25</td><td>支持常见的连接协议，如：SSH、WinRM、Tagent、IPMI、HTTP、HTTPS<font class="font6">、Telnet、SNMP、SMI等。</font></td></tr><tr><td>26</td><td>支持连接方式，如：远端目标机器执行、本地执行、本地到远程执行。</td></tr><tr><td>27</td><td>支持自定义原子操作在线测试验证。</td></tr><tr><td>28</td><td>支持远在操作导入、导出，用于在不同环境的环境迁移。</td></tr><tr><td>29</td><td rowspan="17">组合工具</td><td rowspan="17">组合管理</td><td>支持组合管理的增删改查基础管理功能。</td></tr><tr><td>30</td><td>支持自定义场景编排组合，支持图形化拖拉拽布局设计。</td></tr><tr><td>31</td><td>支持复制现有组合编排，用于创建与原组合编排相似的新组合编排。</td></tr><tr><td>32</td><td>支持组合编排内工具自定义若干阶段或阶段组，阶段内的工具支持串行、并行、条件判断。</td></tr><tr><td>33</td><td>支持组合编排阶段内工具全量、分批次、灰度等执行策略。</td></tr><tr><td>34</td><td>支持组合编排内按阶段划分若干逻辑场景，执行时可按逻辑场景快速选择执行。</td></tr><tr><td>35</td><td>支持组合编排阶段作业通知策略。</td></tr><tr><td>36</td><td>支持组合编排阶段内定义1到N个若干个工具库或自定义原子操作。支持工具库或原则操作同阶段内或跨阶段内数据传递。</td></tr><tr><td>37</td><td>支持常见的组合编排作业参数定义，如：文本、文本域、密码、文件、时间、日期、单选、多选、开关、账号、JSON对象、节点信息，以及参数默认值、必填、校验规则等规则设置。</td></tr><tr><td>38</td><td>支持组合编排作业参数与阶段内工具库或自定义操作参数传递。</td></tr><tr><td>39</td><td>支持工具库或自定义原子操作参数引用参数模板，批量修改和赋值。</td></tr><tr><td>40</td><td>支持组合编排授权操作，如：执行权限、修改权限等。</td></tr><tr><td>41</td><td>支持组合编排预设执行目标、以及执行时动态选择执行目标，支持阶段单独设置执行目标且优先级高于编排全局目标。</td></tr><tr><td>42</td><td>支持编排内工具库、自定义原则操作引用作业参数、上游工具输出参数、预设参数集、全局参数、静态参数的定义。</td></tr><tr><td>43</td><td>支持组合编排阶段配置动态执行目标，上游阶段的输出参数作为下游阶段的执行目标。</td></tr><tr><td>44</td><td>支持工具库和自定义原子操作执行策略，如：失败继续还是终止。</td></tr><tr><td>45</td><td>支持组合编排导出、导入，可用于组合流程在不同环境迁移。</td></tr><tr><td>46</td><td rowspan="10">组合执行</td><td rowspan="10">编排执行</td><td>支持组合编排设置为定时执行。</td></tr><tr><td>47</td><td>支持组合编排有权限用户发起自动化作业立即执行。</td></tr><tr><td>48</td><td>支持组合编排设置并发数量设置分批数量，分批次执行。</td></tr><tr><td>49</td><td>支持多种执行目标录入方式，包括直接勾选节点、设置过滤器指定目标范围、文本输入等方式。</td></tr><tr><td>50</td><td>支持动态执行目标设置，引用上游节点输出参数作为执行目标。</td></tr><tr><td>51</td><td>支持失败中止、失败继续执行策略，部分节点失败时，支持人为干预，也支持忽略错误，继续执行。</td></tr><tr><td>52</td><td>支持终止、重跑等操作，重跑支持全部重跑，跳过成功节点重跑。</td></tr><tr><td>53</td><td>支持验证编排执行作业，且验证完成的作业不可以执行重跑、终止等操作。</td></tr><tr><td>54</td><td>支持导出作业结果将作业结果导出为Excel，包含节点结果信息，执行阶段信息，作业输出参数。</td></tr><tr><td>55</td><td>支持查看、导出节点运行日志、输出参数查看、导出执行单节点运行日志。</td></tr><tr><td>56</td><td rowspan="4">执行代理</td><td rowspan="4">Agent</td><td>支持常见操作系统，如：Windows、Linux、AIX。</td></tr><tr><td>57</td><td>支持分布式部署、根据管理网段下发执行。</td></tr><tr><td>58</td><td>支持在线查看状态、日志、配置，也可以在线管理，如：启停。</td></tr><tr><td>59</td><td>支持Agent对操作系统资源极少，资源范围为：cpu &lt;= 2%,内存： &lt;= 200MB。</td></tr></table>

### ⭐️ 巡检
<table><tr><td>编号</td><td>分类</td><td>功能点</td><td>说明</td></tr><tr><td>1</td><td rowspan="15">巡检管理</td><td rowspan="2">巡检定义</td><td>支持对巡检范围内的巡检插件指标进行阀值定义。</td></tr><tr><td>2</td><td>支持以应用角度配置巡检对象阀值定义。</td></tr><tr><td>3</td><td rowspan="6">应用巡检</td><td>支持以应用系统、应用模块、环境树形结构查看应用巡检资源清单。</td></tr><tr><td>4</td><td>支持以单个应用为巡检单位设置应用的定时巡检。</td></tr><tr><td>5</td><td>支持人工发起单个应用、应用某个模块、应用某个环境进行巡检。</td></tr><tr><td>6</td><td>支持以应用巡检导出应用最新问题列表。</td></tr><tr><td>7</td><td>支持用户以邮件方式推送应用巡检问题列表。</td></tr><tr><td>8</td><td>支持以应用角度导出巡检报告。</td></tr><tr><td>9</td><td rowspan="4">资产巡检</td><td>支持以资产、职能岗位角度查看资产巡检对象。</td></tr><tr><td>10</td><td>支持某类资产定时发起巡检。</td></tr><tr><td>11</td><td>支持单个资产对象手工发起巡检。</td></tr><tr><td>12</td><td>支持单个资产对象导出巡检报告。</td></tr><tr><td>13</td><td rowspan="3">配置巡检</td><td>支持对应用、操作系统、网络配置文件备份内容进行巡检。</td></tr><tr><td>14</td><td>支持在资产清单界面，定义巡检配置文件的路径且支持通配符表达式。</td></tr><tr><td>15</td><td>配置文件发生变更时，自动生成版本，且支持配置文件版本差异在线比对。</td></tr><tr><td>16</td><td rowspan="2">巡检方式</td><td rowspan="2">巡检方法</td><td>提供通用的巡检采集插件，匹配用户实际巡检范围进行巡检。</td></tr><tr><td>17</td><td>支持用户定义脚本方式进行巡检，支持常见的脚本语言，包括Python、Ruby、VBScript、Perl、PowerShell、CMD、Bash、csh、ksh、sh、JavaScript。</td></tr><tr><td>18</td><td rowspan="3">巡检结果</td><td rowspan="3">最新问题</td><td>支持按应用、按资产、按巡检状态、以及其他条件快速查找巡检资产。</td></tr><tr><td>19</td><td>支持按类型快速查看资产的问题列表。</td></tr><tr><td>20</td><td>支持最新问题列表导出。</td></tr><tr><td>21</td><td rowspan="12">巡检范围</td><td rowspan="4">应用巡检</td><td>支持HTTP URL模拟:系统可模拟HTTP请求序列，对请求响应状态、请求时间等相关性能指标进行巡检。</td></tr><tr><td>22</td><td>支持ICMP检测:系统模拟ICMP请求序列，返回系统响应时间等指标。</td></tr><tr><td>23</td><td>支持报文序列检测:系统模拟Socket请求，返回系统响应时间、状态等信息。</td></tr><tr><td>24</td><td>模拟用户访问系统，确认系统可用，同时巡检系统访问和功能使用耗时。</td></tr><tr><td>25</td><td>操作系统巡检</td><td>支持多版本的Windows、Linux、AIX操作系统巡检，采集主机运行状态、CPU、内存、存储、I/O、出入口网络流量等数据。</td></tr><tr><td>26</td><td>虚拟化巡检</td><td>支持vCenter、VMware、华为FusionCompute设备虚拟化巡检。</td></tr><tr><td>27</td><td>中间件巡检</td><td>支持weblogic、Tomcat、Apache、Jetty、WebSphere、tuxedo、Nginx等中间件资源的巡检，采集中间件运行状态。</td></tr><tr><td>28</td><td>数据库巡检</td><td>支持Oracle、MySql、SqlServer、MongoDB、PostgreSQL数据库运行状态等。</td></tr><tr><td>29</td><td>网络巡检</td><td>支持网络交换机、F5服务器、防火墙、专线等网络设备/对象的巡检，采集设备/对象运行状态、资源消耗等数据，覆盖常用网络交换机、防火墙品牌。</td></tr><tr><td>30</td><td>容器巡检</td><td>支持Docker运行容器的健康与性能状态巡检，并且支持对docker容器内的应用进行巡检的能力。</td></tr><tr><td>31</td><td>存储巡检</td><td>存储设备需因具体的客户现场环境而定，IBM DS系列、IBM Flash系列、IBM V7000系列、IBM SVC、IBM FlashSystem 900、EMC RPA、EMC VNX、NetApp、HDS VSP系列、HDS AMS系列巡检。</td></tr><tr><td>32</td><td>服务硬件巡检</td><td>支持对Dell、IBM、浪潮、华为等服务厂商基于带外管理网卡进行服务器硬件巡检。</td></tr></table>

### ⭐️ DevOps
<table><tr><td>编号</td><td>分类</td><td>功能点</td><td>说明</td></tr><tr><td>1</td><td rowspan="5">参数</td><td rowspan="2">全局参数</td><td>支持自动化作业全局参数的增删改查基础管理。</td></tr><tr><td>2</td><td>支持全局参数字段文本、密码、日期、文本域等类型。</td></tr><tr><td>3</td><td rowspan="3">预设参数</td><td>支持自动化作业预设参数的增删改查基础管理。</td></tr><tr><td>4</td><td>支持按工具库、原子操作预设参数集。</td></tr><tr><td>5</td><td>支持预设参数集引用全局参数。</td></tr><tr><td>6</td><td rowspan="5">分类</td><td rowspan="3">工具分类</td><td>支持工具分类的增删改查基础管理。</td></tr><tr><td>7</td><td>支持工具库查看统计分类下面的工具库、自定义原子操作、关联编排的数量。</td></tr><tr><td>8</td><td>支持工具库按类型设置工具库的权限。</td></tr><tr><td>9</td><td rowspan="2">工具目录</td><td>支持工具目录的增删改查基础管理。</td></tr><tr><td>10</td><td>支持工具库按目录设置权限。</td></tr><tr><td>11</td><td rowspan="3">场景</td><td rowspan="3">编排场景</td><td>支持自动化场景的增删改查基础管理。</td></tr><tr><td>12</td><td>支持按类型、按场景、按岗位职能定义自动化场景分类。</td></tr><tr><td>13</td><td>支持在自动化的组合编排内，设置不同的使用场景，不同的使用场景包含了编排中的不同执行阶段。场景可以实现一个编排，多种使用场景的需求。</td></tr><tr><td>14</td><td rowspan="15">工具库</td><td rowspan="4">内置工具库</td><td>平台内置常用的基础工具库外，还包括发布模块的文件操作、配置备份、SQL执行、服务启停、WebSphere发布插件、K8s发布插件、Docker发布插件、WebLogic发布插件等。</td></tr><tr><td>15</td><td>支持内置工具在线测试。</td></tr><tr><td>16</td><td>支持内置工具库在线帮助，如输入参数、输出参数、执行方式、风险等级。</td></tr><tr><td>17</td><td>支持内置工具库关联自定义展示模板。</td></tr><tr><td>18</td><td rowspan="11">自定义     原子操作</td><td>支持常见的脚本语言，包括Python、Ruby、VBScript、Perl、PowerShell、CMD、Bash、csh、ksh、sh、JavaScript。</td></tr><tr><td>19</td><td>支持丰富的输入、输出参数类型，包括文本、文本域、密码、文件、时间、日期、单选、多选、开关、账号、JSON对象、节点信息，参数支持设置默认值。</td></tr><tr><td>20</td><td>支持命令行参数,可指定或不指定命令行参数数量。</td></tr><tr><td>21</td><td>支持自定义工具可设置风险等级。</td></tr><tr><td>22</td><td>支持自定义工具可绑定工具目录。</td></tr><tr><td>23</td><td>支持基于git版本管理，支持基于从git导入、导出原子操作。</td></tr><tr><td>24</td><td>支持自定义工具支持版本审核发布。</td></tr><tr><td>25</td><td>支持常见的连接协议，如：SSH、WinRM、Tagent、IPMI、HTTP、HTTPS、Telnet、SNMP、SMI等。</td></tr><tr><td>26</td><td>支持连接方式，如：远端目标机器执行、本地执行、本地到远程执行。</td></tr><tr><td>27</td><td>支持自定义原子操作在线测试验证。</td></tr><tr><td>28</td><td>支持远在操作导入、导出，用于在不同环境的环境迁移。</td></tr><tr><td>29</td><td rowspan="35">发布管理</td><td rowspan="2">发布模板</td><td>支持按类型定义一类的发布场景模板，如WebLogic发布，预定义一类发布模板，单独实例配置时选择对应的模板快速完成发布配置。</td></tr><tr><td>30</td><td>支持常规发布的单实例、集群部署的模板配置，如WebLogic应用更新。</td></tr><tr><td>31</td><td rowspan="9">发布配置</td><td>支持发布模板组合管理的增删改查基础管理功能。</td></tr><tr><td>32</td><td>支持自定义场景发布编排组合，支持图形化拖拉拽布局设计。</td></tr><tr><td>33</td><td>支持复制现有发布组合编排，用于创建与原组合编排相似的新组合编排。</td></tr><tr><td>34</td><td>支持发布组合编排内工具自定义若干阶段或阶段组，阶段内的工具支持串行、并行、条件判断。</td></tr><tr><td>35</td><td>支持发布组合编排阶段内工具全量、分批次、灰度等执行策略。</td></tr><tr><td>36</td><td>支持基础剧本的新增、编辑、删除管理功能，基础剧本可进行可见层级授权，即剧本可设置被调用的层级，包括在子系统层级可见、环境层级可见与实例层级可见。</td></tr><tr><td>37</td><td>编排剧本内可支持对编排工具库和自定义原则操作的直接调用，简化剧本的设计过程。</td></tr><tr><td>38</td><td>支持子系统、系统环境、发布实例下的剧本(Playbook)编排，可对多个剧本进行编排，快速执行剧本的增删改等操作。</td></tr><tr><td>39</td><td>相同剧本在不同层级管理单元之间可继承、重用，且优先级从高到低的顺序为：应用实例层级、子系统环境层级、子系统层级。</td></tr><tr><td>40</td><td>场景预设</td><td>支持一个系统内的发布按场景进行预选设置，比如：只编译、构建、备份、回退、SQL执行、应用启停等。</td></tr><tr><td>41</td><td rowspan="7">编译&amp;构建</td><td>支持从SVN、Git代码仓库开始的应用代码自动化编译打包与自动发布，同时支持从SVN获取数据库脚本、应用配置文件的自动化执行与下发功能。</td></tr><tr><td>42</td><td>支持代码基线自动维护，发布后自动归并修改到基线，编译前自动归并基线上的修改到发布分支。</td></tr><tr><td>43</td><td>支持对JUnit的测试结果数据进行抽取和展示。</td></tr><tr><td>44</td><td>支持对接SonarQube代码扫描，并且出具扫表结果清单。</td></tr><tr><td>45</td><td>具有封版的功能，封版后，需要有权限的用户开版后才能再编译，防止在生产发布前代码存在修改而没有经过测试。</td></tr><tr><td>46</td><td>支持通过Maven、Ant、Gradle等工具自动化编译打包Java应用；支持.Net、C、VC++、Java等应用的自动化编译打包；同时支持自定义编译打包脚本设计功能，以完成应用的自定义编译打包。</td></tr><tr><td>47</td><td>对于编译完成后的应用，平台支持根据自定义需要进行物料整理，并将整理后的物料重新完成自动化打包。</td></tr><tr><td>48</td><td rowspan="2">配置修改/替换</td><td>支持应用配置文件的自动化部署功能，支持配置文件的自动化修改、自动替换。</td></tr><tr><td>49</td><td>支持服务器差异配置的自动配置，支持配置模版功能，配置模版和环境配置结合，生成可以适配到不同的环境(例如：SIT、UAT、PRD)的最终配置文件。</td></tr><tr><td>50</td><td rowspan="6">数据库脚本自动执行</td><td>依据一定的规则，对DB脚本自动识别运行目标和运行顺序串行或并行运行，出错可以交互选择提交或回退。操作可重入，执行成功的脚本不会再执行，能够自动识别修改过的DB脚本并进行提示；自带数据库链接插件，支持MySql、PosgreSQL、Oracle、DB2的发布；DB脚本使用MySql   client，Oracle SQLPlus，db2cli兼容的运行方式，能够在上述工具中运行的DB脚本能够直接在发布工具中执行。</td></tr><tr><td>51</td><td>数据库脚本执行出错时，平台页面支持用户交互，用户可选择提交或者回滚；支持数据库事务rollback/commit实时回显时，支持对关键字及所在行作颜色标记。</td></tr><tr><td>52</td><td>支持多DB脚本的免输入以及免说明的一键发布；支持选择并执行若干个DB脚本</td></tr><tr><td>53</td><td>数据库脚本操作可重入，执行成功的脚本不会重复执行，平台能够自动识别修改过的数据库脚本并给出提示。</td></tr><tr><td>54</td><td>支持数据库脚本的回退功能，明确列出回退脚本列表，用户可以交互式的进行回退操作。并且脚本回退操作可重入，执行成功的脚本不能重复执行。</td></tr><tr><td>55</td><td>针对Oracle数据库，进行失效对象的检查和重新编译处理。</td></tr><tr><td>56</td><td rowspan="8">部署&amp;发布</td><td>支持Linux、Unix、Windows等系统上的的应用自动化部署。</td></tr><tr><td>57</td><td>支持自带部署插件,包含Tomcat、WebLogic和WAS的部署插件，并且支持扩展。</td></tr><tr><td>58</td><td>支持应用批量一键发布功能，即一次发布多个系统版本，应用系统之间需考虑发布优先级。在发布过程中，能够对批量发布中的作业进行暂停、重新运行等操作，以及能够对已修复的作业进行重新发布；</td></tr><tr><td>59</td><td>单个应用的自动化发布与多个应用的一键发布，均需实时显示各个发布过程的状态；</td></tr><tr><td>60</td><td>支持应用版本回退，平台可快速的选择历史任一版本进行回退操作；</td></tr><tr><td>61</td><td>支持应用实例的状态监控，版本发布完成后，平台能够方便的检测与查看应用实例的健康状态。</td></tr><tr><td>62</td><td>支持应用重启操作，可以选择应用的多个实例进行并行或串行的启动与停止操作，且能够提供独立的授权管理功能，可授权给指定用户，便于用户能够自动化部署平台重启应用并查看日志。</td></tr><tr><td>63</td><td>支持待发布物料的完备性、版本一致性等校验，支持数据库脚本的自动扫描。</td></tr><tr><td>64</td><td rowspan="4">代码管理</td><td rowspan="4">代码管理</td><td>支持编译前检查版本分支（集成分支）是否已经合并代码基线（master）的更改。</td></tr><tr><td>65</td><td>支持生产发布并通过验证后，自动触发将对应版本的代码合并回基线（master）。</td></tr><tr><td>66</td><td>支持通过设置Git或者SVN钩子触发版本的自动编译以及SIT环境的自动发布，并能够看到此次编译对应的代码修改的相关描述。</td></tr><tr><td>67</td><td>支持对代码分支自动打标签。</td></tr><tr><td>68</td><td rowspan="4">版本中心</td><td rowspan="2">制品管理</td><td>支持内置应用版本制品管理，生产能够从自身制品中获取版本。</td></tr><tr><td>69</td><td>支持对接nexus等第三方版本制品库。</td></tr><tr><td>70</td><td rowspan="2">版本管理</td><td>支持对发布过的版本代码、SQL、配置文件进行归档。</td></tr><tr><td>71</td><td>支持版本定义、版本开/封版本操作。</td></tr><tr><td>72</td><td rowspan="17">版本发布</td><td rowspan="6">批量发布</td><td>支持基于发布流水线、发布作业进行批量发布编排定义。</td></tr><tr><td>73</td><td>支持多个作业批量执行，在同一批量作业内，可依据应用间逻辑关系进行多个发布作业的串行或并行编排。用户只需一个按钮即可完成整个多个作业的批量执行。</td></tr><tr><td>74</td><td>支持批量发布中的发布任务串行、并行泳道定义和配置。</td></tr><tr><td>75</td><td>支持批量发布中发布任务状态策略，如失败了继续、等待策略等。</td></tr><tr><td>76</td><td></td></tr><tr><td>77</td><td>支持批量发布权限配置。</td></tr><tr><td>78</td><td rowspan="11">一键发布</td><td>支持以应用角度查看发布列表，支持快速搜索功能，通过搜索快速查找应用系统或模块的配置。</td></tr><tr><td>79</td><td>支持发布回退。</td></tr><tr><td>80</td><td>支持按场景或全量发布配置方式执行。</td></tr><tr><td>81</td><td>可以选择性的执行现有的自动部署流程中的部分步骤。</td></tr><tr><td>82</td><td>支持基于应用的服务器实例进行多个操作剧本的外循环运行，可以在外循环某一个循环中执行DB脚本。</td></tr><tr><td>83</td><td>支持作业任务的定时执行，平台支持定期与按周期执行作业，按照设定的触发规则自动创建与运行作业，便于测试环境的自动发布。</td></tr><tr><td>84</td><td>支持应用日志的查看功能，用户能够在自动化部署平台内实时查看与下载应用日志文件。</td></tr><tr><td>85</td><td>支持白盒发布功能，即发布过程中每一个动作，系统均需能够实时滚动式的展示运行日志。</td></tr><tr><td>86</td><td>支持查看发布过程操作日志记录，每一个发布步骤均需有日志信息，记录信息包括但不限于以下内容：操作人、操作时间与操作耗时等；并且操作日志可导出，以供审查。</td></tr><tr><td>87</td><td>支持发布日志实时滚动打印，对于异步启动的应用，能够实时tail日志一直到应用启动完成。</td></tr><tr><td>88</td><td>所有实时显示的状态日志，需以不同颜色区分错误信息、告警信息与正常信息等，方便用户识别。</td></tr></table>

### ⭐️ 研发管理
<table>
    <tr>
        <td>编号</td>
        <td>分类</td>
        <td>功能点</td>
        <td>说明</td>
        <td>开源</td>
    </tr>
    <tr>
        <td>1</td>
        <td>系统管理</td>
        <td>项目管理</td>
        <td>管理所有的项目，包括项目基本信息、应用设置，以及结束、删除项目，或把项目保存为新模板。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>2</td>
        <td></td>
        <td>优先级管理</td>
        <td>支持排序，用于需求、缺陷、任务定义紧急程度。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>3</td>
        <td></td>
        <td>模板管理</td>
        <td>支持自定义项目的模板，自定义启停应用设置和排序，自定义应用设置中的属性和状态配置。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>4</td>
        <td>项目</td>
        <td>支持动态管理项目</td>
        <td>项目的应用设置支持动态设置，动态控制项目的成员</td>
<td>✅</td>
    </tr>
    <tr>
        <td>5</td>
        <td></td>
        <td>项目支持迭代计划功能</td>
        <td>包括对迭代的增删改，支持在迭代中配置需求、任务、缺陷、测试计划和测试用例，支持启停迭代。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>6</td>
        <td></td>
        <td>项目可管理需求、任务、缺陷、测试计划等模块。</td>
        <td>支持对项目公共的需求、任务、缺陷、测试计划、测试用例进行增删改操作</td>
<td>✅</td>
    </tr>
    <tr>
        <td>7</td>
        <td></td>
        <td>支持建立需求与任务、缺陷等数据间的互联</td>
        <td>需求详情中可以快速创建或关联任务、缺陷和测试用例，任务、缺陷和测试用例的详情也可以快速关联需求</td>
<td>✅</td>
    </tr>
    <tr>
        <td>8</td>
        <td></td>
        <td>测试计划支持关联测试用例</td>
        <td>测试计划可快速创建或关联测试用例，且支持批量处理测试用例，并跟踪完成度。</td>
<td>❌</td>
    </tr>
    <tr>
        <td>9</td>
        <td></td>
        <td>支持流转处理</td>
        <td>需求、任务、缺陷等状态支持流转并分配新的处理人，直至关闭状态。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>10</td>
        <td></td>
        <td>支持甘特图</td>
        <td>需求、缺陷、任务支持以甘特图查看</td>
<td>❌</td>
    </tr>
    <tr>
        <td>11</td>
        <td></td>
        <td>列表支持字段设置</td>
        <td>所有列表都支持设置字段，包括修改字段的顺序和是否展示。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>12</td>
        <td></td>
        <td>支持按照条件组合过滤列表数据</td>
        <td>列表的数据支持复合搜索，搜索条件包括关键字、优先级、状态、创建日期等。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>13</td>
        <td></td>
        <td>支持查看数据详情并修改</td>
        <td>可查看需求、任务、缺陷等的详细信息，并在详情页面修改数据和关联项。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>14</td>
        <td></td>
        <td>支持关注功能</td>
        <td>可在详情页面关注数据，方便之后快速查看。</td>
<td>✅</td>
    </tr>
    <tr>
        <td>15</td>
        <td></td>
        <td>支持关联代码库，gitlab、github等</td>
        <td></td>
<td>❌</td>
    </tr>
    <tr>
        <td>16</td>
        <td>工作台</td>
        <td>我的待办</td>
        <td>我的待办列表展示所有处理人是当前登录人的需求、任务、缺陷、测试计划和测试用例</td>
<td>✅</td>
    </tr>
    <tr>
        <td>17</td>
        <td></td>
        <td>我的已办</td>
        <td>我的已办列表展示所有当前登录人处理过的需求、任务、缺陷、测试计划和测试用例</td>
<td>✅</td>
    </tr>
    <tr>
        <td>18</td>
        <td></td>
        <td>我的上报</td>
        <td>我的上报列表展示所有创建人是当前登录人的需求、任务、缺陷、测试计划和测试用例</td>
<td>✅</td>
    </tr>
    <tr>
        <td>19</td>
        <td></td>
        <td>我的关注</td>
        <td>我的上报列表展示所有当前登录人已关注的需求、任务、缺陷、测试计划和测试用例</td>
<td>✅</td>
    </tr>
 <tr>
        <td>20</td>
        <td>仪表板</td>
        <td>数据仪表板</td>
        <td>通过图标的方式展示项目相关数据</td>
<td>❌</td>
    </tr>
</table>

## 研发环境搭建

[点击查看搭建说明](CODE-BUILD.md)

## 在线演示

 [点击注册使用](http://demo.neatlogic.com:9098/neatlogic)
 >- 注册激活使用,资源有限,名额先到先得. 
 >- 注册后如果没有额度,则会在有名额时邮件通知
 >- 如果连续3天没使用,则会回收资源,如仍需使用则需要重新发邮件激活

## 构建教程
[点击查看构建说明](WAR_INSTALL.md)

## 本地部署

准备中...

## Docker部署

[点击查看Docker部署说明](QUICK_START.md)

## 技术交流
- **企业微信** <br>
<p align="left"><img src="https://gitee.com/neat-logic/neatlogic-itom-all/raw/develop3.0.0/README_IMAGES/contact_me.png" width="200" /></p>
 **原交流群用户已满，正在处理中，如需交流请加以下微信群目前企业微信。** 
<p align="left"><img src="https://gitee.com/neat-logic/neatlogic-itom-all/raw/develop3.0.0/README_IMAGES/wechat.jpg" width="250" /></p>
