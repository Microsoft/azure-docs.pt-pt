---
title: Configurações de oferta para um módulo Azure IoT Edge | Azure Marketplace
description: Defina as configurações de oferta para um módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 1043f467a7363bc0e3eedba40fd2246015592276
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814130"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Guia Configurações da oferta do módulo IoT Edge

A página **IOT Edge módulos > nova oferta** é aberta com o foco na guia **configurações da oferta** . 

![Nova página de oferta para módulos de IoT Edge](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Configurações de identidade da oferta

Em **identidade da oferta**, você deve fornecer informações para os campos descritos na tabela a seguir. Um asterisco (*) acrescentado ao nome do campo indica que ele é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador será visível em relatórios de informações e URLs de produtos. Ele tem um comprimento máximo de 50 caracteres e pode usar caracteres alfanuméricos minúsculos e traços (-). (O identificador não pode terminar com um traço.) **Observação:** Este campo não pode ser alterado depois que uma oferta é ativada. <br> Por exemplo, se a contoso publicar uma oferta com a ID **de oferta de exemplo-IOT-Edge-Module**, ela será atribuída à URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **\* do Publicador**     | O identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do editor. Esse valor não pode ser alterado depois que a oferta é salva. |
| **Nome\***          | O nome de exibição para sua oferta. Esse nome é exibido no Azure Marketplace e no Portal do Cloud Partner. Pode ter um máximo de 50 carateres. É recomendável usar um nome de marca reconhecível para seu produto. Não inclua o nome da sua organização, a menos que seja como o produto é comercializado. Se você estiver comercializando esta oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |


Selecione **salvar** para salvar suas configurações de oferta.


## <a name="next-steps"></a>Passos seguintes

Use a guia [SKUs](./cpp-skus-tab.md) para configurar os SKUs para sua oferta.
