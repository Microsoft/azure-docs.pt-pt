---
title: Fase da implementação de ciclo de vida do processo de ciência de dados de equipa
description: As metas, tarefas e resultados finais para a fase de implantação dos seus projetos de ciência de dados
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720491"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fase da implementação de ciclo de vida do processo de ciência de dados de equipa

Este artigo descreve os objetivos, tarefas e resultados finais associados à implantação do Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Compreensão de negócios**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
Implemente modelos com um pipeline de dados para um ambiente de produção simulado para aceitação do usuário final ou de produção. 

## <a name="how-to-do-it"></a>Como fazê-lo
A tarefa principal abordada nessa etapa:

**Operacionalizar o modelo**: Implantar o modelo e o oleoduto num ambiente de produção ou produção para consumo de aplicação.

### <a name="operationalize-a-model"></a>Operacionalizar um modelo
Depois de ter um conjunto de modelos com bom desempenho, pode operacionalizá-las para outras aplicações para consumir. Consoante os requisitos de negócios, são feitas previsões em tempo real ou numa base de batch. Para implementar os modelos, expô-los com uma interface de API aberta. A interface permite que o modelo possa ser facilmente consumidos a partir de vários aplicativos, tais como:

   * Web sites online
   * Folhas de cálculo 
   * Dashboards
   * Aplicações de linha de negócio 
   * Aplicações de back-end 

Por exemplo, de operacionalização de modelos com um serviço web Azure Machine Learning, consulte [Implementar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). É melhor prática para criar a telemetria e monitorização para o modelo de produção e o pipeline de dados que implementar. Essa prática ajuda com o estado do sistema subsequentes reporting e resolução de problemas.  

## <a name="artifacts"></a>Artefactos

* Um dashboard de estado que mostra as métricas de estado de funcionamento e a chave do sistema
* Um relatório de modelagem final com detalhes de implementação
* Um documento de arquitetura da solução final


## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão de negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
