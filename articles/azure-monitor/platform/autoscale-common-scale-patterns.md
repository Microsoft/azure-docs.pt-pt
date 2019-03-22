---
title: Descrição geral dos padrões comuns do dimensionamento automático
description: O conheça alguns dos padrões comuns para dimensionamento automático do seu recurso no Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 8356a8c8c31a043197485b4913b4a67d7d719778
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997250"
---
# <a name="overview-of-common-autoscale-patterns"></a>Descrição geral dos padrões comuns do dimensionamento automático
Este artigo descreve alguns dos padrões comuns para dimensionar o seu recurso no Azure.

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Permite começar a utilizar

Este artigo pressupõe que está familiarizado com o dimensionamento automático. Pode [comece aqui para dimensionar o seu recurso][1]. Seguem-se alguns dos padrões comuns do dimensionamento.

## <a name="scale-based-on-cpu"></a>Dimensionar com base na CPU

Tem uma aplicação web (/ VMSS/função do serviço cloud) e

- Pretende dimensionamento out/reduzir horizontalmente com base na CPU.
- Além disso, pretender certificar-se de que existe um número mínimo de instâncias.
- Além disso, pretender certificar-se de que defina um limite máximo para o número de instâncias que pode ser dimensionado para.

![Dimensionar com base na CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Dimensionar de forma diferente aos fins de semana de vs dias da semana

Tem uma aplicação web (/ VMSS/função do serviço cloud) e

- Se desejar 3 instâncias padrão (nos dias de semana)
- Não que o tráfego no fim de semana e, por conseguinte, pretende dimensionar-se para baixo até 1 instância no fim de semana.

![Dimensionar de forma diferente aos fins de semana de vs dias da semana][3]

## <a name="scale-differently-during-holidays"></a>Dimensionar de forma diferente durante feriados

Tem uma aplicação web (/ VMSS/função do serviço cloud) e

- Pretende aumentar/reduzir verticalmente, com base na utilização da CPU por predefinição
- No entanto, durante a época festiva (ou os dias específicos que são importantes para a sua empresa) que pretende substituir os padrões e tem mais capacidade à sua disposição.

![Feriados de forma diferente em escala][4]

## <a name="scale-based-on-custom-metric"></a>Dimensionar com base na métrica personalizada

Tem um front-end da web e uma camada de API que comunica com o back-end.

- Pretende dimensionar a camada de API com base em eventos personalizados no front-end (exemplo: Pretende dimensionar o seu processo de Check-out com base no número de itens no carrinho de compras)

![Dimensionar com base na métrica personalizada][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

