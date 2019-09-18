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
ms.date: 07/10/2019
ms.openlocfilehash: fe60b740312ee49510ea931bba1346ceaef9f31a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035525"
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

Um recurso de computação gerenciado é criado e gerenciado pelo Azure Machine Learning. Essa computação é otimizada para cargas de trabalho de Machine Learning. Azure Machine Learning computação é a única computação gerenciada a partir de 30 de maio de 2019. Recursos de computação gerenciados adicionais podem ser adicionados no futuro.

Você pode usar Azure Machine Learning computação para treinamento e inferência do lote (versão prévia).  Com esse recurso de computação, você tem:

* Cluster único ou com vários nós
* Dimensionamento a cada vez que você envia uma execução 
* Gerenciamento automático de clusters e agendamento de trabalhos 
* Suporte para recursos de CPU e GPU

Você pode criar Azure Machine Learning instâncias de computação no portal do Azure ou na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com), com o SDK ou com a CLI. Quando criado, ele faz parte automaticamente de seu espaço de trabalho, ao contrário de outros tipos de destinos de computação.

## <a name="unmanaged-compute"></a>Computação não gerenciada

Um destino de computação *não gerenciado não* é gerenciado pelo Azure Machine Learning. Você cria esse tipo de destino de computação fora do Azure Machine Learning e, em seguida, anexa-o ao seu espaço de trabalho. Recursos de computação não gerenciados podem exigir etapas adicionais para você manter ou para melhorar o desempenho de cargas de trabalho de aprendizado de máquina.

## <a name="next-steps"></a>Passos Seguintes

Aprenda a:
* [Configurar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md)
* [Implantar seu modelo em um destino de computação](how-to-deploy-and-where.md)