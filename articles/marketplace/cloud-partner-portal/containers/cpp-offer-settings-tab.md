---
title: Configurações da oferta para uma imagem de contêineres do Azure | Azure Marketplace
description: Defina as configurações de oferta para um contêiner do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8742f4eb6ce8f3e1b7c4b0449c2f19d8a254e5dc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823190"
---
# <a name="container-offer-settings-tab"></a>Guia Configurações da oferta do contêiner

A página **contêineres > nova oferta** é aberta com o foco na guia **configurações da oferta** . 

![Identidade da oferta](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Configurações de identidade da oferta

Em **identidade da oferta**, você deve fornecer informações para os campos descritos na tabela a seguir. Um asterisco (*) acrescentado ao nome do campo indica que ele é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador será visível em relatórios de informações e URLs de produtos. Ele tem um comprimento máximo de 50 caracteres e pode usar caracteres alfanuméricos minúsculos e traços (-). (O identificador não pode terminar com um traço.) **Observação:** Este campo não pode ser alterado depois que uma oferta é ativada. <br> Por exemplo, se a contoso publicar uma oferta com a ID **de oferta Sample-container**, ela será atribuída à URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID do Publicador\***     | O identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do editor. Esse valor não pode ser alterado depois que a oferta é salva. |
| **Nome\***          | O nome de exibição para sua oferta. Esse nome é exibido no Azure Marketplace e no Portal do Cloud Partner. Pode ter um máximo de 50 carateres. É recomendável usar um nome de marca reconhecível para seu produto. Não inclua o nome da sua organização, a menos que seja como o produto é comercializado. Se você estiver comercializando esta oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |

Selecione **salvar** para salvar suas configurações de oferta.


## <a name="next-steps"></a>Passos seguintes

Use a guia [SKUs](./cpp-skus-tab.md) para configurar os SKUs para sua oferta.
