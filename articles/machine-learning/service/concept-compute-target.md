---
title: 'Destinos de computação: onde treinar e implantar modelos'
titleSuffix: Azure Machine Learning
description: Defina onde você deseja treinar ou implantar seu modelo com Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 75e789ac1f9c161bc67ba90062be39f986f5b140
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580586"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>O que são destinos de computação no Azure Machine Learning? 

Um **destino de computação** é um ambiente/recurso de computação designado em que você executa o script de treinamento ou hospeda sua implantação de serviço. Esse local pode ser seu computador local ou um recurso de computação baseado em nuvem. O uso de destinos de computação facilita a alteração do ambiente de computação, sem a necessidade de alterar o código.  

Em um ciclo de vida típico de desenvolvimento de modelos, você pode:
1. Comece desenvolvendo e experimentando uma pequena quantidade de dados. Neste estágio, recomendamos seu ambiente local (computador local ou VM baseada em nuvem) como seu destino de computação. 
2. Escale verticalmente para dados maiores ou faça treinamentos distribuídos usando um desses [destinos de computação de treinamento](#train).  
3. Quando o modelo estiver pronto, implante-o em um ambiente de hospedagem da Web ou dispositivo IoT com um desses [destinos de computação de implantação](#deploy).

Os recursos de computação que você usa para seus destinos de computação são anexados a um [espaço de trabalho](concept-workspace.md). Recursos de computação diferentes do computador local são compartilhados por usuários do espaço de trabalho.

## <a name="train"></a>Treinamento de destinos de computação

Azure Machine Learning tem suporte variado em diferentes recursos de computação.  Você também pode anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Saiba mais sobre como [configurar e usar um destino de computação para treinamento de modelo](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Destinos de implantação

Os recursos de computação a seguir podem ser usados para hospedar sua implantação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Saiba [onde e como implantar seu modelo em um destino de computação](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Computação Azure Machine Learning (gerenciada)

Um recurso de computação gerenciado é criado e gerenciado pelo Azure Machine Learning. Essa computação é otimizada para cargas de trabalho de Machine Learning. Azure Machine Learning clusters de computação. Recursos de computação gerenciados adicionais podem ser adicionados no futuro.

Você pode criar Azure Machine Learning clusters de computação no Azure Machine Learning Studio, portal do Azure, o SDK, um modelo do Resource Manager ou a CLI.

Quando criados, esses recursos de computação são automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de destinos de computação.

Você pode usar Azure Machine Learning clusters de computação para treinamento e inferência do lote (versão prévia).  Com esse recurso de computação, você tem:

* Cluster único ou com vários nós
* Dimensionamento a cada vez que você envia uma execução 
* Gerenciamento automático de clusters e agendamento de trabalhos 
* Suporte para recursos de CPU e GPU



## <a name="unmanaged-compute"></a>Computação não gerenciada

Um destino de computação *não gerenciado não* é gerenciado pelo Azure Machine Learning. Você cria esse tipo de destino de computação fora do Azure Machine Learning e, em seguida, anexa-o ao seu espaço de trabalho. Recursos de computação não gerenciados podem exigir etapas adicionais para você manter ou para melhorar o desempenho de cargas de trabalho de aprendizado de máquina.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:
* [Configurar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md)
* [Implantar seu modelo em um destino de computação](how-to-deploy-and-where.md)