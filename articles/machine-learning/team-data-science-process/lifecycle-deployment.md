---
title: Fase de implantação do ciclo de vida do Processo de Ciência de Dados da Equipa
description: Os objetivos, tarefas e entregas para a fase de implantação dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720491"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fase de implantação do ciclo de vida do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e entregas associados à implementação do Processo de Ciência de Dados da Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais etapas que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implantação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida da TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
Implementar modelos com um pipeline de dados para um ambiente de produção ou produção para aceitação final do utilizador. 

## <a name="how-to-do-it"></a>Como fazê-lo
A principal tarefa abordada nesta fase:

**Operacionalizar o modelo**: Implantar o modelo e o oleoduto num ambiente de produção ou produção para consumo de aplicação.

### <a name="operationalize-a-model"></a>Operacionalizar um modelo
Depois de ter um conjunto de modelos que funcionam bem, pode operacionalizá-los para outras aplicações consumirem. Dependendo dos requisitos do negócio, as previsões são feitas em tempo real ou numa base de lote. Para implementar modelos, expõe-nos com uma interface API aberta. A interface permite que o modelo seja facilmente consumido a partir de várias aplicações, tais como:

   * Sites online
   * Folhas de cálculo 
   * Dashboards
   * Aplicações de linha de negócio 
   * Aplicações de back-end 

Por exemplo, de operacionalização de modelos com um serviço web Azure Machine Learning, consulte [Implementar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). É uma boa prática construir telemetria e monitorização no modelo de produção e no pipeline de dados que implementa. Esta prática ajuda com relatórios de estado do sistema subsequentes e resolução de problemas.  

## <a name="artifacts"></a>Artefactos

* Um painel de instrumentos de estado que exibe a saúde do sistema e métricas-chave
* Um relatório final de modelação com detalhes de implementação
* Um documento final de arquitetura de solução


## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implantação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. Os passadiços ilustram como combinar cloud, ferramentas no local e serviços em um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
