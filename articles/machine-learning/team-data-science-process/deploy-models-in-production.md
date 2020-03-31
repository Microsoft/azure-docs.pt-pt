---
title: Implementar modelos em produção - Team Data Science Process
description: Como implementar modelos para a produção permitindo-lhes desempenhar um papel ativo na tomada de decisões empresariais.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722242"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implementar modelos para a produção para desempenhar um papel ativo na tomada de decisões empresariais

A implantação da produção permite que um modelo desempem um papel ativo num negócio. As previsões de um modelo implantado podem ser usadas para decisões empresariais.

## <a name="production-platforms"></a>Plataformas de produção

Existem várias abordagens e plataformas para colocar modelos em produção. Aqui estão algumas opções:

- [Onde implementar modelos com Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementação de um modelo no servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Antes da implantação, é preciso garantir que a latência da pontuação do modelo é baixa o suficiente para ser usada na produção.
>

>[!NOTE]
>Para ser implementado utilizando o Azure Machine Learning Studio, consulte [A implantação de um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Ensaio a/B

Quando vários modelos estão em produção, os [testes A/B](https://en.wikipedia.org/wiki/A/B_testing) podem ser usados para comparar o desempenho do modelo. 
 
## <a name="next-steps"></a>Passos seguintes

São também fornecidos walkthroughs que demonstrem todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no artigo [de walkthroughs exemplo.](walkthroughs.md) Ilustram como combinar cloud, ferramentas no local e serviços num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 
