---
title: Grupos inteligentes
description: Grupos Inteligentes são agregações de alertas que ajudam a reduzir o ruído de alerta
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665498"
---
# <a name="smart-groups"></a>Grupos inteligentes

Um desafio comum enfrentado quando se lida com alertas é vasculhar o ruído para descobrir o que realmente importa - os grupos inteligentes pretendem ser a solução para esse problema.  

Os grupos inteligentes são automaticamente criados usando algoritmos de aprendizagem automática para combinar alertas relacionados que representam uma única questão.  Quando um alerta é criado, o algoritmo adiciona-o a um novo grupo inteligente ou a um grupo inteligente existente baseado em informações como padrões históricos, propriedades semelhantes e estrutura semelhante. Por exemplo, se % cpU em várias máquinas virtuais numa subscrição simultaneamente picos que conduzem a muitos alertas individuais, e se tais alertas ocorreram juntos em qualquer momento no passado, estes alertas provavelmente serão agrupados em um único Grupo Inteligente, sugerindo um potencial causa raiz comum. Isto significa que, para alguém que resolue os alertas, os grupos inteligentes não só lhes permitem reduzir o ruído gerindo alertas relacionados como uma única unidade agregada, como também os guia para possíveis causas de raiz comuns para os seus alertas.

Atualmente, o algoritmo apenas considera alertas do mesmo serviço de monitor dentro de uma subscrição. Os grupos inteligentes podem reduzir até 99% do ruído de alerta através desta consolidação. Pode ver a razão pela qual os alertas foram incluídos num grupo na página de detalhes do grupo inteligente.

Pode ver os detalhes de grupos inteligentes e definir o estado da mesma forma que pode com alertas. Cada alerta é membro de um grupo inteligente. 

## <a name="smart-group-state"></a>Estado de grupo inteligente

O estado de grupo inteligente é um conceito semelhante ao estado de alerta, que lhe permite gerir o processo de resolução ao nível de um grupo inteligente. À semelhança do estado de alerta, quando um grupo inteligente é criado, tem o Estado **Novo,** que pode ser alterado para **Reconhecido** ou **Fechado**.

Os seguintes estados inteligentes são apoiados.

| Estado | Descrição |
|:---|:---|
| Novo | A questão acaba de ser detetada e ainda não foi revista. |
| Confirmado | Um administrador reviu o grupo inteligente e começou a trabalhar nele. |
| Fechado | A questão foi resolvida. Depois de um grupo inteligente ter sido fechado, pode reabri-lo mudando-o para outro estado. |

[Aprenda a mudar o estado do seu grupo inteligente.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Mudar o estado de um grupo inteligente não altera o estado dos alertas individuais.

## <a name="smart-group-details-page"></a>Página de detalhes de grupo inteligente

A página de detalhes do grupo Smart é exibida quando seleciona um grupo inteligente. Fornece detalhes sobre o grupo inteligente, incluindo o raciocínio que foi usado para criar o grupo, e permite-lhe mudar o seu estado.
 
![Detalhe de grupo inteligente](media/alerts-smartgroups-overview/smart-group-detail.png)


A página de detalhes do grupo inteligente inclui as seguintes secções.

| Section | Descrição |
|:---|:---|
| Alertas | Lista os alertas individuais que estão incluídos no grupo inteligente. Selecione um alerta para abrir a sua página de detalhes de alerta. |
| Histórico | Enumera cada ação tomada pelo grupo inteligente e quaisquer alterações que lhe sejam feitas. Isto limita-se atualmente a alterações estatais e a alterações de adesão de alerta. |

## <a name="smart-group-taxonomy"></a>Taxonomia de grupo inteligente

O nome de um grupo inteligente é o nome do seu primeiro alerta. Não se pode criar ou mudar o nome de um grupo inteligente.

## <a name="next-steps"></a>Passos seguintes

- [Gerir grupos inteligentes](https://aka.ms/managing-smart-groups)
- [Mude o seu estado de alerta e grupo inteligente](https://aka.ms/managing-alert-smart-group-states)


