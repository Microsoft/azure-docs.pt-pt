---
title: Fase da implementação de ciclo de vida do processo de ciência de dados de equipa - Azure | Documentos da Microsoft
description: As metas, tarefas e resultados finais para a fase de implantação dos seus projetos de ciência de dados
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: af53b876eff401c7ee3bb0fa52e3fc30ff721afc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232824"
---
# <a name="deployment"></a>Implementação

Este artigo descreve os objetivos, tarefas e resultados finais associados à implantação do Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Compreensão empresarial**
   2. **Aquisição de dados e compreensão**
   3. **Modelagem**
   4. **Implementação**
   5. **Aceitação do cliente**

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
Implemente modelos com um pipeline de dados para um ambiente de produção simulado para aceitação do usuário final ou de produção. 

## <a name="how-to-do-it"></a>Como fazê-lo
A tarefa principal abordada nessa etapa:

**Operacionalizar o modelo**: implementar o modelo e um pipeline para um ambiente de produção simulado para consumo de aplicativo ou produção.

### <a name="operationalize-a-model"></a>Operacionalizar um modelo
Depois de ter um conjunto de modelos com bom desempenho, pode operacionalizá-las para outras aplicações para consumir. Consoante os requisitos de negócios, são feitas previsões em tempo real ou numa base de batch. Para implementar os modelos, expô-los com uma interface de API aberta. A interface permite que o modelo possa ser facilmente consumidos a partir de vários aplicativos, tais como:

   * Web sites online
   * Folhas de cálculo 
   * Dashboards
   * Aplicações de linha de negócio 
   * Aplicações de back-end 

Para obter exemplos de operacionalização de modelo com um serviço web Azure Machine Learning, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). É melhor prática para criar a telemetria e monitorização para o modelo de produção e o pipeline de dados que implementar. Essa prática ajuda com o estado do sistema subsequentes reporting e resolução de problemas.  

## <a name="artifacts"></a>Artefactos

* Um dashboard de estado que mostra as métricas de estado de funcionamento e a chave do sistema
* Um relatório de modelagem final com detalhes de implementação
* Um documento de arquitetura da solução final


## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão empresarial](lifecycle-business-understanding.md)
   2. [Aquisição de dados e compreensão](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O [instruções passo a passo do exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com links e descrições em miniatura. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](https://aka.ms/datascienceprocess).
