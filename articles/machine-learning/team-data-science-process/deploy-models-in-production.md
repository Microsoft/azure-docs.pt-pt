---
title: Implementar modelos na produção - Team Data Science Process
description: Como implementar modelos para produção, permitindo-lhes desempenhar um papel ativo na tomada de decisões de negócios.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1a75c842989cfbaf7bb1880831fda2bc6994622b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238354"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implementar modelos para produção para desempenhar um papel ativo na tomada de decisões de negócios

Implantação de produção permite que um modelo para desempenhar um papel ativo numa empresa. Predições a partir de um modelo implementado podem ser utilizadas para a tomada de decisões comerciais.

## <a name="production-platforms"></a>Plataformas de produção

Existem várias abordagens e plataformas para colocar os modelos em produção. Aqui estão algumas opções:

- [Onde pretende implementar modelos com o serviço Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Implementação de um modelo no SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Antes da implantação, é necessário assegurar que a latência de modelo de classificação é baixa o suficiente para utilizar na produção.
>

>[!NOTE]
>Para ver a implementação com o Azure Machine Learning Studio, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A testes a / B

Quando vários modelos na produção, pode ser útil realizar [A testes a / B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 
 
## <a name="next-steps"></a>Passos Seguintes

Orientações passo a passo que demonstram todas as etapas do processo para **cenários específicos** também são fornecidas. Se estão listadas e estão associados ao descrições em miniatura da [instruções passo a passo do exemplo](walkthroughs.md) artigo. Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 
