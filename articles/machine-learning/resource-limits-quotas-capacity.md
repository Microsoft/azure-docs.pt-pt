---
title: Limites do serviço
titleSuffix: Azure Machine Learning
description: Limites de serviço utilizados para o planeamento da capacidade e limites máximos em pedidos e respostas para a Aprendizagem Automática Azure.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: 132c4b223b99f7110cd4553bb0dd93f1e58f4cdd
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733388"
---
# <a name="service-limits-in-azure-machine-learning"></a>Limites de serviço na Aprendizagem automática Azure

Esta secção enumera quotas básicas e limiares de estrangulamento no Azure Machine Learning.

## <a name="workspaces"></a>Áreas de trabalho
| Limite | Valor |
| --- | --- |
| Nome da área de trabalho | 2-32 caracteres |

## <a name="runs"></a>Execuções
| Limite | Valor |
| --- | --- |
| Corre por espaço de trabalho | 10 milhões |
| RunId/ParentRunId | 256 caracteres |
| DataContainerId | 261 caracteres |
| DisplayName |256 caracteres|
| Description |5.000 caracteres|
| Número de imóveis |50 |
| Duração da chave de propriedade |100 caracteres |
| Duração do valor da propriedade |1.000 caracteres |
| Número de tags |50 |
| Comprimento da chave de etiqueta |100 |
| Comprimento do valor da etiqueta |1.000 caracteres |
| CancelUri / CompleteUri / DiagnosticsUri |1.000 caracteres |
| Comprimento da mensagem de erro |3.000 caracteres |
| Comprimento da mensagem de aviso |300 caracteres |
| Número de conjuntos de dados de entrada |200 |
| Número de conjuntos de dados de saída |20 |


## <a name="metrics"></a>Métricas
| Limite | Valor |
| --- | --- |
| Nomes métricos por corrida |50|
| Linhas métricas por nome métrico |10 milhões|
| Colunas por linha métrica |15|
| Comprimento do nome da coluna métrica |255 caracteres |
| Comprimento do valor da coluna métrica |255 caracteres |
| Linhas métricas por lote carregado | 250 |

> [!NOTE]
> Se estiver a atingir o limite de nomes métricos por corrida porque está a formalizar variáveis no nome métrico, considere em vez disso usar uma métrica de linha onde uma coluna é o valor variável e a segunda coluna é o valor métrico.

## <a name="artifacts"></a>Artefactos

| Limite | Valor |
| --- | --- |
| Número de artefactos por corrida |10 milhões|
| Comprimento máximo do caminho do artefacto |5.000 caracteres |

## <a name="limit-increases"></a>Aumentos de limites
Alguns limites podem ser aumentados para espaços de trabalho individuais [contactando o suporte.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) 

## <a name="next-steps"></a>Passos seguintes

- [Configure o seu ambiente de aprendizagem de máquinas Azure](how-to-configure-environment.md)
