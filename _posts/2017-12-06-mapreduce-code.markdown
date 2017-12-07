---
layout: post
title:  "Windows环境下eclipse开发MapReduce程序"
date:   2017-12-06 16:00:00 +0800
tag: MapReduce
category: 2017年12月
description: "在windows平台下使用eclipse开发MapReduce程序"
keywords: MapReduce,eclipse,windows
type: coding
user: kevis
---
### 下载hadoop

从Apache Download下载[hadoop](https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/)后，直接解压到本地。

### 下载插件

下载[Hadoop2.x-eclipse-plugin.jar](https://pan.baidu.com/s/1c2dcs3u)，将其放入 eclipse 的 \plugins 目录，并重启 eclipse，在 Window—>Show View—>Other 将会看到有 Map/Reduce 视图，同时左侧工程空间出现 DFS Locations 类似文件夹的东西。

### 配置hadoop安装路径

```
打开eclipse->window->preferences,搜索hadoop,给hadoop Map/Reduce 配置本地hadoop安装位置（即第一步解压后的路径）
```

### 配置文件系统

```
打开Map/Reduce Locations视图，配置hadoop location。你将会看到DFS Master 与 Map/Reduce Master。在DFS Master勾选 Use M/R Master host,然后根据你的集群hadoop配置文件来配置这里DFS Master与Map/Reduce Master的host与port。配置成功后，刷新DFS Locations，你将会看到你集群的DFS文件系统。
```

### 编写Map/Reduce程序

```
新建Maven项目MapR
根据hadoop MapReduce提供的hadoop-mapreduce-examples-2.x.x.jar包，根据其中的pom.xml修改新建项目中MapR的pom.xml。(注意版本号的修改)
新建WordCount.java，单词统计（运行时参数：输入文件路径，输出文件路径）

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

	public static void main(String[] args) throws Exception {

		Configuration conf = new Configuration();
		String[] otherArgs = (new GenericOptionsParser(conf,args)).getRemainingArgs();
		
		if(otherArgs.length < 2) {
			System.err.println("Usage:wordcount <in> [<in>...] <out>");
	        System.exit(2); 
		}
		
		Job job = Job.getInstance(conf,"word count");        //设置环境参数
		job.setJarByClass(WordCount.class);                     //设置整个程序的类名   
		job.setMapperClass(WordCount.TokenizerMapper.class);    //添加Mapper类
		job.setCombinerClass(IntSumReduce.class);           
		job.setReducerClass(WordCount.IntSumReduce.class);      //添加Reducer类  
		job.setOutputKeyClass(Text.class);                      //设置输出类型
		job.setOutputValueClass(IntWritable.class);             //设置输出类型 
		
		for (int i = 0; i < otherArgs.length-1; i++) {
			FileInputFormat.addInputPath(job, new Path(otherArgs[i]));
		}
		
		FileOutputFormat.setOutputPath(job, new Path(otherArgs[otherArgs.length-1]));	
		System.exit(job.waitForCompletion(true)?0:1);
	}

	public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable>{
		private static final IntWritable one = new IntWritable(1);
        private Text word = new Text();	
        
        public TokenizerMapper(){
        	
        }
        
      
        protected void map(Object key, Text value,
        		Mapper<Object, Text, Text, IntWritable>.Context context)
        		throws IOException, InterruptedException {
        	// TODO Auto-generated method stub
            StringTokenizer itr = new StringTokenizer(value.toString());
            while(itr.hasMoreTokens()){
            	this.word.set(itr.nextToken());
            	context.write(this.word, one);
            }
        }
	}
	
	public static class IntSumReduce extends Reducer<Text, IntWritable, Text, IntWritable>{
		private IntWritable result = new IntWritable();
		
		public IntSumReduce(){
			
		}
		
		protected void reduce(Text key, Iterable<IntWritable> values,
				Reducer<Text, IntWritable, Text, IntWritable>.Context context)
				throws IOException, InterruptedException {
			// TODO Auto-generated method stub
			int sum = 0;
			for (IntWritable intWritable : values) {
				sum += intWritable.get();
			}
			this.result.set(sum);
			context.write(key, this.result);
		}
	}
}

配置运行时参数
    输入文件与输出文件可以是本地文件也可以是集群上的文件， 比如本地输入文件（D：/input），集群输入文件（hdfs://192.168.182.128:8020/input）。
```

### windows平台hadoop环境配置

下载windows平台运行hadoop需要的库[hadoop-common-2.7.1-bin](https://github.com/SweetInk/hadoop-common-2.7.1-bin)。然后将winutils.exe,libwinutils.lib拷贝到本地hadoop的bin目录下，将hadoop.dll拷贝到c:\windows\system32目录中。


### 执行报错

```
错误信息：(null) entry incommand string: null chmod 0700
解决：确保将winutils.exe,libwinutils.lib拷贝到本地hadoop的bin目录下，hadoop.dll拷贝到c:\windows\system32目录中。
错误情况：eclipse打开hdfs文件显示 could not obtain block
解决：虚拟机关闭防火墙
错误情况：mapReduce程序结果输出文件无内容
解决：确保eclipse Map/Reduce Locations->Edit Hadoop Location-> Advanced parameters 中 各参数 ip 为集群对应节点ip。集群中hadoop的配置文件将hostname、localhost、0.0.0.0也换成IP。重启集群与eclipse，执行M/R程序。 
```
  


