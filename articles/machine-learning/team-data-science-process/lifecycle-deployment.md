---
title: Fase de implantação do ciclo de vida do processo de ciência de dados da equipa
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
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324553"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fase de implantação do ciclo de vida do processo de ciência de dados da equipa

Este artigo descreve os objetivos, tarefas e entregas associados à implementação do Processo de Ciência de Dados de Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais fases que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
Implementar modelos com um pipeline de dados para um ambiente de produção ou produção para a aceitação final do utilizador. 

## <a name="how-to-do-it"></a>Como fazê-lo
A principal tarefa abordada nesta fase:

**Operacionalizar o modelo**: Implantar o modelo e o gasoduto num ambiente de produção ou produção para consumo de aplicações.

### <a name="operationalize-a-model"></a>Operacionalizar um modelo
Depois de ter um conjunto de modelos que funcionam bem, pode operacionalizá-los para outras aplicações consumirem. Dependendo dos requisitos do negócio, as previsões são feitas em tempo real ou em lote. Para implementar modelos, expõe-nos com uma interface API aberta. A interface permite que o modelo seja facilmente consumido a partir de várias aplicações, tais como:

   * Sites online
   * Folhas de cálculo 
   * Dashboards
   * Aplicações de linha de negócio 
   * Aplicações de back-end 

Para exemplos de operacionalização de modelos com um serviço web Azure Machine Learning, consulte [implementar um serviço web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md). É uma boa prática construir telemetria e monitorização no modelo de produção e no pipeline de dados que implementa. Esta prática ajuda na subsequente comunicação do estado do sistema e na resolução de problemas.  

## <a name="artifacts"></a>Artefactos

* Um painel de estado que exibe a saúde do sistema e as principais métricas
* Um relatório final de modelação com detalhes de implantação
* Um documento de arquitetura de solução final


## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações a cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de Dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passos completos que demonstram todos os passos no processo para cenários específicos. O artigo [exemplo walkthroughs](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. As caminhadas ilustram como combinar ferramentas de nuvem, ferramentas no local e serviços em um fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com Azure Machine Learning](./index.yml).