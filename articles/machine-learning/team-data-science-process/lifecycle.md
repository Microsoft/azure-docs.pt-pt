---
title: O ciclo de vida do processo de ciência de dados de equipa
description: O Team Data Science Process (TDSP) fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720457"
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do processo de ciência de dados de equipa

O Team Data Science Process (TDSP) fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve os passos completos que os projetos de sucesso seguem. Se utilizar outro ciclo de vida de ciência de dados, como o Processo Padrão da Indústria Cruzada para a Mineração de Dados [(CRISP-DM),](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)A Descoberta do Conhecimento em Bases de Dados [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)ou o próprio processo personalizado da sua organização, ainda pode utilizar o TDSP baseado em tarefas. 

Este ciclo de vida foi concebido para os projetos de ciência de dados que se destinam a são fornecidos como parte de aplicações inteligentes. Esses aplicativos implementarem modelos de aprendizagem ou inteligência artificial de automática para Análise Preditiva. Projetos exploratórios de ciência de dados e projetos de análise improvisados também podem beneficiar da utilização deste processo. Mas, relativamente aos projetos, algumas das etapas descritas aqui podem não ser necessários. 

## <a name="five-lifecycle-stages"></a>Cinco fases do ciclo de vida

O ciclo de vida do TDSP é composto por cinco fases principais que é executado de maneira iterativa. Estas fases incluem:

   1. [Compreensão de negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida do TDSP é modelado como uma seqüência de etapas repetitivo que fornecer orientações sobre as tarefas necessárias para utilizar modelos de previsão. Implementar modelos de previsão no ambiente de produção que planeia utilizar para criar as aplicações inteligentes. O objetivo deste ciclo de vida do processo é a mudar de um projeto de ciência de dados para um ponto de final de envolvimento clara. Ciência de dados é um exercício em pesquisa e deteção. A capacidade de comunicar tarefas para a sua equipa e os seus clientes através de um conjunto bem definido de artefactos que empregam modelos padronizados ajuda a evitar confusões. Também usá-los aumenta a probabilidade da conclusão com êxito de um projeto de ciência de dados complexos.

Para cada estágio, fornecemos as seguintes informações:

   * **Objetivos**: Os objetivos específicos.
   * **Como fazê-lo**: Um esboço das tarefas específicas e orientação sobre como completá-las.
   * **Artefactos**: Os entregas e o suporte para produzi-los.

## <a name="next-steps"></a>Passos Seguintes

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
