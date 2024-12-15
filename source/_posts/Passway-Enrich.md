---
title: 通路富集
categories:
  - code
  - R
tags:
  - R
abbrlink: c093987d
date: 2023-05-22 17:58:06
---

##  通路富集

<!--more-->

```R
indir <- "/path"
genelist <- "deg.txt"
pre <- "deg"
day <- "230522"

### load R packages
library(dplyr)
library(Seurat)
library(clusterProfiler)
library(enrichplot)
library(ggplot2)
library(org.Hs.eg.db)
library(reactome.db)
library(ReactomePA)
library(msigdbr)
library(openxlsx)
setwd(indir)

### 提取H库
m_t2g <- msigdbr(species = "Homo sapiens", category = "H") %>% 
    dplyr::select(gs_name, entrez_gene) #两列：gs_name,entrez_gene

### extract Top200 gene
deg <- read.delim(genelist,sep="\t",header = T)
deg <- deg %>% dplyr::group_by(cluster) %>% 
    dplyr::top_n(200,wt = avg_log2FC) %>% 
    dplyr::select(cluster,gene) %>% 
    as.data.frame()
# colnames(deg) <- c("cluster","gene")
rownames(deg) <- c()

### id transform
entrez <- bitr(as.character(deg$gene), fromType="SYMBOL", toType="ENTREZID", OrgDb="org.Hs.eg.db")
deg <- merge(deg, entrez, by.x = "gene", by.y = "SYMBOL")

### enrich 
# KEGG
EKEGG <- compareCluster(ENTREZID~cluster, data=deg, fun="enrichKEGG", organism="hsa", pvalueCutoff=0.05, qvalueCutoff  = 0.5)
EKEGG=setReadable(EKEGG,'org.Hs.eg.db', 'ENTREZID')
# GO
EGO_BP <- compareCluster(ENTREZID~cluster, data=deg, fun="enrichGO", OrgDb = org.Hs.eg.db, ont = "BP", pAdjustMethod = "BH", pvalueCutoff=0.05, qvalueCutoff  = 0.5, readable = TRUE)
# Reactome
EReact <- compareCluster(ENTREZID~cluster, data=deg, fun="enrichPathway")
# hallmarkers
EHM <- compareCluster(ENTREZID~cluster,data = deg,enricher, TERM2GENE=m_t2g)
EHM = setReadable(EHM,'org.Hs.eg.db', 'ENTREZID')


### plot result
pdf(paste0(indir,"/",pre,"_enrich.",day,".pdf"), 10, 15)
dotplot(EKEGG, showCategory=5, color="qvalue",title="KEGG")+theme(axis.text.x = element_text(vjust=1,hjust=1,size=8,angle = 45))
dotplot(EGO_BP, showCategory=5, color="qvalue",title="GO_BP")+theme(axis.text.x = element_text(vjust=1,hjust=1,size=8,angle = 45))
dotplot(EReact,showCategory=5,color="qvalue",title="Reactome")+theme(axis.text.x = element_text(vjust=1,hjust=1,size=8,angle = 45))
dotplot(EHM,title="Hallmark gene sets")+theme(axis.text.x = element_text(vjust=1,hjust=1,size=8,angle = 45))
dev.off()


### output result
write.xlsx(list(GO = EGO_BP@compareClusterResult, KEGG = EKEGG@compareClusterResult, Reactome=EReact@compareClusterResult,HallMarker=EHM), paste0(pre,"_Enrich.",day,".xlsx"))
```

