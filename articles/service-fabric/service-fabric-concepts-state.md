---
title: Gerenciar o estado nos serviços de Service Fabric do Azure
description: Saiba mais sobre o estado no Azure Service Fabric, incluindo como definir e gerenciar o estado do serviço nos serviços de Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614575"
---
# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se aos dados na memória ou em disco que um serviço requer para funcionar. Ele inclui, por exemplo, as estruturas de dados e variáveis de membro que o serviço lê e grava para fazer o trabalho. Dependendo de como o serviço é arquitetado, ele também pode incluir arquivos ou outros recursos armazenados em disco. Por exemplo, os arquivos que um banco de dados usaria para armazenar os logs de transações e de transação.

Como um serviço de exemplo, vamos considerar uma calculadora. Um serviço de calculadora básica usa dois números e retorna sua soma. A execução desse cálculo não envolve nenhuma variável de membro ou outras informações.

Agora, considere a mesma calculadora, mas com um método adicional para armazenar e retornar a última soma calculada. Este serviço agora está com estado. Com estado significa que ele contém algum estado no qual ele grava quando computa uma nova soma e lê de quando você solicita que ele retorne a última soma calculada.

No Azure Service Fabric, o primeiro serviço é chamado de serviço sem estado. O segundo serviço é chamado de serviço com estado.

## <a name="storing-service-state"></a>Armazenando o estado do serviço
O estado pode ser externo ou colocalizado com o código que está manipulando o estado. A externalização do Estado normalmente é feita com o uso de um banco de dados externo ou de outro armazenamento que é executado em máquinas diferentes na rede ou fora do processo no mesmo computador. Em nosso exemplo de calculadora, o repositório de dados pode ser um banco de dado SQL ou uma instância do repositório de tabelas do Azure. Cada solicitação para calcular a soma executa uma atualização sobre esses dados, e as solicitações para o serviço retornam o resultado no valor atual que está sendo obtido do repositório. 

O estado também pode ser colocalizado com o código que manipula o estado. Os serviços com estado no Service Fabric normalmente são criados usando esse modelo. Service Fabric fornece a infraestrutura para garantir que esse Estado seja altamente disponível, consistente e durável, e que os serviços criados dessa maneira possam ser facilmente dimensionados.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre conceitos de Service Fabric, consulte os seguintes artigos:

* [Disponibilidade de serviços de Service Fabric](service-fabric-availability-services.md)
* [Escalabilidade dos serviços de Service Fabric](service-fabric-concepts-scalability.md)
* [Particionando serviços de Service Fabric](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
