---
title: Escalabilidade das aplicações de malha de tecido de serviço Azure
description: Uma das vantagens de implementar aplicações para a Rede de Tecidos de Serviço é a capacidade de escalar facilmente os seus serviços, manualmente ou com políticas de autoscaling.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712246"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Aplicações de malha de tecido de serviço de escala

Uma das principais vantagens de implementar aplicações para a Rede de Tecidos de Serviço é a capacidade de escalar facilmente os seus serviços dentro ou fora. Isto deve ser usado para lidar com quantidades variadas de carga nos seus serviços ou melhorar a disponibilidade. Pode escalar manualmente os seus serviços dentro ou fora ou configurar políticas de autoscaling.

## <a name="manual-scaling-instances"></a>Instâncias de escala manual

No modelo de implementação do recurso da aplicação, cada serviço tem uma propriedade *replicaCount* que pode ser utilizada para definir o número de vezes que pretende que o serviço seja implementado. Uma aplicação pode consistir em vários serviços, cada serviço com um número de *replicaCount* exclusivo, que são implementados e geridos em conjunto. Para dimensionar o número de réplicas do serviço, modifique o valor de *replicaCount* para cada serviço que pretende reduzir horizontalmente no modelo de implementação ou ficheiro de parâmetros. Em seguida, atualize a aplicação.

Por exemplo, casos de serviços de escala manual, consulte [manualmente escalar os seus serviços dentro ou fora](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Casos de serviço de auto-calagem
O dimensionamento automático é uma capacidade adicional do Tecido de Serviço para escalar dinamicamente o número de instâncias de serviço (escala horizontal). A escala automática proporciona uma grande elasticidade e permite o fornecimento ou remoção de instâncias de serviço com base na CPU ou na utilização da memória.  O dimensionamento automático permite-lhe executar o número certo de instâncias de serviço para a sua carga de trabalho e otimizar o custo.

Uma política de escala automática é definida por serviço no ficheiro de recursos de serviço. Cada política de escala é constituída por duas partes:

- Um gatilho de escala, que descreve quando será realizado o escalonamento do serviço. Há três fatores que determinam quando o serviço irá escalar. *O limiar de carga mais baixo* é um valor que determina quando o serviço será dimensionado. Se a carga média de todas as instâncias das divisórias for inferior a este valor, então o serviço será dimensionado. *O limiar de carga superior* é um valor que determina quando o serviço será dimensionado. Se a carga média de todas as instâncias da partição for superior a este valor, então o serviço será dimensionado. *O intervalo de escala* determina com que frequência (em segundos) o gatilho será verificado. Uma vez verificado o gatilho, se for necessário escalonamento, o mecanismo será aplicado. Se não for necessário escalar, não serão tomadas medidas. Em ambos os casos, o gatilho não será verificado novamente antes de expirar o intervalo de escala.

- Um mecanismo de escala, que descreve como o escalonamento será realizado quando é acionado. *O incremento de* escala determina quantas instâncias serão adicionadas ou removidas quando o mecanismo é acionado. *A contagem máxima de instância* define o limite superior para a escala. Se o número de ocorrências atingir este limite, o serviço não será dimensionado independentemente da carga. *A contagem mínima de instâncias* define o limite inferior para a escala. Se o número de casos da partição atingir este limite, o serviço não será dimensionado independentemente da carga.

Para aprender a definir uma política de autoescala para o seu serviço, leia [os serviços de autoescala](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre o modelo de aplicação, consulte [os recursos do Service Fabric](service-fabric-mesh-service-fabric-resources.md)
