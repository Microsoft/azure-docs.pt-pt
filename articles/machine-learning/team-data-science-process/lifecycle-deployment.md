---
title: Estágio de implantação do ciclo de vida do processo de ciência de dados de equipe
description: As metas, as tarefas e os resultados finais para o estágio de implantação de seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 156f2e95913dffd88de0b4669b13a5af5fc9605c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684696"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Estágio de implantação do ciclo de vida do processo de ciência de dados de equipe

Este artigo descreve as metas, as tarefas e os resultados associados à implantação do TDSP (processo de ciência de dados de equipe). Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os principais estágios que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções básicas sobre negócios**
   2. **Aquisição de dados e compreensão**
   3. **Mode**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
Implante modelos com um pipeline de dados em um ambiente de produção ou semelhante à produção para a aceitação final do usuário. 

## <a name="how-to-do-it"></a>Como fazer isso
A principal tarefa abordada neste estágio:

Colocar **o modelo em operação**: implante o modelo e o pipeline em um ambiente de produção ou semelhante à produção para o consumo de aplicativos.

### <a name="operationalize-a-model"></a>Operacionalizar um modelo
Depois de ter um conjunto de modelos que têm um bom desempenho, você pode operacionalá-los para que outros aplicativos consumam. Dependendo dos requisitos de negócios, as previsões são feitas em tempo real ou em uma base de lote. Para implantar modelos, você os expõe com uma interface de API aberta. A interface permite que o modelo seja facilmente consumido de vários aplicativos, como:

   * Sites online
   * Planilhas 
   * Dashboards
   * Aplicativos de linha de negócios 
   * Aplicativos de back-end 

Para obter exemplos de operacionalização de modelo com um serviço Web Azure Machine Learning, consulte [implantar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). É uma prática recomendada criar telemetria e monitoramento no modelo de produção e no pipeline de dados que você implanta. Essa prática ajuda com o relatório de status do sistema e a solução de problemas subsequentes.  

## <a name="artifacts"></a>Artefactos

* Um painel de status que exibe a integridade do sistema e as métricas-chave
* Um relatório final de modelagem com detalhes de implantação
* Um documento de arquitetura de solução final


## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada etapa no ciclo de vida do TDSP:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Mode](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações completas de ponta a ponta que demonstram todas as etapas no processo para cenários específicos. O artigo [passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de miniaturas e links. As orientações ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas em TDSPs que usam Azure Machine Learning Studio, consulte [usar o TDSP com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
