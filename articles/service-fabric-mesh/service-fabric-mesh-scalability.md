---
title: Aplicações de malha de tecido de serviço Azure
description: Uma das vantagens de implementar aplicações para a Malha de Tecido de Serviço é a capacidade de dimensionar facilmente os seus serviços, manualmente ou com políticas de autoscalcificação.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384937"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Aplicações de malha de tecido de serviço de escala

Uma das principais vantagens de implementar aplicações para a Malha de Tecido de Serviço é a capacidade de você facilmente escalar os seus serviços dentro ou fora. Isto deve ser utilizado para lidar com quantidades variadas de carga nos seus serviços ou para melhorar a disponibilidade. Pode escalar manualmente os seus serviços dentro ou fora ou configurar políticas de autoscalcificação.

## <a name="manual-scaling-instances"></a>Instâncias de escala manual

No modelo de implementação do recurso da aplicação, cada serviço tem uma propriedade *replicaCount* que pode ser utilizada para definir o número de vezes que pretende que o serviço seja implementado. Uma aplicação pode consistir em vários serviços, cada serviço com um número de *replicaCount* exclusivo, que são implementados e geridos em conjunto. Para dimensionar o número de réplicas do serviço, modifique o valor de *replicaCount* para cada serviço que pretende reduzir horizontalmente no modelo de implementação ou ficheiro de parâmetros. Em seguida, atualize a aplicação.

Por exemplo, em casos de serviços de escala manual, consulte Manualmente a [escala dos seus serviços dentro ou fora](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Instâncias de serviço de autoscalcição
A escala automática é uma capacidade adicional do Tecido de Serviço para escalar dinamicamente o número de instâncias de serviço (escala horizontal). Dimensionamento automático oferece excelente elasticidade e permite o aprovisionamento ou remoção de instâncias de serviço com base na utilização de CPU ou memória.  A escala automática permite-lhe executar o número certo de instâncias de serviço para a sua carga de trabalho e otimizar para o custo.

Uma política de escala automática é definida por serviço no ficheiro de recursos de serviço. Cada política de escala é constituída por duas partes:

- Será realizado um gatilho de escala, que descreve quando será realizada a escala do serviço. Há três fatores que determinam quando o serviço vai escalar. *O limiar de carga mais baixo* é um valor que determina quando o serviço será escalado. Se a carga média de todas as instâncias das divisórias for inferior a este valor, então o serviço será escalado. *O limiar de carga superior* é um valor que determina quando o serviço será eliminado. Se a carga média de todos os casos da partição for superior a este valor, então o serviço será dimensionado. *O intervalo* de escala determina a frequência (em segundos) que o gatilho será verificado. Uma vez verificado o gatilho, se for necessário escalonamento, o mecanismo será aplicado. Se não for necessário escalar, então não serão tomadas medidas. Em ambos os casos, o gatilho não será verificado novamente antes de expirar o intervalo de escala.

- Um mecanismo de escala, que descreve como a escala será realizada quando é desencadeada. *O incremento* de escala determina quantas instâncias serão adicionadas ou removidas quando o mecanismo for acionado. *A contagem máxima de instâncias* define o limite superior para a escala. Se o número de instâncias atingir este limite, então o serviço não será dimensionado independentemente da carga. *A contagem mínima de instâncias* define o limite inferior para a escala. Se o número de instâncias da partição atingir este limite, então o serviço não será escalado independentemente da carga.

Para aprender a definir uma política de escala automática para o seu serviço, leia [os serviços de escala automática.](service-fabric-mesh-howto-auto-scale-services.md)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o modelo de aplicação, consulte [os recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md)
