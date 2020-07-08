---
title: Implementar modelos na produção - Processo de Ciência de Dados de Equipa
description: Como implementar modelos na produção que lhes permitam desempenhar um papel ativo na tomada de decisões empresariais.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76722242"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implementar modelos na produção para desempenhar um papel ativo na tomada de decisões empresariais

A implantação da produção permite que um modelo desem este um papel ativo num negócio. As previsões de um modelo implantado podem ser usadas para decisões empresariais.

## <a name="production-platforms"></a>Plataformas de produção

Existem várias abordagens e plataformas para colocar modelos em produção. Aqui estão algumas opções:

- [Onde implementar modelos com Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implantação de um modelo no sql-servidor](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Servidor do Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Antes da implantação, é preciso garantir que a latência da pontuação do modelo é baixa o suficiente para ser usada na produção.
>

>[!NOTE]
>Para implementar usando o Azure Machine Learning Studio, consulte [implementar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Testes A/B

Quando vários modelos estão em produção, [os testes A/B](https://en.wikipedia.org/wiki/A/B_testing) podem ser usados para comparar o desempenho do modelo. 
 
## <a name="next-steps"></a>Próximos passos

São também fornecidas etapas que demonstram todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no artigo [exemplo.](walkthroughs.md) Ilustram como combinar ferramentas e serviços de nuvem, no local, num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 
