---
title: Descrição geral dos padrões comuns de dimensionamento automático
description: Aprenda alguns dos padrões comuns para escalar automaticamente o seu recurso em Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617731"
---
# <a name="overview-of-common-autoscale-patterns"></a>Descrição geral dos padrões comuns de dimensionamento automático
Este artigo descreve alguns dos padrões comuns para escalar o seu recurso em Azure.

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escalas de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/)e [serviços de Gestão API.](../../api-management/api-management-key-concepts.md)

## <a name="lets-get-started"></a>Vamos começar

Este artigo assume que está familiarizado com a escala automática. Pode [começar aqui para escalar o seu recurso.][1] Seguem-se alguns dos padrões de escala comum.

## <a name="scale-based-on-cpu"></a>Escala baseada no CPU

Você tem uma aplicação web (/VMSS/papel de serviço na nuvem) e

- Você quer escalar para fora/escala em com base no CPU.
- Além disso, pretende garantir que há um número mínimo de casos.
- Além disso, pretende certificar-se de que estabelece um limite máximo para o número de casos a que pode escalar.

![Escala baseada no CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Escala de forma diferente nos dias úteis vs fins de semana vs fins de semana

Você tem uma aplicação web (/VMSS/papel de serviço na nuvem) e

- Quer 3 instâncias por defeito (nos dias úteis)
- Não se espera tráfego aos fins de semana e, por isso, quer reduzir para 1 caso nos fins de semana.

![Escala de forma diferente nos dias úteis vs fins de semana vs fins de semana][3]

## <a name="scale-differently-during-holidays"></a>Escalar de forma diferente durante as férias

Você tem uma aplicação web (/VMSS/papel de serviço na nuvem) e

- Pretende escalar para cima/para baixo com base no uso do CPU por padrão
- No entanto, durante a época de férias (ou dias específicos que são importantes para o seu negócio) pretende anular os incumprimentos e ter mais capacidade à sua disposição.

![Escala diferente nos feriados][4]

## <a name="scale-based-on-custom-metric"></a>Escala com base na métrica personalizada

Você tem uma extremidade frontal web e um nível API que comunica com o backend.

- Pretende escalar o nível API com base em eventos personalizados na parte frontal (exemplo: Pretende escalar o seu processo de check-out com base no número de itens no carrinho de compras)

![Escala com base na métrica personalizada][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
