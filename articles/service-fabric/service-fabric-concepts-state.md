---
title: Gerir o estado nos serviços de tecido de serviço Azure
description: Conheça o estado em Azure Service Fabric, incluindo como definir e gerir o estado de serviço nos serviços de Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614575"
---
# <a name="service-state"></a>Estado do serviço
**O estado de serviço** refere-se à memória ou aos dados de disco que um serviço necessita para funcionar. Inclui, por exemplo, as estruturas de dados e variáveis de membro que o serviço lê e escreve para fazer trabalho. Dependendo da forma como o serviço é arquitetado, também pode incluir ficheiros ou outros recursos que são armazenados em disco. Por exemplo, os ficheiros que uma base de dados utilizaria para armazenar dados e registos de transações.

Como um serviço de exemplo, vamos considerar uma calculadora. Um serviço de calculadora básica pega em dois números e devolve a sua soma. A realização deste cálculo não envolve variáveis de membros ou outras informações.

Agora considere a mesma calculadora, mas com um método adicional para armazenar e devolver a última soma que computou. Este serviço é agora imponente. Stateful significa que contém algum estado que escreve quando calcula uma nova soma e lê a partir de quando lhe pede para devolver a última quantia calculada.

Na Azure Service Fabric, o primeiro serviço é chamado de serviço apátrida. O segundo serviço chama-se serviço estatal.

## <a name="storing-service-state"></a>Estado de serviço de armazenamento
O Estado pode ser externalizado ou co-localizado com o código que está a manipular o Estado. A externalização do estado é normalmente feita utilizando uma base de dados externa ou outra loja de dados que funciona em diferentes máquinas através da rede ou fora do processo na mesma máquina. No nosso exemplo de calculadora, a loja de dados pode ser uma base de dados SQL ou instância da Azure Table Store. Cada pedido de cálculo da soma realiza uma atualização sobre estes dados, e solicita ao serviço que devolva o valor resultando no valor atual a ser recolhido da loja. 

O Estado também pode ser co-localizado com o código que manipula o estado. Os serviços estatais em Service Fabric são normalmente construídos usando este modelo. A Service Fabric fornece a infraestrutura para garantir que este estado está altamente disponível, consistente e durável, e que os serviços construídos desta forma podem facilmente escalar.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

* [Disponibilidade de serviços de tecido de serviço](service-fabric-availability-services.md)
* [Escalabilidade dos serviços de tecido de serviço](service-fabric-concepts-scalability.md)
* [Serviços de tecido de partilha](service-fabric-concepts-partitioning.md)
* [Serviços fiáveis de tecido de serviço](service-fabric-reliable-services-introduction.md)
