---
title: Grupos inteligentes
description: Grupos Inteligentes são agregações de alertas que ajudam a reduzir o ruído de alerta
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: 8a164fe6e5fd5be58da969d9266329755705ea82
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037970"
---
# <a name="smart-groups"></a>Grupos inteligentes

Um desafio comum que se coloca ao lidar com os alertas é vasculhar o ruído para descobrir o que realmente importa - os grupos inteligentes destinam-se a ser a solução para esse problema.  

Os grupos inteligentes são criados automaticamente usando algoritmos de aprendizagem automática para combinar alertas relacionados que representam um único problema.  Quando um alerta é criado, o algoritmo adiciona-o a um novo grupo inteligente ou a um grupo inteligente existente com base em informações como padrões históricos, propriedades semelhantes e estrutura semelhante. Por exemplo, se % cpU em várias máquinas virtuais numa subscrição simultaneamente aumenta levando a muitos alertas individuais, e se esses alertas ocorrerem juntos a qualquer momento no passado, estes alertas provavelmente serão agrupados num único Grupo Inteligente, sugerindo uma potencial causa comum. Isto significa que, para alguém que elimina alertas de resolução de problemas, os grupos inteligentes não só lhes permitem reduzir o ruído gerindo alertas relacionados como uma única unidade agregada, como também os orienta para possíveis causas comuns para os seus alertas.

Atualmente, o algoritmo apenas considera alertas do mesmo serviço de monitor dentro de uma subscrição. Os grupos inteligentes podem reduzir até 99% do ruído de alerta através desta consolidação. Pode ver a razão pela qual os alertas foram incluídos num grupo na página de detalhes do grupo inteligente.

Você pode ver os detalhes de grupos inteligentes e definir o estado da mesma forma que você pode com os alertas. Cada alerta é um membro de um e único grupo inteligente. 

## <a name="smart-group-state"></a>Estado de grupo inteligente

O estado do grupo inteligente é um conceito semelhante ao estado de alerta, que lhe permite gerir o processo de resolução ao nível de um grupo inteligente. À semelhança do estado de alerta, quando um grupo inteligente é criado, tem o Estado **Novo,** que pode ser alterado para **reconhecido** ou **fechado.**

Os seguintes estados inteligentes do grupo são apoiados.

| Estado | Descrição |
|:---|:---|
| Novo | A questão acaba de ser detetada e ainda não foi revista. |
| Confirmado | Um administrador reviu o grupo inteligente e começou a trabalhar nele. |
| Fechado | A questão foi resolvida. Depois de um grupo inteligente ter sido fechado, pode reabri-lo mudando-o para outro estado. |

[Aprenda a mudar o estado do seu grupo inteligente.](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

> [!NOTE]
>  Mudar o estado de um grupo inteligente não altera o estado dos alertas individuais dos membros.

## <a name="smart-group-details-page"></a>Página de detalhes do grupo inteligente

A página de detalhes do grupo Smart é apresentada quando seleciona um grupo inteligente. Fornece detalhes sobre o grupo inteligente, incluindo o raciocínio que foi usado para criar o grupo, e permite-lhe mudar o seu estado.
 
![Detalhe de grupo inteligente](media/alerts-smartgroups-overview/smart-group-detail.png)


A página de detalhe do grupo inteligente inclui as seguintes secções.

| Section | Descrição |
|:---|:---|
| Alertas | Lista os alertas individuais incluídos no grupo inteligente. Selecione um alerta para abrir a sua página de detalhes de alerta. |
| Histórico | Enumera cada ação tomada pelo grupo inteligente e quaisquer alterações que lhe sejam feitas. Isto está atualmente limitado a alterações de estado e alterações de adesão de alerta. |

## <a name="smart-group-taxonomy"></a>Taxonomia de grupo inteligente

O nome de um grupo inteligente é o nome do seu primeiro alerta. Não se pode criar ou mudar o nome de um grupo inteligente.

## <a name="next-steps"></a>Passos seguintes

- [Gerir grupos inteligentes](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Mude o seu estado de alerta e grupo inteligente](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)