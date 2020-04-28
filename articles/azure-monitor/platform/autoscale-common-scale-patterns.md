---
title: Descrição geral dos padrões comuns de dimensionamento automático
description: Aprenda alguns dos padrões comuns para escalar automaticamente o seu recurso em Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75396100"
---
# <a name="overview-of-common-autoscale-patterns"></a>Descrição geral dos padrões comuns de dimensionamento automático
Este artigo descreve alguns dos padrões comuns para escalar o seu recurso em Azure.

A escala automática Do Monitor Azure aplica-se apenas a conjuntos de escala de [máquinas virtuais, serviços](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [de cloud,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e serviços de [Gestão API.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

## <a name="lets-get-started"></a>Vamos começar

Este artigo assume que está familiarizado com a escala automática. Pode [começar aqui para escalar o seu recurso.][1] Seguem-se alguns dos padrões de escala comum.

## <a name="scale-based-on-cpu"></a>Escala baseada em CPU

Tem uma aplicação web (/VMSS/cloud service) e

- Pretende escalar/escalar com base na CPU.
- Além disso, pretende garantir que há um número mínimo de ocorrências.
- Além disso, pretende garantir que estabelece um limite máximo para o número de casos a que pode escalar.

![Escala baseada em CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Escala de forma diferente nos dias úteis vs fins de semana

Tem uma aplicação web (/VMSS/cloud service) e

- Deseja 3 instâncias por padrão (nos dias úteis)
- Não se espera tráfego aos fins de semana e, por isso, quer reduzir para 1 instância nos fins de semana.

![Escala de forma diferente nos dias úteis vs fins de semana][3]

## <a name="scale-differently-during-holidays"></a>Escala de forma diferente durante as férias

Tem uma aplicação web (/VMSS/cloud service) e

- Você quer escalar para cima/para baixo com base no uso de CPU por padrão
- No entanto, durante a época festiva (ou dias específicos que são importantes para o seu negócio) pretende anular os incumprimentos e ter mais capacidade à sua disposição.

![Escala de forma diferente nos feriados][4]

## <a name="scale-based-on-custom-metric"></a>Escala com base na métrica personalizada

Você tem uma extremidade frontal web e um nível DeAPi que comunica com o backend.

- Deseja escalar o nível API com base em eventos personalizados na parte frontal (exemplo: Quer escalar o seu processo de check-out com base no número de itens no carrinho de compras)

![Escala com base na métrica personalizada][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

