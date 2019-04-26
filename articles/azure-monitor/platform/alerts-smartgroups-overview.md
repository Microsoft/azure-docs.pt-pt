---
title: Grupos inteligentes
description: Os grupos de inteligentes são as agregações de alertas que o ajudam a reduzem o ruído de alerta
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: e0bef0fc4f4b61add24c243af0dac64933ad5bab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346339"
---
# <a name="smart-groups"></a>Grupos inteligentes
Um desafio comuns enfrentado ao lidar com alertas é pesquisar minuciosamente o ruído para descobrir o que realmente importa - inteligentes grupos destinam-se a ser a solução para esse problema.  

Grupos inteligentes são criados automaticamente, usando algoritmos de machine learning para combinar os alertas relacionados que representam um único problema.  Quando é criado um alerta, o algoritmo adiciona-o para um novo grupo inteligente ou um grupo existente inteligente, com base nas informações como padrões históricos, propriedades semelhantes e estrutura semelhante. Por exemplo, se % da CPU em várias máquinas virtuais numa subscrição picos em simultâneo que leva a muitos alertas individuais e se esses alertas ocorreram em conjunto em qualquer altura no passado, estes alertas provavelmente irão ser agrupados num único grupo de Smart, sugira um potencial causa de raiz comuns. Isso significa que alguém alertas de resolução de problemas, grupos inteligentes não só permite-los para reduzir o ruído ao gerir alertas relacionados como uma única unidade de agregados, ele também orienta-os às possíveis causas comuns para os seus alertas.

Atualmente, o algoritmo apenas os alertas do serviço de monitor do mesmo numa subscrição. Grupos inteligentes podem reduzir a até 99% do ruído de alertas por meio desta consolidação. Pode ver o motivo que os alertas foram incluídos num grupo na página de detalhes do grupo inteligente.

Pode ver os detalhes dos grupos inteligentes e definir o estado da mesma forma para como é possível com alertas. Cada alerta é um membro do grupo de smart de apenas um. 

## <a name="smart-group-state"></a>Estado do grupo inteligente
Estado do grupo inteligente é um conceito semelhante para o estado do alerta, que permite-lhe gerir o processo de resolução com o nível de um grupo inteligente. Assim como o estado do alerta, quando é criado um grupo inteligente, ele tem o **New** Estado, o que pode ser alterado para **confirmado** ou **fechado**.

São suportados os seguintes Estados de grupo inteligente.

| Estado | Descrição |
|:---|:---|
| Novo | O problema apenas foi detetado e ainda não foi revisto. |
| Confirmado | Um administrador tem revisto o grupo inteligente e começou a trabalhar no mesmo. |
| Fechado | O problema foi resolvido. Depois de um grupo inteligente foi fechado, pode abri-lo novamente ao alterá-la para outro Estado. |

[Saiba como alterar o estado do seu grupo inteligente.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Alterar o estado de um grupo inteligente não altera o estado dos alertas membro individual.

## <a name="smart-group-details-page"></a>Página de detalhes do grupo inteligente

A página de detalhes do grupo inteligente é apresentada ao selecionar um grupo inteligente. Fornece detalhes sobre o grupo inteligente, incluindo o raciocínio que foi utilizado para criar o grupo e permite-lhe alterar o seu estado.
 
![Detalhes do grupo de inteligente](media/alerts-smartgroups-overview/smart-group-detail.png)


A página de detalhes do grupo inteligente inclui as secções seguintes.

| Section | Descrição |
|:---|:---|
| Alertas | Lista os alertas individuais que estão incluídos no grupo de inteligente. Selecione um alerta para abrir a página de detalhes de alerta. |
| Histórico | Apresenta uma lista de cada ação tomada pelo grupo de smart e quaisquer alterações efetuadas ao mesmo. Isso está limitado a alterações de estado e as alterações na associação de alerta. |

## <a name="smart-group-taxonomy"></a>Taxonomia de grupo inteligente

O nome de um grupo inteligente é o nome do seu primeiro alerta. Não é possível criar ou mudar o nome de um grupo inteligente.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir grupos inteligentes](https://aka.ms/managing-smart-groups)
- [Alterar o estado do grupo de alerta e inteligente](https://aka.ms/managing-alert-smart-group-states)

