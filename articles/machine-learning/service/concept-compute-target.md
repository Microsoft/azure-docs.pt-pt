---
title: 'Destinos de computação: onde preparar e implementar modelos'
titleSuffix: Azure Machine Learning service
description: Defina o local de preparar ou implementar o modelo com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806053"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Quais são os destinos de computação no serviço Azure Machine Learning? 

R **destino de computação** é um recurso de computação designado/ambiente em que executou o script de treinamento ou anfitrião a implementação do serviço. Esta localização pode ser seu computador local ou um recurso de computação com base na cloud. Com destinos tornam mais fácil para alterar posteriormente o seu ambiente de computação sem ter de alterar o seu código de computação.  

Num ciclo de vida do desenvolvimento de modelo típico, poderá:
1. Comece a desenvolver e fazer experiências numa pequena quantidade de dados. Nesta fase, recomendamos que o seu local ambiente (computador local ou VM com base na cloud), como o destino de computação. 
2. Aumentar verticalmente para volumes de dados maiores ou distribuído treinamento usando um destes [destinos de computação de treinamento](#train).  
3. Assim que o modelo estiver pronto, implemente-a uma dispositivo de IoT com um dos seguintes ou de ambiente de alojamento na web [destinos de computação de implementação](#deploy).

Os recursos de computação que utiliza para seus destinos de computação estão anexados a um [área de trabalho](concept-workspace.md). Recursos que não seja o computador local são partilhados por utilizadores da área de trabalho de computação.

## <a name="train"></a> Destinos de computação de treinamento

O serviço do Azure Machine Learning tem suporte variado nos recursos de computação diferentes.  Também pode anexar seus próprios recursos de computação, embora o suporte para vários cenários podem variar.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Saiba mais sobre [configurar e utilizar um destino de computação para a preparação de modelos](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Destinos de implementação

Os seguintes recursos de computação podem ser utilizados para alojar a sua implementação do modelo.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Saiba mais [onde e como implementar o seu modelo para um destino de computação](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Computação de Machine Learning do Azure (gerenciada)

Um recurso de computação gerida é criado e gerenciado pelo serviço Azure Machine Learning. Este computação está otimizada para cargas de trabalho do machine learning. Computação do Azure Machine Learning é a única computação gerida a partir de 30 de Maio de 2019. Recursos de computação geridos adicionais podem ser adicionados no futuro.

Pode usar a computação do Azure Machine Learning para formação e inferência de batch (pré-visualização).  Este recurso de computação, tem:

* Cluster único ou vários node
* É dimensionado automaticamente sempre que submete uma execução 
* Gerenciamento de cluster automática e o agendamento de tarefas 
* Suporte para recursos de CPU e GPU

Pode criar instâncias de computação do Azure Machine Learning no portal do Azure, com o SDK, ou com a CLI. Quando criou automaticamente faz parte da sua área de trabalho, ao contrário de outros tipos de destinos de computação.

## <a name="unmanaged-compute"></a>Computação não gerida

É um destino de computação não gerido *não* gerido pelo serviço Azure Machine Learning. Criar este tipo de destino de computação fora do Azure Machine Learning, em seguida, anexá-lo à sua área de trabalho. Recursos de computação não gerenciado podem exigem passos adicionais para si para manter ou melhorar o desempenho para cargas de trabalho do machine learning.

## <a name="next-steps"></a>Passos Seguintes

Aprenda a:
* [Configurar um destino de computação para preparar o seu modelo](how-to-set-up-training-targets.md)
* [Implementar o seu modelo para um destino de computação](how-to-deploy-and-where.md)