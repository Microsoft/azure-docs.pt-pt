---
title: Gerir o Estado nos serviços azure service fabric
description: Conheça o Estado em Tecido de Serviço Azure, incluindo como definir e gerir o estado de serviço nos serviços de Fabricação de Serviço.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614575"
---
# <a name="service-state"></a>Estado do serviço
**O estado** de serviço refere-se à memória ou aos dados do disco que um serviço necessita para funcionar. Inclui, por exemplo, as estruturas de dados e variáveis membros que o serviço lê e escreve para fazer trabalho. Dependendo da forma como o serviço é arquitetado, também pode incluir ficheiros ou outros recursos que sejam armazenados em disco. Por exemplo, os ficheiros que uma base de dados utilizaria para armazenar registos de dados e transações.

Como um serviço de exemplo, vamos considerar uma calculadora. Um serviço de calculadora básica leva dois números e devolve a sua soma. A realização deste cálculo não envolve variáveis de membros ou outras informações.

Agora considere a mesma calculadora, mas com um método adicional para armazenar e devolver a última soma que calculou. Este serviço é agora imponente. Audazes significa que contém algum estado ao qual escreve quando calcula uma nova soma e lê de quando lhe pede saque para devolver a última soma calculada.

No Azure Service Fabric, o primeiro serviço é chamado de serviço apátrida. O segundo serviço é chamado de serviço estatal.

## <a name="storing-service-state"></a>Estado de serviço de armazenamento
O Estado pode ser externo ou co-localizado com o código que está a manipular o Estado. A externalização do Estado é normalmente feita utilizando uma base de dados externa ou outra loja de dados que funciona em diferentes máquinas através da rede ou fora do processo na mesma máquina. No exemplo da nossa calculadora, a loja de dados pode ser uma base de dados SQL ou instância da Azure Table Store. Cada pedido para calcular a soma realiza uma atualização destes dados, e solicita ao serviço que devolva o valor resultando no valor atual a ser recolhido da loja. 

O Estado também pode ser co-localizado com o código que manipula o Estado. Os serviços estatais em Tecido de Serviço são normalmente construídos usando este modelo. A Service Fabric fornece a infraestrutura para garantir que este estado está altamente disponível, consistente e durável, e que os serviços construídos desta forma podem facilmente escalar.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

* [Disponibilidade de serviços de Tecido de Serviço](service-fabric-availability-services.md)
* [Escalabilidade dos serviços de Tecido de Serviço](service-fabric-concepts-scalability.md)
* [Serviço de Partição Serviço De Tecido](service-fabric-concepts-partitioning.md)
* [Serviço sintetizado de tecido](service-fabric-reliable-services-introduction.md)
