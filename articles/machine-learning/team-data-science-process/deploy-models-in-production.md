---
title: Implementar modelos na produção - Team Data Science Process
description: Como implementar modelos para produção, permitindo-lhes desempenhar um papel ativo na tomada de decisões de negócios.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722242"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implementar modelos para produção para desempenhar um papel ativo na tomada de decisões de negócios

Implantação de produção permite que um modelo para desempenhar um papel ativo numa empresa. Predições a partir de um modelo implementado podem ser utilizadas para a tomada de decisões comerciais.

## <a name="production-platforms"></a>Plataformas de produção

Existem várias abordagens e plataformas para colocar os modelos em produção. Aqui estão algumas opções:

- [Onde implementar modelos com Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementação de um modelo no servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Antes da implantação, é necessário assegurar que a latência de modelo de classificação é baixa o suficiente para utilizar na produção.
>

>[!NOTE]
>Para ser implementado utilizando o Azure Machine Learning Studio, consulte [A implantação de um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A testes a / B

Quando vários modelos estão em produção, os [testes A/B](https://en.wikipedia.org/wiki/A/B_testing) podem ser usados para comparar o desempenho do modelo. 
 
## <a name="next-steps"></a>Passos Seguintes

São também fornecidos walkthroughs que demonstrem todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no artigo [de walkthroughs exemplo.](walkthroughs.md) Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 
