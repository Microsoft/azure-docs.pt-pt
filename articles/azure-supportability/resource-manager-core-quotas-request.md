---
title: Pedidos de aumentar a quota de vCPU de Gestor de recursos do Azure | Documentos da Microsoft
description: Pedidos de aumento de quota de vCPU de Gestor de recursos do Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076813"
---
# <a name="quota-increase-requests"></a>Pedidos de aumento de quota

Quotas de vCPU de Gestor de recursos para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em dois escalões para cada subscrição, em cada região. 

A primeira camada é o limite de vCPUs regionais Total (em todas as séries de VM) e a segunda camada é o por limite de vCPUs de série de VM (por exemplo, a série D vCPUs). Sempre que uma nova VM vai ser implementada, a soma da utilização de vCPUs novos e existentes para a série de VM não pode exceder a quota de vCPU aprovada para a série de VM específica. Além disso, a contagem de vCPU total de novas e existentes implementada em todas as séries de VM não deve exceder a quota de vCPUs Total Regional aprovada para a subscrição. Se qualquer um das quotas são excedido, a implementação da VM será não ser permitida.
Pode pedir um aumento de limite de quota de vCPUs para a série VM no portal do Azure. Um aumento de quota de série de VM aumenta automaticamente o limite Total Regional de vCPUs na mesma proporção. 

Quando é criada uma nova subscrição, o padrão de vCPUs regionais, o Total pode não ser igual à soma de quotas de vCPU predefinidas para todas as séries de VM individuais. Isso pode resultar numa subscrição com quota suficiente para cada série de VM individuais que pretende implementar, mas não suficientes quota de vCPUs regionais Total para todas as implementações. Neste caso, terá de submeter um pedido para aumentar o limite Total Regional de vCPUs explicitamente. Limite de total de vCPUs regionais não pode exceder a soma de quota aprovado em todas as séries VM para a região.

Saiba mais sobre quotas no [página de quotas de vCPU de Máquina Virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [subscrição do Azure e limites do serviço](https://aka.ms/quotalimits) página. 

