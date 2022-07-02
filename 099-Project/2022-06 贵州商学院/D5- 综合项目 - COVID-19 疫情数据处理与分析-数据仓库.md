---
title: 综合项目 - COVID-19 疫情数据处理与分析大屏
share: true
description:  
category: 贵州商学院实习
daily: false
createtime: 2022-06-26 16:14
tags:
- 贵州商学院
---
```toc
```
## Summary
从五个主题、分析

## 数据概况
三个数据集，每个数据分别存储全球确诊、死亡、治愈数据。
![static/attachment/Pasted image 20220627084334.png](static/attachment/Pasted%20image%2020220627084334.png)

数据字段
![static/attachment/Pasted image 20220627084512.png](static/attachment/Pasted%20image%2020220627084512.png)


### 数仓的建立
ODS（Operational Data Stored）：数据源中的数据，经过**抽取、转换、加载**，也就说传说中的 ETL 之后，装入本层。本层的数据，总体上大多是按照源头业务系统的分类方式而分类的。
DW（Data Warehouse））：从 ODS 层中获得的数据**按主题**建立各种数据模型，**复用性强**。
APP（Application）：要求比**较快的响应速度**，**复用性不强**
- 建立数据仓库 
	- 数据源层（ODS，Operational Data Stored）：存储原始数据
		- 借还信息
		- 读者信息
	- 数据仓库（DW, Data Warehouse）：存储主题数据
		- 读者主题
		- 书籍主题
	- 应用APP（Application）
		- 读者画像
		- 数目
	- DIM
		- 省市转换

![[Excalidraw/D5- 综合项目 - COVID-19 疫情数据处理与分析-数据仓库 2022-06-28 08.20.24.excalidraw]]
## 思路框架
![[Excalidraw/D5- 综合项目 - COVID-19 疫情数据处理与分析-数据仓库 2022-06-29 08.26.01.excalidraw]]

## 实验步骤
- 数据加载 ✔
	- 将数据导入到ODS层（死亡、治愈、确诊） ✔
		- 数据处理（将原始数据转换为 `国家，省份，经度，纬度，日期，治愈人数`）
			- 将横纵坐标交换（data.T, 转置）（将日期放到纵轴）
				- 每一列：表示每一个国家每个日期的治愈人数
			- 拼装的目标格式：
	- 加载了DIM数据（映射数据） ✔
		- 对应关系数据
			- 1 对应为 男
			- 2 对应为 女

- ODS->DW（**复用性强**)
	- 按**主题**抽取和整理转换
		- 抽取：获取数据并对数据进行一定处理
		- 转换：英文的国家、省转换（映射）为中文
		- 加载：将转换好的数据存储到DW
	- 主题1
		- **每个国家总计确诊、死亡、治愈人数**（主题）（主题=多个分析点的集合）
		- 实战步骤
			- 从ODS层抽取数据  ✔
			- 从DIM层抽取国家数据（映射数据）  ✔
			- 将英文的国家映射为中文的国家  ✔
			- 统计每个国家**每年每月**的确诊、死亡、治愈人数  ✔
			- 存储到 DW ✔
				- 建立主题表
					- 字段：`国家、时间（年）、时间（月）、确诊、死亡、治愈人数`

- APP（**较快的响应速度**，**复用性不强**）
	- 在 DW 层的基础上，按三个维度进行汇总
		- 按年统计确诊、死亡、治愈人数


## 实战
- 每个国家的死亡人数汇总（柱状图）
- 2020年/2021年总死亡人数（指标卡） 
- 总死亡人数
## 数据分析
**数据仓库即** Data Warehouse ，是为企业所有级别的决策制定过程，提供所有类型数据支持的战略集合。它是单个数据存储，出于分析性报告和决策支持目的而创建。 为需要业务智能的企业，提供指导业务流程改进、监视时间、成本、质量以及控制。

简单来说，DW 的本质也是一个数据库，但是，他是针对数据分析的业务场景而设定的。传统的数据库，是为了支撑实际业务如商城而建立的，针对查询量小（单条数据如查询用户信息）的业务场景。数据仓库是为了做数据分析的，针对的是查询量大（每次查询几万甚至几百万条数据）的业务场景。

- 全球疫情情况（按月建立数据仓库）（主题1）
	- 总确诊人数
	- 总死亡人数
	- 总治愈人数
	- 治愈率
	- 死亡率
- 全球疫情形式（按月建立数据仓库）（主题2）
	- x：日期
	- y：日新增人数、日确诊人数、日死亡人数
