---
title: 'Estúdio ML (clássico): Migrar para Azure Machine Learning - Executar R Script'
description: Reconstruir Estúdio (clássico) Executar módulos de script R para executar em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565290"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrar Executar módulos de script R em Estúdio (clássico)

Neste artigo, aprende-se a reconstruir um módulo de Script **executo** (clássico) em Azure Machine Learning.

Para obter mais informações sobre a migração do Studio (clássico), consulte o [artigo de visão geral da migração.](migrate-overview.md)

## <a name="execute-r-script"></a>Executar Script R

A designer de Aprendizagem automática Azure agora corre em Linux. Estúdio (clássico) funciona no Windows. Devido à mudança de plataforma, tem de ajustar o seu **Script Executr R** durante a migração, caso contrário o pipeline falhará.

Para migrar um módulo **executo R script** do Studio (clássico), tem de substituir as `maml.mapInputPort` e `maml.mapOutputPort` interfaces por funções padrão.

A tabela seguinte resume as alterações ao módulo de Script R:

|Funcionalidade|Studio (clássico)|Estruturador do Azure Machine Learning|
|---|---|---|
|Script Interface|`maml.mapInputPort` e `maml.mapOutputPort`|Interface de função|
|Plataforma|Windows|Linux|
|Acessível à Internet |No|Yes|
|Memória|14 GB|Dependente do Compute SKU|

### <a name="how-to-update-the-r-script-interface"></a>Como atualizar a interface do script R

Aqui estão os conteúdos de uma amostra **execute** módulo de script R em Studio (clássico):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Aqui estão os conteúdos atualizados no designer. Note que o `maml.mapInputPort` e `maml.mapOutputPort` foram substituídos pela interface de função padrão `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Para obter mais informações, consulte a referência do [módulo executo R Script](../algorithm-module-reference/execute-r-script.md)do designer.

### <a name="install-r-packages-from-the-internet"></a>Instalar pacotes R a partir da internet

O designer de Machine Learning Azure permite instalar pacotes diretamente da CRAN.

Esta é uma melhoria sobre Studio (clássico). Uma vez que o Studio (clássico) funciona num ambiente de caixa de areia sem acesso à Internet, teve de carregar scripts num pacote zip para instalar mais pacotes. 

Utilize o seguinte código para instalar pacotes CRAN no módulo **executo R script** do designer:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a migrar módulos executou scripts para Azure Machine Learning.

Veja os outros artigos na série de migração Studio (clássica):

1. [Visão geral da migração](migrate-overview.md).
1. [Conjunto de dados migrar](migrate-register-dataset.md).
1. [Reconstruir um pipeline de treino studio (clássico).](migrate-rebuild-experiment.md)
1. [Reconstruir um serviço web Studio (clássico).](migrate-rebuild-web-service.md)
1. [Integre um serviço web Azure Machine Learning com aplicações para clientes.](migrate-rebuild-integrate-with-client-app.md)
1. **Migrar Executar módulos de script R**.