---
title: Subscrições move EA e CSP Azure VMware Solution
description: Saiba como mover a nuvem privada de uma subscrição para outra. O movimento pode ser feito por várias razões, como a faturação.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555614"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Subscrições move EA e CSP Azure VMware Solution

Neste artigo, você vai aprender a mover a nuvem privada de uma subscrição para outra. O movimento pode ser feito por várias razões, como a faturação. 

>[!IMPORTANT]
>Deve ter pelo menos direitos de contribuinte tanto nas subscrições de origem como de alvo. O gateway VNet e VNet não pode ser movido de uma subscrição para outra. Além disso, mover as suas subscrições não tem impacto na gestão e carga de trabalho, como o vCenter, NSX e máquinas virtuais de carga de trabalho.

1. Inscreva-se no portal Azure e selecione a nuvem privada que pretende mover.

1. Selecione o link **Subscrição (alteração).**

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Screenshot mostrando os detalhes da nuvem privada.":::

1. Forneça os detalhes da subscrição para **Target** e selecione **Next**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Screenshot do recurso alvo." lightbox="media/move-resources-subscription-target.png":::

1. Confirme a validação dos recursos selecionados para se deslocar e selecione **Next**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Screenshot mostrando o recurso a ser movido." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Selecione a caixa de verificação indicando que compreende que as ferramentas e scripts associados não funcionarão até que as atualize para utilizar os novos IDs de recursos. Em seguida, **selecione Mover**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Screenshot mostrando o resumo do recurso selecionado a ser movido. " lightbox="media/review-move-resources-subscription-target.png":::

   Uma notificação aparece assim que o movimento de recursos estiver completo. A nova subscrição aparece na visão geral da nuvem privada.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Screenshot mostrando uma nova subscrição." lightbox="media/moved-subscription-target.png":::

