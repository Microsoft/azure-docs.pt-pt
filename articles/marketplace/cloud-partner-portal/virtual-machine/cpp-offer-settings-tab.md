---
title: Guia de configurações da oferta de máquina virtual no Portal do Cloud Partner para o Azure Marketplace
description: Descreve a guia de configurações de oferta usada na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 6f7b90f6b02999869026db24836091233692143c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824447"
---
# <a name="virtual-machine-offer-settings-tab"></a>Guia de configurações da oferta de máquina virtual

A **nova** página de oferta para máquinas virtuais é aberta na primeira guia denominada **configurações da oferta**.  

![Nova página de oferta para máquinas virtuais](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Campos de configurações da oferta

Na guia **configurações da oferta** , você deve fornecer os campos a seguir.  Um asterisco anexado (*) no nome do campo indica que ele é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***   | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador será visível em URLs de produto, modelos de Azure Resource Manager e relatórios de cobrança. Ele tem um comprimento máximo de 50 caracteres, só pode ser composto por caracteres alfanuméricos minúsculos e traços (-), mas não pode terminar com um traço. Este campo não pode ser alterado depois que uma oferta é ativada. <br> Por exemplo, se a contoso publica uma oferta com a ID da oferta **Sample-VM**, ele recebe a URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **\* do Publicador**  | O identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do editor. Esse valor não pode ser modificado depois que a oferta é salva. |
| **Nome\***       | Nome de exibição para sua oferta. Esse nome será exibido no Azure Marketplace e no Portal do Cloud Partner. Pode ter um máximo de 50 carateres. Aqui está a orientação para incluir um nome de marca reconhecível para seu produto. Não inclua o nome da sua organização aqui, a menos que seja como ele é comercializado. Se você estiver comercializando esta oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|   |   |
 
Clique em **salvar** depois de fornecer todos os campos. 


## <a name="next-steps"></a>Passos seguintes

Na próxima guia, você adicionará [SKUs](./cpp-skus-tab.md) à sua oferta.
