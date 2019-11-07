---
title: Implantar modelos em produção – processo de ciência de dados da equipe
description: Como implantar modelos para produção, permitindo que eles joguem um papel ativo para tomar decisões de negócios.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6f371d07fdf09aae6ce4396f0916758f8588506b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670312"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implante modelos na produção para desempenhar uma função ativa ao tomar decisões de negócios

A implantação de produção permite que um modelo reproduza uma função ativa em uma empresa. As previsões de um modelo implantado podem ser usadas para decisões de negócios.

## <a name="production-platforms"></a>Plataformas de produção

Há várias abordagens e plataformas para colocar os modelos em produção. Aqui estão algumas opções:

- [Onde implantar modelos com Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Implantação de um modelo no SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Antes da implantação, é necessário garantir que a latência da Pontuação do modelo seja baixa o suficiente para uso na produção.
>

>[!NOTE]
>Para a implantação usando o Azure Machine Learning Studio, consulte [implantar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Teste A/B

Quando vários modelos estão em produção, pode ser útil executar testes a [/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 
 
## <a name="next-steps"></a>Passos seguintes

Os passo a passos que demonstram todas as etapas do processo para **cenários específicos** também são fornecidos. Eles são listados e vinculados a descrições em miniatura no artigo [passo a passos de exemplo](walkthroughs.md) . Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 
