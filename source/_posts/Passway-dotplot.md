---
title: 通路(挑选)点图
categories:
  - code
  - R
tags:
  - R
abbrlink: d1d177a0
date: 2023-05-22 18:16:22
---

##  挑选通路画DotPlot

<!--more-->

```R
indir <- "/path"
enrich <- "deg_Enrich.23522.xlsx"
sel <- "deg_Enrich.select.230522.txt"
id <- "deg_Enrich_Select"
day <- "230522"

### 加载R包
library(ggplot2)
library(readxl)
setwd(indir)

### 导入数据, 合并所有的sheet
tab <- read_xlsx(enrich,sheet=1,col_names = TRUE)
for (i in 2:4) {
    temp <- read_xlsx(enrich,sheet=i,col_names = TRUE)
    tab <- rbind(tab,temp) 
}

### 筛选出一些通路
select <- read.delim(sel,sep="\t",header=TRUE,check.names=FALSE)
tab <- tab[tab$ID %in% select$ID,]


# 替换列名中的.
names(tab) <- gsub("\\.","_",names(tab))
# 将基因集的名称中的KEGG和Hallmark去掉，并将短杠替换为空格
tab$Description <- gsub("_"," ",gsub("HALLMARK_","H:",tab$Description))
tab$Description <- ifelse(grepl("hsa",tab$ID),paste0("K:",tab$Description),tab$Description)
tab$Description <- ifelse(grepl("R-HSA",tab$ID),paste0("R:",tab$Description),tab$Description)
### 计算基因比例
tab$gene.s <- as.numeric(sapply(stringr::str_split(tab$GeneRatio,pattern = "/",n = 2),"[",1))
tab$gene.a <- as.numeric(sapply(stringr::str_split(tab$GeneRatio,pattern = "/",n = 2),"[",2))
tab$GRatio <- tab$gene.s/tab$gene.a
tab$Description <- factor(tab$Description,levels = rev(unique(tab$Description[order(tab$Cluster,-tab$GRatio)])))


### 进行画图
pdf(paste0(id,"_",day,".pdf"),5,7)
ggplot(tab,aes(x=Cluster,y=Description,size=GRatio,color=qvalue))+
    geom_point(stat="identity")+ylab("")+xlab("")+
    scale_y_discrete(labels = function(x) stringr::str_wrap(x, width = 70))+
    scale_color_gradientn(colors=c("#d93f09","blue"))+
    theme(panel.grid.major=element_line(colour = "gray90"),
          panel.background=element_rect(fill='transparent',color ="black"),
          axis.text.y = element_text(face = "bold",color ="black",size = 8))+
    scale_size_continuous(range = c(3,7)) #控制点的大小,防止极值过大
dev.off()

```

