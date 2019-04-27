---
title: Separador de definições da oferta de máquina virtual no Portal de parceiros da Cloud para o Azure Marketplace | Documentos da Microsoft
description: Descreve o separador de definições da oferta utilizado na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844361"
---
# <a name="virtual-machine-offer-settings-tab"></a>Separador de definições da oferta de máquina virtual

O **nova oferta** é aberta a página para máquinas virtuais no primeiro separador com o nome **oferecer definições**.  Um anexado asterisco (*) no nome do campo indica que é necessário. 

![Nova página de ofertas para máquinas virtuais](./media/publishvm_004.png)

Na **oferecer definições** guia, tem de fornecer os seguintes campos obrigatórios.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de oferta**       | Um identificador exclusivo (dentro de um perfil do publicador) para a oferta. Este identificador estará visível no produto URLs, modelos do Azure Resource Manager, e relatórios de faturação. Tem um comprimento máximo de 50 carateres, só pode ser composto por carateres alfanuméricos em minúsculas e hífenes (-), mas não pode terminar com um traço. Este campo não pode ser alterado depois de uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID da oferta **vm de exemplo**, que é atribuído o URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publicador**     | Identificador exclusivo da sua organização, no Azure Marketplace. Todas as suas ofertas devem ser associadas com o ID de publicador. Este valor não pode ser modificado depois da oferta é guardada. |
| **Nome**          | Nome a apresentar para a sua oferta. Este nome será apresentada no Azure Marketplace e no Portal de parceiros de nuvem. Pode ter um máximo de 50 carateres. Documentação de orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que o que é a forma como é comercializada. Se estiver de marketing esta oferta em outros Web sites e publicações, certifique-se de que o nome é exatamente o mesmo em todas as publicações. |
|  |  |
 
Clique em **guardar** para guardar o seu progresso. No separador seguinte, adicionará [SKUs](./cpp-skus-tab.md) para sua oferta.
