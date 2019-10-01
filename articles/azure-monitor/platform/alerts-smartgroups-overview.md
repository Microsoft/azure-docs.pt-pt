---
title: Grupos inteligentes
description: Os grupos inteligentes são agregações de alertas que ajudam a reduzir o ruído de alerta
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 08657163eb170b8d3185ce15bd724f1f55bbe2bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702863"
---
# <a name="smart-groups"></a>Grupos inteligentes
Um desafio comum enfrentado ao lidar com alertas é examinar o ruído para descobrir o que realmente importa, os grupos inteligentes se destinam a ser a solução para esse problema.  

Os grupos inteligentes são criados automaticamente usando algoritmos de aprendizado de máquina para combinar alertas relacionados que representam um único problema.  Quando um alerta é criado, o algoritmo o adiciona a um novo grupo inteligente ou a um grupo inteligente existente com base em informações como padrões históricos, propriedades semelhantes e estrutura semelhante. Por exemplo, se% CPU em várias máquinas virtuais em uma assinatura for simultaneamente picos que levam a muitos alertas individuais, e se esses alertas ocorrerem juntos a qualquer momento no passado, esses alertas provavelmente serão agrupados em um único grupo inteligente, sugerindo um possível causa raiz comum. Isso significa que, para alertas de solução de problemas, os grupos inteligentes não apenas permitem que eles reduzam o ruído ao gerenciar alertas relacionados como uma única unidade agregada, ele também os orienta em direção a possíveis causas raiz comuns para seus alertas.

Atualmente, o algoritmo só considera alertas do mesmo serviço de monitor em uma assinatura. Os grupos inteligentes podem reduzir até 99% do ruído de alerta por meio dessa consolidação. Você pode exibir o motivo pelo qual os alertas foram incluídos em um grupo na página de detalhes do grupo inteligente.

Você pode exibir os detalhes de grupos inteligentes e definir o estado da mesma forma como é possível com alertas. Cada alerta é um membro de um e apenas um grupo inteligente. 

## <a name="smart-group-state"></a>Estado do grupo inteligente
O estado do grupo inteligente é um conceito semelhante ao estado do alerta, que permite que você gerencie o processo de resolução no nível de um grupo inteligente. Semelhante ao estado do alerta, quando um grupo inteligente é criado, ele tem o **novo** estado, que pode ser alterado para **confirmado** ou **fechado**.

Há suporte para os seguintes Estados de grupo inteligente.

| State | Descrição |
|:---|:---|
| Novo | O problema acabou de ser detectado e ainda não foi revisado. |
| Reconhecido | Um administrador analisou o grupo inteligente e começou a trabalhar nele. |
| Fechado | O problema foi resolvido. Depois que um grupo inteligente for fechado, você poderá reabri-lo alterando-o para outro Estado. |

[Saiba como alterar o estado do seu grupo inteligente.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Alterar o estado de um grupo inteligente não altera o estado dos alertas de membro individuais.

## <a name="smart-group-details-page"></a>Página de detalhes do grupo inteligente

A página de detalhes do grupo inteligente é exibida quando você seleciona um grupo inteligente. Ele fornece detalhes sobre o grupo inteligente, incluindo o raciocínio que foi usado para criar o grupo e permite que você altere seu estado.
 
![Detalhe do grupo inteligente](media/alerts-smartgroups-overview/smart-group-detail.png)


A página de detalhes do grupo inteligente inclui as seções a seguir.

| Section | Descrição |
|:---|:---|
| Alertas | Lista os alertas individuais que estão incluídos no grupo inteligente. Selecione um alerta para abrir a página de detalhes do alerta. |
| Histórico | Lista cada ação tomada pelo grupo inteligente e quaisquer alterações feitas nele. Atualmente, isso está limitado a alterações de estado e a alterações de associação de alerta. |

## <a name="smart-group-taxonomy"></a>Taxonomia de grupo inteligente

O nome de um grupo inteligente é o nome de seu primeiro alerta. Não é possível criar ou renomear um grupo inteligente.

## <a name="next-steps"></a>Passos seguintes

- [Gerenciar grupos inteligentes](https://aka.ms/managing-smart-groups)
- [Alterar o estado do alerta e do grupo inteligente](https://aka.ms/managing-alert-smart-group-states)


