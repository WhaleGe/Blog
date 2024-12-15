---
title: 差异基因火山图
categories:
  - code
  - R
tags:
  - R
abbrlink: 6c0be4fd
date: 2023-05-22 18:07:48
---

## 差异基因画火山图 

<!--more-->

```R
indir <- "/path"
degfile <- "deg.txt"
id <- "deg_vol"
la1 <- "control" 
la2 <- "case"
day <- "230522"

library(dplyr)
library(ggplot2)
library(ggrepel)
setwd(indir)

### 读取数据
df <- read.delim(degfile,sep="\t",header=TRUE,check.names=FALSE)
df$avg_log2FC <- ifelse(df$cluster==la2,-df$avg_log2FC,df$avg_log2FC )
head(df)


### 阈值确定：
pvalue = 0.05
log2FC = 1

### 根据阈值添加上下调分组标签：
df$group <- case_when(
    df$avg_log2FC > log2FC & df$p_val_adj < pvalue & df$cluster==la1 ~ la1,
    df$avg_log2FC < -log2FC & df$p_val_adj < pvalue & df$cluster==la2 ~ la2,
    TRUE ~ 'none'
)
head(df)


### 转换为因子，指定绘图顺序；
df$'-log10(p_val_adj)' <- -log10(df$p_val_adj) #新增-log10p列
df$group <- factor(df$group, levels = c(la1,la2,"none"))


#自定义颜色：
mycol <- c("#EB4232","#2DB2EB","#d8d8d8")
#自定义主题：
mytheme <- theme_classic() +
    theme(axis.title = element_text(size = 15),
          axis.text = element_text(size = 14),
          legend.text = element_text(size = 14),
		  plot.margin = margin(15,5.5,5.5,5.5))

### 绘制基本图形
p <- ggplot(data = df,aes(x = avg_log2FC, y = -log10(p_val_adj),color = group)) + #建立映射
    geom_point(size = 2.2) + #绘制散点
    scale_colour_manual(name = "", values = alpha(mycol, 0.7)) + #自定义散点颜色
    scale_x_continuous(limits = c(-5, 5),breaks = seq(-5, 5, by = 2)) + #x轴限制
    scale_y_continuous(expand = expansion(add = c(2, 0)),limits = c(0, 40),
                       breaks = seq(0, 40, by = 10)) + #y轴限制
    geom_hline(yintercept = c(-log10(pvalue)),linewidth = 0.7,color = "black",lty = "dashed") + #水平阈值线
    geom_vline(xintercept = c(-log2FC, log2FC),linewidth = 0.7,color = "black",lty = "dashed") + #垂直阈值线
    mytheme
p


### 分别筛选两个分组中的满足条件的基因, 此处通过阈值筛选
up <- filter(df, group == la1) %>% distinct(gene, .keep_all = T) %>% filter(avg_log2FC > log2FC & p_val_adj < pvalue)
down <- filter(df, group == la2) %>% distinct(gene, .keep_all = T) %>% filter(avg_log2FC < -log2FC  & p_val_adj < pvalue)
head(up);head(down)


### 个性化调整：对齐基因标签
p1 <- p +
    geom_point(data = up,
               aes(x = avg_log2FC , y = -log10(p_val_adj)),
               color = '#EB4232', size = 4.5, alpha = 0.2) +
    geom_text_repel(data = up,
                    aes(x = avg_log2FC , y = -log10(p_val_adj), label = gene),
                    seed = 233,size = 2.5,color = 'black',min.segment.length = 0,
                    force = 2,force_pull = 2,box.padding = 0.1, max.overlaps = Inf,
                    segment.linetype = 3, #线段类型,1为实线,2-6为不同类型虚线
                    segment.color = 'black', #线段颜色
                    segment.alpha = 0.5, #线段不透明度
                    nudge_x = 3 - up$avg_log2FC , #标签x轴起始位置调整
                    direction = "y", #按y轴调整标签位置方向，若想水平对齐则为x
                    hjust = 0 #对齐标签：0右对齐，1左对齐，0.5居中
                    )

p2 <- p1 +
    geom_point(data = down,aes(x = avg_log2FC , y = -log10(p_val_adj)),
               color = '#2DB2EB',size = 4.5, alpha = 0.2) +
    geom_text_repel(data = down,
                    aes(x = avg_log2FC , y = -log10(p_val_adj), label = gene),
                    seed = 233,size = 2.5,color = 'black',min.segment.length = 0,
                    force = 2,force_pull = 2,box.padding = 0.1,max.overlaps = Inf,
                    segment.linetype = 3,segment.color = 'black',segment.alpha = 0.8,
                    nudge_x = -3 - down$avg_log2FC ,direction = "y",
                    hjust = 1 #改为左对齐即可
                    )

ggsave(filename = paste0(id,"_",day,".pdf"),plot = p2,width = 7,height = 5.5)
```

