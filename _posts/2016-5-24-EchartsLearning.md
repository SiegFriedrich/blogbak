---
layout: post
title: "Echarts学习笔记"
date: 2016-5-24
description: "搭建自己的博客"
tag: 前端美化
---


### 模块化单文件引入

1.新建一个echarts.html文件，为ECharts准备一个具备大小（宽高）的Dom
```
	<!DOCTYPE html>
	<head>
	    <meta charset="utf-8">
	    <title>ECharts</title>
	</head>
	<body>
	    <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
	    <div id="main" style="height:400px"></div>
	</body>
```

2、新建``<script>``标签引入模块化单文件echarts.js
```
	<!DOCTYPE html>
	<head>
	    <meta charset="utf-8">
	    <title>ECharts</title>
	</head>
	<body>
	    <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
	    <div id="main" style="height:400px"></div>
	    <!-- ECharts单文件引入 -->
	    <script src="http://echarts.baidu.com/build/dist/echarts.js"></script>
	</body>
```
3、新建``<script>``标签中为模块加载器配置echarts和所需图表的路径（相对路径为从当前页面链接到echarts.js），引入图表文件见引入ECharts2
```
	<!DOCTYPE html>
	<head>
		<meta charset="utf-8">
		<title>ECharts</title>
	</head>
	<body>
		<!-- 为ECharts准备一个具备大小（宽高）的Dom -->
		<div id="main" style="height:400px"></div>
		<!-- ECharts单文件引入 -->
		<script src="http://echarts.baidu.com/build/dist/echarts.js"></script>
		<script type="text/javascript">
				// 路径配置
				require.config({
						paths: {
								echarts: 'http://echarts.baidu.com/build/dist'
						}
				});
		</script>
	</body>
```
4、``<script>``标签内动态加载echarts和所需图表，回调函数中可以初始化图表并驱动图表的生成
```
	<!DOCTYPE html>
	<head>
	  <meta charset="utf-8">
	  <title>ECharts</title>
	</head>
	<body>
	  <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
	  <div id="main" style="height:400px"></div>
	  <!-- ECharts单文件引入 -->
	  <script src="http://echarts.baidu.com/build/dist/echarts.js"></script>
	  <script type="text/javascript">
	      // 路径配置
	      require.config({
	          paths: {
	              echarts: 'http://echarts.baidu.com/build/dist'
	          }
	      });

	      // 使用
	      require(
	          [
	              'echarts',
	              'echarts/chart/bar' // 使用柱状图就加载bar模块，按需加载
	          ],
	          function (ec) {
	              // 基于准备好的dom，初始化echarts图表
	              var myChart = ec.init(document.getElementById('main'));

	              var option = {
	                  tooltip: {
	                      show: true
	                  },
	                  legend: {
	                      data:['销量']
	                  },
	                  xAxis : [
	                      {
	                          type : 'category',
	                          data : ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
	                      }
	                  ],
	                  yAxis : [
	                      {
	                          type : 'value'
	                      }
	                  ],
	                  series : [
	                      {
	                          "name":"销量",
	                          "type":"bar",
	                          "data":[5, 20, 40, 10, 10, 20]
	                      }
	                  ]
	              };

	              // 为echarts对象加载数据
	              myChart.setOption(option);
	          }
	      );
	  </script>
	</body>
```
5、浏览器中打开echarts.html，就可看到效果
