---
title: O ciclo de vida do processo de ciência de dados de equipa - Azure | Documentos da Microsoft
description: Os passos necessários para executar seus projetos de ciência de dados
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 611793297559c99ffa46f7c0266ed6865eb650fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234277"
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do processo de ciência de dados de equipa

O Team Data Science Process (TDSP) fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve os passos, do início ao fim, que projetos seguem, normalmente, quando forem executados. Se utilizar outro ciência de dados ciclo de vida, como o Cross setor processo padrão para Data Mining [(CRISP-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), deteção de dados de conhecimento em bases de dados [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), ou seu processo de personalizados da organização , pode continuar a utilizar o TDSP baseado em tarefas. 

Este ciclo de vida foi concebido para os projetos de ciência de dados que se destinam a são fornecidos como parte de aplicações inteligentes. Esses aplicativos implementarem modelos de aprendizagem ou inteligência artificial de automática para Análise Preditiva. Projetos de ciência de dados exploratório e projetos de análise ad hoc também podem se beneficiar da utilização deste processo. Mas, relativamente aos projetos, algumas das etapas descritas aqui podem não ser necessários. 

O ciclo de vida do TDSP é composto por cinco fases principais que é executado de maneira iterativa. Estas fases incluem:

   1. [Compreensão empresarial](lifecycle-business-understanding.md)
   2. [Aquisição de dados e compreensão](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida do TDSP é modelado como uma seqüência de etapas repetitivo que fornecer orientações sobre as tarefas necessárias para utilizar modelos de previsão. Implementar modelos de previsão no ambiente de produção que planeia utilizar para criar as aplicações inteligentes. O objetivo deste ciclo de vida do processo é a mudar de um projeto de ciência de dados para um ponto de final de envolvimento clara. Ciência de dados é um exercício em pesquisa e deteção. A capacidade de comunicar tarefas para a sua equipa e os seus clientes através de um conjunto bem definido de artefactos que empregam modelos padronizados ajuda a evitar confusões. Também usá-los aumenta a probabilidade da conclusão com êxito de um projeto de ciência de dados complexos.

Para cada estágio, fornecemos as seguintes informações:

   * **Objetivos**: os objetivos específicos.
   * **Como fazê-lo**: uma descrição das tarefas específicas e obter orientações sobre como concluí-las.
   * **Artefactos**: os resultados finais e o suporte para produzi-los.

## <a name="next-steps"></a>Passos Seguintes

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O [instruções passo a passo do exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com links e descrições em miniatura. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](https://aka.ms/datascienceprocess).
