---
title: Descrição geral dos padrões comuns de dimensionamento automático
description: Aprenda alguns dos padrões comuns para dimensionar automaticamente seu recurso no Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396100"
---
# <a name="overview-of-common-autoscale-patterns"></a>Descrição geral dos padrões comuns de dimensionamento automático
Este artigo descreve alguns dos padrões comuns para dimensionar seu recurso no Azure.

Azure Monitor dimensionamento automático se aplica somente aos [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Vamos começar

Este artigo pressupõe que você esteja familiarizado com o dimensionamento automático. Você pode começar [aqui para dimensionar seu recurso][1]. A seguir estão alguns dos padrões de escala comuns.

## <a name="scale-based-on-cpu"></a>Escala com base na CPU

Você tem um aplicativo Web (função de serviço/VMSS/cloud) e

- Você deseja escalar horizontalmente/reduzir horizontalmente com base na CPU.
- Além disso, você deseja garantir que haja um número mínimo de instâncias.
- Além disso, você deseja garantir que você defina um limite máximo para o número de instâncias para as quais você pode dimensionar.

![Escala com base na CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Dimensionar de forma diferente em dias da semana versus fins de semana

Você tem um aplicativo Web (função de serviço/VMSS/cloud) e

- Você deseja três instâncias por padrão (em dias da semana)
- Você não espera o tráfego nos finais de semana e, portanto, deseja reduzir verticalmente até 1 instância nos finais de semana.

![Dimensionar de forma diferente em dias da semana versus fins de semana][3]

## <a name="scale-differently-during-holidays"></a>Dimensione de maneira diferente durante feriados

Você tem um aplicativo Web (função de serviço/VMSS/cloud) e

- Você deseja escalar verticalmente com base no uso da CPU por padrão
- No entanto, durante a época de Natal (ou dias específicos que são importantes para seu negócio), você deseja substituir os padrões e ter mais capacidade à sua disposição.

![Dimensione de forma diferente nos feriados][4]

## <a name="scale-based-on-custom-metric"></a>Escala com base na métrica personalizada

Você tem um front-end da Web e uma camada de API que se comunica com o back-end.

- Você deseja dimensionar a camada de API com base em eventos personalizados no front-end (exemplo: você deseja dimensionar seu processo de check-out com base no número de itens no carrinho de compras)

![Escala com base na métrica personalizada][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

