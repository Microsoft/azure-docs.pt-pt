---
title: Destinos de computação
titleSuffix: Azure Machine Learning service
description: Um destino de computação permite-lhe especificar o recurso de computação em que executou o script de treinamento ou anfitrião a implementação do serviço. Esta localização pode ser seu computador local ou um recurso de computação com base na cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755354"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>O que é um destino de computação no serviço Azure Machine Learning? 

Um destino de computação permite-lhe especificar o recurso de computação em que executou o script de treinamento ou anfitrião a implementação do serviço. Esta localização pode ser seu computador local ou um recurso de computação com base na cloud.

Destinos de computação tornam mais fácil alterar o seu ambiente de computação sem alterar o seu código.  Um ciclo de vida do desenvolvimento de modelo típico:

* Comece com dev/experimentação numa pequena quantidade de dados. Nesta fase, recomendamos que utilize um ambiente local. Por exemplo, seu computador local ou uma VM com base na cloud.
* Aumentar verticalmente o seu treinamento em conjuntos de dados maiores ou distribuído treinamento através de um da [destinos de treinamento](#train).  
* Implementar para vários ambientes de alojamento na web ou dispositivos de IoT através de um da [destinos de implementação](#deploy).

Os recursos de computação que utiliza para seus destinos de computação estão anexados a um [área de trabalho](concept-workspace.md). Recursos que não seja o computador local são partilhados por utilizadores da área de trabalho de computação.

## <a name="train"></a> Destinos de treinamento

O serviço do Azure Machine Learning tem suporte variado nos recursos de computação diferentes.  Também pode anexar seus próprios recursos de computação, embora o suporte para vários cenários podem variar como detalhadas abaixo:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Destinos de implementação

Os seguintes recursos de computação podem ser utilizados para alojar a sua implementação do modelo.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Computação gerida

Um recurso de computação gerida é criado e gerenciado pelo serviço Azure Machine Learning. Este computação está otimizada para cargas de trabalho do machine learning. Computação do Azure Machine Learning é a única computação gerida a partir de 30 de Maio de 2019. Recursos de computação geridos adicionais podem ser adicionados no futuro.

### <a name="amlcompute"></a> Computação do Azure Machine Learning

Pode usar a computação do Azure Machine Learning para formação e inferência de batch (pré-visualização).  Este recurso de computação, tem:

* Cluster único ou vários node
* É dimensionado automaticamente sempre que submete uma execução 
* Gerenciamento de cluster automática e o agendamento de tarefas 
* Suporte para recursos de CPU e GPU

Pode criar instâncias de computação do Azure Machine Learning com qualquer um dos seguintes procedimentos:

* O portal do Azure
* O Azure Machine Learning SDK
* A CLI do Azure

Todos os outros recursos de computação tem de ser criados fora da área de trabalho e, em seguida, ligados ao mesmo.

## <a name="unmanaged-compute"></a>Computação não gerida

É um recurso de computação não gerido *não* gerido pelo serviço Azure Machine Learning. Criar este tipo de computação fora do Azure Machine Learning, em seguida, anexá-lo à sua área de trabalho. Recursos de computação não gerenciado podem exigem passos adicionais para si para manter ou melhorar o desempenho para cargas de trabalho do machine learning.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar destinos de computação de preparação de modelos](how-to-set-up-training-targets.md)
* [Implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md)