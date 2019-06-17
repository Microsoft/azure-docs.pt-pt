---
title: Separador de definições da oferta de máquina virtual no Portal de parceiros da Cloud para o Azure Marketplace
description: Descreve o separador de definições da oferta utilizado na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938160"
---
# <a name="virtual-machine-offer-settings-tab"></a>Separador de definições da oferta de máquina virtual

O **nova oferta** é aberta a página para máquinas virtuais no primeiro separador com o nome **oferecer definições**.  

![Nova página de ofertas para máquinas virtuais](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Campos de definições da oferta

Na **oferecer definições** guia, tem de fornecer os seguintes campos.  Um anexado asterisco (*) no nome do campo indica que é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de oferta\***   | Um identificador exclusivo (dentro de um perfil do publicador) para a oferta. Este identificador estará visível no produto URLs, modelos do Azure Resource Manager, e relatórios de faturação. Tem um comprimento máximo de 50 carateres, só pode ser composto por carateres alfanuméricos em minúsculas e hífenes (-), mas não pode terminar com um traço. Este campo não pode ser alterado depois de uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID da oferta **vm de exemplo**, que é atribuído o URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publisher\***  | Identificador exclusivo da sua organização, no Azure Marketplace. Todas as suas ofertas devem ser associadas com o ID de publicador. Este valor não pode ser modificado depois da oferta é guardada. |
| **Nome\***       | Nome a apresentar para a sua oferta. Este nome será apresentada no Azure Marketplace e no Portal de parceiros de nuvem. Pode ter um máximo de 50 carateres. Documentação de orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que o que é a forma como é comercializada. Se estiver de marketing esta oferta em outros Web sites e publicações, certifique-se de que o nome é exatamente o mesmo em todas as publicações. |
|   |   |
 
Clique em **guardar** depois que forneceu todos os campos. 


## <a name="next-steps"></a>Passos Seguintes

No separador seguinte, adicionará [SKUs](./cpp-skus-tab.md) para sua oferta.
