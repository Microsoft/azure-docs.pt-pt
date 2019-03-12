---
title: Simulação paralela em R com o Azure Batch
description: Tutorial - instruções passo a passo para executar uma simulação financeira Monte Carlo no Azure Batch com o pacote doAzureParallel de R
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: c682d35872587aa238ca9d76ade3d58654963628
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551743"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Tutorial: Executar uma simulação paralela em R com o Azure Batch 

Execute as suas cargas de trabalho paralelas em R em escala com [doAzureParallel](https://www.github.com/Azure/doAzureParallel), um pacote de R leve que lhe permite utilizar o Azure Batch diretamente na sua sessão de R. O pacote doAzureParallel foi criado com base no popular pacote de R [foreach](https://cran.r-project.org/web/packages/foreach/index.html). doAzureParallel obtém cada iteração do ciclo foreach e submete-a como tarefa do Azure Batch.

Este tutorial mostra-lhe como implementar um conjunto do Batch e executar um trabalho paralelo de R no Azure Batch diretamente dentro do RStudio. Saiba como:
 

> [!div class="checklist"]
> * Instalar doAzureParallel e configurá-lo para aceder às suas contas do Batch e de armazenamento
> * Criar um conjunto do Batch como um back-end paralelo para a sua sessão de R
> * Executar uma simulação paralela de exemplo no conjunto

## <a name="prerequisites"></a>Pré-requisitos

* Uma distribuição de [R](https://www.r-project.org/) instalada, como o [Microsoft R Open](https://mran.microsoft.com/open). Utilize a versão 3.3.1 ou posterior de R.

* [RStudio](https://www.rstudio.com/), quer a edição comercial, quer o [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) de código aberto. 

* Uma conta do Azure Batch e uma conta de Armazenamento do Azure. Para criar estas contas, veja os inícios rápidos do Batch no [portal do Azure](quick-create-portal.md) ou na [CLI do Azure](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>Instalar doAzureParallel

Na consola do RStudio, instale o [pacote doAzureParallel do GitHub](https://www.github.com/Azure/doAzureParallel). Os comandos seguintes transferem e instalam o pacote e as respetivas dependências na sua sessão atual de R: 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
A instalação pode demorar vários minutos.

Para configurar doAzureParallel com as credenciais da conta que obteve anteriormente, gere um ficheiro de configuração chamado *credentials.json* no seu diretório de trabalho: 

```R
generateCredentialsConfig("credentials.json") 
``` 

Preencha este ficheiro com os nomes e as chaves da conta do Batch e da conta de armazenamento. Deixe a definição `githubAuthenticationToken` inalterada.

Quando terminar, o ficheiro de credenciais terá um aspeto semelhante ao seguinte: 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Guarde o ficheiro. Em seguida, execute o comando seguinte para definir as credenciais para a sua sessão atual de R: 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Criar um conjunto do Batch 

doAzureParallel inclui uma função para gerar um conjunto do Azure Batch (cluster) para executar trabalhos paralelos de R. Os nós executam uma [Máquina Virtual de Ciência de Dados do Azure](../machine-learning/data-science-virtual-machine/overview.md) baseada no Ubuntu. O Microsoft R Open e os populares pacotes de R vêm pré-instalados nesta imagem. Pode ver ou personalizar determinadas definições dos clusters, como o número e o tamanho dos nós. 

Para gerar um ficheiro JSON de configuração de cluster no seu diretório de trabalho: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
Abra o ficheiro para ver a configuração predefinida, que inclui três nós dedicado e três nós de [prioridade baixa](batch-low-pri-vms.md). Estas definições são apenas exemplos que pode experimentar ou modificar. Os nós dedicados estão reservados para o conjunto. Os nós de prioridade baixa são disponibilizados a um preço reduzido a partir da capacidade excedente da VM no Azure. Os nós de prioridade baixa ficam indisponíveis se o Azure não tiver capacidade suficiente. 

Para este tutorial, altere a configuração da seguinte forma:

* Aumente `maxTasksPerNode` para *2*, de modo a tirar partido de ambos os núcleos em cada nó
* Defina `dedicatedNodes` como *0*, para poder experimentar as VMs de prioridade baixa disponíveis para o Batch. Defina `min` de `lowPriorityNodes` como *5* e `max` como *10* ou escolha números mais pequenos, se necessário. 

Deixe as predefinições nas restantes definições e guarde o ficheiro. Deve ter um aspeto semelhante ao seguinte:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Agora, crie o cluster. O Batch cria o conjunto de imediato, mas demora alguns minutos a alocar e a iniciar os nós de computação. Quando o cluster estiver disponível, registe-o como o back-end paralelo da sua sessão de R. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

O resultado indica o número de "funções de trabalho da execução" para doAzureParallel. Este número é o número de nós multiplicado pelo valor de `maxTasksPerNode`. Se tiver modificado a configuração do cluster tal como descrito anteriormente, o número é *10*. 
 
## <a name="run-a-parallel-simulation"></a>Executar uma simulação paralela

Agora que o cluster foi criado, está pronto para executar o ciclo foreach com o seu back-end paralelo registado (o conjunto do Azure Batch). Como exemplo, execute uma simulação financeira Monte Carlo, primeiro localmente, mediante a utilização de um ciclo foreach padrão, e depois ao executar o foreach com o Batch. Este exemplo é uma versão simplificada da previsão do preço de títulos mediante a simulação de um grande número de resultados diferentes ao fim de cinco anos.

Suponha que os títulos da Contoso Corporation ganham, em média, mais 1,001 sobre o preço de abertura todos os dias, mas têm uma volatilidade (desvio-padrão) de 0,01. Tendo por base um preço inicial de 100 $, utilize uma simulação de preços Monte Carlo para descobrir o preço dos títulos da Contoso ao fim de cinco anos.

Parâmetros para a simulação Monte Carlo:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

Para simular os preços de fecho, defina a seguinte função:

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

Primeiro, execute dez mil simulações localmente com um ciclo foreach padrão com a palavra-chave `%do%`:

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


Desenhe os preços de fecho num histograma para ver a distribuição dos resultados:

```R
hist(closingPrices_s)
``` 

O resultado é semelhante ao seguinte:

![Distribuição dos preços de fecho](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
As simulações locais são concluídas em poucos segundos ou menos:

```R
difftime(end_s, start_s) 
```

O tempo de execução previsto para dez milhões de resultados localmente, mediante uma aproximação linear, é de cerca de 30 minutos:

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


Agora, execute o código através da utilização de `foreach` com a palavra-chave `%dopar%` para comparar quanto tempo demora a executar dez milhões de simulações no Azure. Para paralelizar a simulação com o Batch, execute cem iterações de cem mil simulações:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

A simulação distribui tarefas para os nós no conjunto do Batch. Pode ver a atividade no mapa térmico relativa ao conjunto no portal do Azure]. Aceda a **contas do Batch** > *myBatchAccount*. Clique em **Conjuntos** > *myPoolName*. 

![Mapa térmico do conjunto que executa tarefas paralelas de R](media/tutorial-r-doazureparallel/pool.png)

Ao fim de alguns minutos, a simulação é concluída. O pacote intercala automaticamente os resultados e extrai-os a partir dos nós. Depois, estará pronto para utilizar os resultados da sua sessão de R. 

```R
hist(closingPrices_p) 
```

O resultado é semelhante ao seguinte:

![Distribuição dos preços de fecho](media/tutorial-r-doazureparallel/closing-prices.png)

Quanto tempo demorou a simulação paralela? 

```R
difftime(end_p, start_p, unit = "min")  
```

Deve ter em conta que a execução da simulação no conjunto do Batch lhe proporciona um aumento significativo no desempenho em relação ao tempo previsto para executar a simulação localmente. 

## <a name="clean-up-resources"></a>Limpar recursos

O trabalho é eliminado automaticamente depois de terminar. Quando o cluster deixar de ser necessário, chame a função `stopCluster` no pacote doAzureParallel para eliminá-lo:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
Instalar doAzureParallel e configurá-lo para aceder às suas contas do Batch e de armazenamento
> * Criar um conjunto do Batch como um back-end paralelo para a sua sessão de R
> * Executar uma simulação paralela de exemplo no conjunto


Para obter mais informações sobre doAzureParallel, veja a documentação e os exemplos no GitHub.

> [!div class="nextstepaction"]
> [doAzureParallel package](https://github.com/Azure/doAzureParallel/) (Pacote doAzureParallel)