- 排行（按月建立数据仓库）（主题3）
	- 各国确诊人数TOP20
	- 各国死亡人数TOP20
	- 各国治愈人数TOP20
- 中国疫情现状TOP20（按月建立数据仓库）（主题4）
	- 确诊
	- 死亡
	- 治愈
- 中国疫情趋势（按月建立数据仓库）（主题5）
	- ![static/attachment/Pasted image 20220627085626.png](static/attachment/Pasted%20image%2020220627085626.png)

## 地图数据
```python
country_map = {
    'Singapore Rep.': '新加坡', 'Dominican Rep.': '多米尼加', 'Palestine': '巴勒斯坦', 'Bahamas': '巴哈马', 'Timor-Leste': '东帝汶',
    'Afghanistan': '阿富汗', 'Guinea-Bissau': '几内亚比绍', "Côte d'Ivoire": '科特迪瓦', 'Siachen Glacier': '锡亚琴冰川',
    "Br. Indian Ocean Ter.": '英属印度洋领土', 'Angola': '安哥拉', 'Albania': '阿尔巴尼亚', 'United Arab Emirates': '阿联酋',
    'Argentina': '阿根廷', 'Armenia': '亚美尼亚', 'French Southern and Antarctic Lands': '法属南半球和南极领地', 'Australia': '澳大利亚',
    'Austria': '奥地利', 'Azerbaijan': '阿塞拜疆', 'Burundi': '布隆迪', 'Belgium': '比利时', 'Benin': '贝宁', 'Burkina Faso': '布基纳法索',
    'Bangladesh': '孟加拉国', 'Bulgaria': '保加利亚', 'The Bahamas': '巴哈马', 'Bosnia and Herz.': '波斯尼亚和黑塞哥维那', 'Belarus': '白俄罗斯',
    'Belize': '伯利兹', 'Bermuda': '百慕大', 'Bolivia': '玻利维亚', 'Brazil': '巴西', 'Brunei': '文莱', 'Bhutan': '不丹',
    'Botswana': '博茨瓦纳', 'Central African Rep.': '中非', 'Canada': '加拿大', 'Switzerland': '瑞士', 'Chile': '智利',
    'China': '中国', 'Ivory Coast': '象牙海岸', 'Cameroon': '喀麦隆', 'Dem. Rep. Congo': '刚果民主共和国', 'Congo': '刚果',
    'Colombia': '哥伦比亚', 'Costa Rica': '哥斯达黎加', 'Cuba': '古巴', 'N. Cyprus': '北塞浦路斯', 'Cyprus': '塞浦路斯', 'Czech Rep.': '捷克',
    'Germany': '德国', 'Djibouti': '吉布提', 'Denmark': '丹麦', 'Algeria': '阿尔及利亚', 'Ecuador': '厄瓜多尔', 'Egypt': '埃及',
    'Eritrea': '厄立特里亚', 'Spain': '西班牙', 'Estonia': '爱沙尼亚', 'Ethiopia': '埃塞俄比亚', 'Finland': '芬兰', 'Fiji': '斐',
    'Falkland Islands': '福克兰群岛', 'France': '法国', 'Gabon': '加蓬', 'United Kingdom': '英国', 'Georgia': '格鲁吉亚',
    'Ghana': '加纳', 'Guinea': '几内亚', 'Gambia': '冈比亚', 'Guinea Bissau': '几内亚比绍', 'Eq. Guinea': '赤道几内亚', 'Greece': '希腊',
    'Greenland': '格陵兰', 'Guatemala': '危地马拉', 'French Guiana': '法属圭亚那', 'Guyana': '圭亚那', 'Honduras': '洪都拉斯',
    'Croatia': '克罗地亚', 'Haiti': '海地', 'Hungary': '匈牙利', 'Indonesia': '印度尼西亚', 'India': '印度', 'Ireland': '爱尔兰',
    'Iran': '伊朗', 'Iraq': '伊拉克', 'Iceland': '冰岛', 'Israel': '以色列', 'Italy': '意大利', 'Jamaica': '牙买加', 'Jordan': '约旦',
    'Japan': '日本', 'Kazakhstan': '哈萨克斯坦', 'Kenya': '肯尼亚', 'Kyrgyzstan': '吉尔吉斯斯坦', 'Cambodia': '柬埔寨', 'Korea': '韩国',
    'Kosovo': '科索沃', 'Kuwait': '科威特', 'Lao PDR': '老挝', 'Lebanon': '黎巴嫩', 'Liberia': '利比里亚', 'Libya': '利比亚',
    'Sri Lanka': '斯里兰卡', 'Lesotho': '莱索托', 'Lithuania': '立陶宛', 'Luxembourg': '卢森堡', 'Latvia': '拉脱维亚', 'Morocco': '摩洛哥',
    'Moldova': '摩尔多瓦', 'Madagascar': '马达加斯加', 'Mexico': '墨西哥', 'Macedonia': '马其顿', 'Mali': '马里', 'Myanmar': '缅甸',
    'Montenegro': '黑山', 'Mongolia': '蒙古', 'Mozambique': '莫桑比克', 'Mauritania': '毛里塔尼亚', 'Malawi': '马拉维',
    'Malaysia': '马来西亚', 'Namibia': '纳米比亚', 'New Caledonia': '新喀里多尼亚', 'Niger': '尼日尔', 'Nigeria': '尼日利亚',
    'Nicaragua': '尼加拉瓜', 'Netherlands': '荷兰', 'Norway': '挪威', 'Nepal': '尼泊尔', 'New Zealand': '新西兰', 'Oman': '阿曼',
    'Pakistan': '巴基斯坦', 'Panama': '巴拿马', 'Peru': '秘鲁', 'Philippines': '菲律宾', 'Papua New Guinea': '巴布亚新几内亚',
    'Poland': '波兰', 'Puerto Rico': '波多黎各', 'Dem. Rep. Korea': '朝鲜', 'Portugal': '葡萄牙', 'Paraguay': '巴拉圭',
    'Qatar': '卡塔尔', 'Romania': '罗马尼亚', 'Russia': '俄罗斯', 'Rwanda': '卢旺达', 'W. Sahara': '西撒哈拉', 'Saudi Arabia': '沙特阿拉伯',
    'Sudan': '苏丹', 'S. Sudan': '南苏丹', 'Senegal': '塞内加尔', 'Solomon Is.': '所罗门群岛', 'Sierra Leone': '塞拉利昂',
    'El Salvador': '萨尔瓦多', 'Somaliland': '索马里兰', 'Somalia': '索马里', 'Serbia': '塞尔维亚', 'Suriname': '苏里南',
    'Slovakia': '斯洛伐克', 'Slovenia': '斯洛文尼亚', 'Sweden': '瑞典', 'Swaziland': '斯威士兰', 'Syria': '叙利亚', 'Chad': '乍得',
    'Togo': '多哥', 'Thailand': '泰国', 'Tajikistan': '塔吉克斯坦', 'Turkmenistan': '土库曼斯坦', 'East Timor': '东帝汶',
    'Trinidad and Tobago': '特里尼达和多巴哥', 'Tunisia': '突尼斯', 'Turkey': '土耳其', 'Tanzania': '坦桑尼亚', 'Uganda': '乌干达',
    'Ukraine': '乌克兰', 'Uruguay': '乌拉圭', 'United States': '美国', 'Uzbekistan': '乌兹别克斯坦', 'Venezuela': '委内瑞拉',
    'Vietnam': '越南', 'Vanuatu': '瓦努阿图', 'West Bank': '西岸', 'Yemen': '也门', 'South Africa': '南非', 'Zambia': '赞比亚',
    'Zimbabwe': '津巴布韦', 'Comoros': '科摩罗'
}

province_map = {
    'Anhui': '安徽', 'Beijing': '北京', 'Chongqing': '重庆', 'Fujian': '新疆', 'Gansu': '甘肃', 'Guangdong': '广东',
    'Guangxi': '广西', 'Guizhou': '贵州', 'Hainan': '海南', 'Hebei': '河北', 'Heilongjiang': '黑龙江', 'Henan': '河南',
    'Hong Kong': '香港', 'Hubei': '湖北', 'Hunan': '湖南', 'Inner Mongolia': '内蒙古', 'Jiangsu': '江苏',
    'Jiangxi': '江西', 'Jilin': '吉林', 'Liaoning': '辽宁', 'Macau': '澳门', 'Ningxia': '宁夏', 'Qinghai': '青海',
    'Shaanxi': '陕西', 'Shandong': '山东', 'Shanghai': '上海', 'Shanxi': '山西', 'Sichuan': '四川', 'Tianjin': '天津',
    'Tibet': '西藏', 'Xinjiang': '新疆', 'Yunnan': '云南', 'Zhejiang': '浙江', 'Fujian':'福建', 'Taiwan': '台湾'
} 
```