---
title: Gerir o estado em serviços do Azure Service Fabric | Documentos da Microsoft
description: Saiba como definir e gerir o estado do serviço nos serviços do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870898"
---
# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se para a memória ou nos dados de disco que requer que um serviço de função. Ele inclui, por exemplo, as estruturas de dados e as variáveis de membro que o serviço de leituras e escritas para fazer o trabalho. Dependendo de como o serviço está arquitetado, também pode incluir ficheiros ou outros recursos que estão armazenados no disco. Por exemplo, os ficheiros de uma base de dados usaria para armazenar os registos de dados e transações.

Como um serviço de exemplo, vamos considerar uma calculadora. Um serviço da calculadora básica leva dois números e devolve a soma. Efetuar esse cálculo envolve a nenhuma variável de membro ou outras informações.

Agora, considere a Calculadora mesmo, mas com um método adicional para armazenar e devolver a soma de último tem computado. Este serviço agora é com monitoração de estado. Com monitorização de estado significa que ele contém algum Estado que escreve quando ele calcula uma soma de novo e lê a partir de quando pede que ela retorne a última soma calculada.

No Azure Service Fabric, o primeiro serviço denomina-se um serviço sem estado. O serviço de segundo denomina-se um serviço com estado.

## <a name="storing-service-state"></a>Armazenamento do Estado do serviço
Estado pode ser externalized ou localizado conjuntamente com o código que está manipulando o estado. Externalização de estado é geralmente feita utilizando uma base de dados externo ou noutro arquivo de dados que é executado em diferentes computadores através da rede ou fora do processo na mesma máquina. No nosso exemplo de calculadora, o arquivo de dados pode ser uma base de dados SQL ou a instância do Store de tabela do Azure. Cada solicitação para calcular a soma efetua uma atualização sobre estes dados e os pedidos para o serviço para retornar o resultado do valor no valor atual que está a ser obtido a partir da loja. 

Estado também pode ser localizado conjuntamente com o código que manipula o estado. Serviços com estado no Service Fabric são normalmente criados com este modelo. Service Fabric disponibiliza a infraestrutura para se certificar de que este estado é altamente disponível, consistente e durável e que os serviços criados dessa forma podem ser dimensionado facilmente.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre conceitos do Service Fabric, consulte os artigos seguintes:

* [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
* [Escalabilidade de serviços do Service Fabric](service-fabric-concepts-scalability.md)
* [A criação de partições de serviços do Service Fabric](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
