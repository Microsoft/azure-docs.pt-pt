---
title: Separador de definições de oferta de máquina virtual no portal do parceiro de nuvem para o Mercado Azure
description: Descreve o separador Definições de Oferta utilizado na criação de uma oferta VM do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: e5353797aaa7d40e1b6a95685b79cb3fe99f5ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277655"
---
# <a name="virtual-machine-offer-settings-tab"></a>Separador de oferta de máquina virtual

A página **New Offer** para máquinas virtuais abre no primeiro separador chamado **Definições**de Oferta .  

![Nova página de Oferta para máquinas virtuais](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Oferecer campos de Definições

No separador Definições de **Oferta,** deve fornecer os seguintes campos.  Um asterisco anexado (*) no nome do campo indica que é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***   | Um identificador único (dentro de um perfil de editor) para a oferta. Este identificador será visível em URLs de produto, modelos de Gestor de Recursos Azure e relatórios de faturação. Tem um comprimento máximo de 50 caracteres, só pode ser composto por caracteres alfanuméricos minúsculos e traços (-), mas não pode terminar num traço. Este campo não pode ser alterado depois de uma oferta ir ao vivo. <br> Por exemplo, se a Contoso publicar uma oferta com oferta de **id-vm,** é atribuído o URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` azure Marketplace . |
| **Editora\***  | O identificador único da sua organização no Mercado Azure. Todas as suas ofertas devem estar associadas à sua identificação do editor. Este valor não pode ser modificado uma vez que a oferta é guardada. |
| **Nome\***       | Mostrar o nome da sua oferta. Este nome será exibido no Azure Marketplace e no Portal do Parceiro cloud. Pode ter um máximo de 50 carateres. A orientação aqui é incluir uma marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que seja assim que é comercializado. Se estiver a comercializar esta oferta noutros websites e publicações, certifique-se de que o nome é exatamente o mesmo em todas as publicações. |
|   |   |
 
Clique em **Guardar** depois de ter fornecido todos os campos. 


## <a name="next-steps"></a>Passos seguintes

No separador seguinte, irá adicionar [SKUs](./cpp-skus-tab.md) à sua oferta.
