---
title: O ciclo de vida do processo de ciência de dados de equipe
description: O TDSP (processo de ciência de dados de equipe) fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados.
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
ms.openlocfilehash: 00efe89314d4a1a5c3302e820b8609adf194aa59
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053247"
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do processo de ciência de dados de equipe

O TDSP (processo de ciência de dados de equipe) fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve as etapas, do início ao fim, que os projetos geralmente seguem quando são executados. Se você usar outro ciclo de vida de ciência de dados, como o processo padrão entre o setor para mineração de dados [(Crisp-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), a descoberta de conhecimento em bancos [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)ou o próprio processo personalizado da sua organização, você ainda poderá usar o TDSP baseado em tarefas. 

Esse ciclo de vida foi projetado para projetos de ciência de dados que devem ser fornecidos como parte de aplicativos inteligentes. Esses aplicativos implantam modelos de aprendizado de máquina ou de inteligência artificial para análise preditiva. Projetos de ciência de dados exploratórios e projetos de análise ad hoc também podem se beneficiar do uso desse processo. Mas, para esses projetos, algumas das etapas descritas aqui podem não ser necessárias. 

## <a name="five-lifecycle-stages"></a>Cinco estágios do ciclo de vida

O ciclo de vida do TDSP é composto de cinco estágios principais que são executados iterativamente. Esses estágios incluem:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Mode](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Aqui está uma representação visual do ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida do TDSP é modelado como uma sequência de etapas iteradas que fornecem orientação sobre as tarefas necessárias para usar modelos de previsão. Você implanta os modelos de previsão no ambiente de produção que planeja usar para criar os aplicativos inteligentes. O objetivo desse ciclo de vida do processo é continuar a mover um projeto de ciência de dados em direção a um ponto de extremidade claro do Engagement. A ciência de dados é um exercício em pesquisa e descoberta. A capacidade de comunicar tarefas com sua equipe e seus clientes usando um conjunto bem definido de artefatos que empregam modelos padronizados ajuda a evitar mal-entendidos. O uso desses modelos também aumenta a chance da conclusão bem-sucedida de um projeto de ciência de dados complexo.

Para cada estágio, fornecemos as seguintes informações:

   * **Metas**: os objetivos específicos.
   * **Como fazer isso**: uma descrição das tarefas específicas e diretrizes sobre como concluí-las.
   * **Artefatos**: os resultados finais e o suporte para produzi-los.

## <a name="next-steps"></a>Passos seguintes

Fornecemos orientações completas de ponta a ponta que demonstram todas as etapas no processo para cenários específicos. O artigo [passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de miniaturas e links. As orientações ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas em TDSPs que usam Azure Machine Learning Studio, consulte [usar o TDSP com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
