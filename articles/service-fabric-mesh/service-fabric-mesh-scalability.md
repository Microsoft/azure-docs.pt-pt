---
title: Aplicações de Mesh de escalabilidade do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre o dimensionamento de serviços na malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339089"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Dimensionamento de aplicações de Mesh do Service Fabric

Uma das principais vantagens da implementação de aplicações no Service Fabric Mesh é a capacidade de reduzir ou aumentar horizontalmente os seus serviços. Deve utilizar a redução ou o aumento para processar quantidades diferentes de carga nos serviços ou melhorar a disponibilidade. Pode dimensionar manualmente suas services in ou out ou políticas de dimensionamento automático de configuração.

## <a name="manual-scaling-instances"></a>Instâncias de dimensionamento manuais

No modelo de implementação do recurso da aplicação, cada serviço tem uma propriedade *replicaCount* que pode ser utilizada para definir o número de vezes que pretende que o serviço seja implementado. Uma aplicação pode consistir em vários serviços, cada serviço com um número de *replicaCount* exclusivo, que são implementados e geridos em conjunto. Para dimensionar o número de réplicas do serviço, modifique o valor de *replicaCount* para cada serviço que pretende reduzir horizontalmente no modelo de implementação ou ficheiro de parâmetros. Em seguida, atualize a aplicação.

Para obter exemplos de dimensionar manualmente as instâncias dos serviços, consulte [Dimensionar manualmente os seus serviços dentro ou para fora](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Instâncias de serviço de dimensionamento automático
Dimensionamento automático é uma capacidade adicional do Service Fabric para dimensionar dinamicamente o número de instâncias de serviço (dimensionamento horizontal). Dimensionamento automático oferece excelente elasticidade e permite o aprovisionamento ou remoção de instâncias de serviço com base na utilização de CPU ou memória.  Dimensionamento automático permite-lhe executar o número certo de instâncias de serviço para a sua carga de trabalho e otimizar o custo.

Uma política de dimensionamento de automática é definida por serviço no arquivo de recursos de serviço. Cada política de dimensionamento consiste em duas partes:

- Um acionador de dimensionamento, que descreve quando o dimensionamento do serviço será executado. Existem três fatores que determinam quando o serviço será dimensionada. *Limiar de carregamento inferior* é um valor que determina quando o serviço irá ser reduzido horizontalmente. Se a carga média de todas as instâncias das partições for inferior este valor, em seguida, o serviço irá ser reduzido horizontalmente. *Limiar de carregamento superior* é um valor que determina quando o serviço irá ser aumentado horizontalmente. Se a carga média de todas as instâncias da partição é maior do que este valor, em seguida, o serviço irá ser dimensionado. *Intervalo de dimensionamento* determina com que frequência (em segundos) será verificado o acionador. Assim que o acionador for selecionado, se o dimensionamento é necessário o mecanismo será aplicado. Se não for necessário dimensionamento, irá ser efetuada nenhuma ação. Em ambos os casos, acionador não estará marcado novamente antes de expira o intervalo de dimensionamento.

- Um mecanismo dimensionamento, que descreve como dimensionar será efetuada quando for acionado. *Dimensionar o incremento* determina quantas instâncias serão adicionadas ou removidas quando o mecanismo é acionado. *Número máximo de instâncias* define o limite superior para dimensionamento. Se o número de instâncias atingir este limite, em seguida, o serviço será não ser dimensionado, independentemente da carga. *Contagem de instâncias mínima* define o limite inferior para dimensionamento. Se o número de instâncias da partição atingir este limite, em seguida, serviço será não ser reduzido horizontalmente, independentemente da carga.

Para saber como definir uma política de dimensionamento automático para o seu serviço, leia [serviços de dimensionamento automático](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre o modelo de aplicativo, consulte [recursos de infraestrutura do serviço](service-fabric-mesh-service-fabric-resources.md)
