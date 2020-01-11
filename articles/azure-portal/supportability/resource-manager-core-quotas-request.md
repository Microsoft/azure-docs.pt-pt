---
title: Azure Resource Manager solicitações de aumento de cota de vCPU | Microsoft Docs
description: Azure Resource Manager solicitações de aumento de cota de vCPU
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897107"
---
# <a name="quota-increase-requests"></a>Pedidos de aumento de quota

As cotas de vCPU do Resource Manager para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em duas camadas para cada assinatura, em cada região. 

A primeira camada é o limite de vCPUs regional total (em toda a série de VM), e a segunda camada é o limite de vCPUs por série de VMs (como a série D vCPUs). Sempre que uma nova VM for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.
Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor. 

Quando uma nova assinatura é criada, o vCPUs regional total padrão pode não ser igual à soma de cotas de vCPU padrão para todas as séries de VMs individuais. Isso pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar, mas não cota suficiente para o total de vCPUs regionais para todas as implantações. Nesse caso, será necessário enviar uma solicitação para aumentar o limite de vCPUs regional total explicitamente. O limite de vCPUs regional total não pode exceder a soma da cota aprovada em todas as séries de VM para a região.

Saiba mais sobre cotas na [página de cotas do vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e na página [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits) . 

