---
title: Escalabilidade de aplicativos de malha de Service Fabric do Azure
description: Uma das vantagens de implantar aplicativos em Service Fabric malha é a capacidade de dimensionar facilmente seus serviços, seja manualmente ou com políticas de dimensionamento automático.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458994"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Dimensionamento de aplicativos de malha Service Fabric

Uma das principais vantagens da implantação de aplicativos em Service Fabric malha é a capacidade de você facilmente dimensionar seus serviços para dentro ou para fora. Isso deve ser usado para lidar com quantidades diferentes de carga em seus serviços ou para melhorar a disponibilidade. Você pode dimensionar manualmente seus serviços para dentro ou para fora ou configurar políticas de dimensionamento automático.

## <a name="manual-scaling-instances"></a>Instâncias de dimensionamento manual

No modelo de implementação do recurso da aplicação, cada serviço tem uma propriedade *replicaCount* que pode ser utilizada para definir o número de vezes que pretende que o serviço seja implementado. Uma aplicação pode consistir em vários serviços, cada serviço com um número de *replicaCount* exclusivo, que são implementados e geridos em conjunto. Para dimensionar o número de réplicas do serviço, modifique o valor de *replicaCount* para cada serviço que pretende reduzir horizontalmente no modelo de implementação ou ficheiro de parâmetros. Em seguida, atualize a aplicação.

Para obter exemplos de dimensionamento manual de instâncias de serviços, consulte [dimensionar manualmente os serviços para dentro ou para fora](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Dimensionamento automático de instâncias de serviço
O dimensionamento automático é um recurso adicional de Service Fabric para dimensionar dinamicamente o número de suas instâncias de serviço (dimensionamento horizontal). Dimensionamento automático oferece excelente elasticidade e permite o aprovisionamento ou remoção de instâncias de serviço com base na utilização de CPU ou memória.  O dimensionamento automático permite que você execute o número certo de instâncias de serviço para sua carga de trabalho e otimize o custo.

Uma política de dimensionamento automático é definida por serviço no arquivo de recurso de serviço. Cada política de dimensionamento consiste em duas partes:

- Um gatilho de dimensionamento, que descreve quando o dimensionamento do serviço será executado. Há três fatores que determinam quando o serviço será dimensionado. *Limite de carga inferior* é um valor que determina quando o serviço será dimensionado horizontalmente. Se a carga média de todas as instâncias das partições for menor que esse valor, o serviço será dimensionado no. *Limite de carga superior* é um valor que determina quando o serviço será escalado horizontalmente. Se a carga média de todas as instâncias da partição for maior que esse valor, o serviço será escalado horizontalmente. O *intervalo de dimensionamento* determina com que frequência (em segundos) o gatilho será verificado. Quando o gatilho for verificado, se for necessário dimensionar, o mecanismo será aplicado. Se o dimensionamento não for necessário, nenhuma ação será executada. Em ambos os casos, o gatilho não será verificado novamente antes de o intervalo de dimensionamento expirar.

- Um mecanismo de dimensionamento, que descreve como o dimensionamento será executado quando for disparado. O *incremento de escala* determina quantas instâncias serão adicionadas ou removidas quando o mecanismo for disparado. *Contagem máxima de instâncias* define o limite superior para o dimensionamento. Se o número de instâncias atingir esse limite, o serviço não será escalado horizontalmente, independentemente da carga. A *contagem mínima de instâncias* define o limite inferior para o dimensionamento. Se o número de instâncias da partição atingir esse limite, o serviço não será dimensionado, independentemente da carga.

Para saber como definir uma política de dimensionamento automático para seu serviço, leia [serviços de dimensionamento automático](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o modelo de aplicativo, consulte [recursos de Service Fabric](service-fabric-mesh-service-fabric-resources.md)
