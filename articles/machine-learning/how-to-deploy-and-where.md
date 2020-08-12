---
title: Como e onde implementar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implementar os seus modelos de Machine Learning Azure, incluindo instâncias de contentores Azure, Serviço Azure Kubernetes, Azure IoT Edge e conjuntos de portas programáveis em campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 439bf51aac4d04ee12968588a4e38c4643b4808a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120355"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implementar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar o seu modelo de machine learning como um serviço web na nuvem Azure ou para dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante, independentemente de onde implementa o modelo:

1. Registar o modelo.
1. Preparar uma configuração de inferência
1. Preparar um script de entrada (a menos que utilize [a implementação sem código)](how-to-deploy-no-code-deployment.md)
1. Implementar o modelo para o destino de computação.
1. Teste o modelo implantado, também chamado de serviço web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [Gerir, implementar e monitorizar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).


::: zone pivot="py-sdk"

[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]

::: zone-end

::: zone pivot="cli"

[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]

::: zone-end

### <a name="understanding-service-state"></a>Estado de serviço de compreensão

Durante a implementação do modelo, poderá ver a alteração do estado de serviço enquanto este se desdobra completamente.

A tabela a seguir descreve os diferentes estados de serviço:

| Estado do serviço web | Descrição | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | No |
| Mau estado de funcionamento | O serviço foi implantado, mas está atualmente inacessível.  | No |
| Insodulável | O serviço não pode ser implantado neste momento devido à falta de recursos. | No |
| Com falhas | O serviço falhou em ser acionado devido a um erro ou acidente. | Yes |
| Bom estado de funcionamento | O serviço é saudável e o ponto final está disponível. | Yes |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferência do lote
Os alvos Azure Machine Learning Compute são criados e geridos pela Azure Machine Learning. Podem ser utilizados para a previsão de lote a partir de oleodutos Azure Machine Learning.

Para uma passagem de inferência de lote com Azure Machine Learning Compute, consulte [Como executar previsões de lotes](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferência IoT Edge
O suporte para implantação na borda está em pré-visualização. Para obter mais informações, consulte [implementar a Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas de uma implantação falhada](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)

