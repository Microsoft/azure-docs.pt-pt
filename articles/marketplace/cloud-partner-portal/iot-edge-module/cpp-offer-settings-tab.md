---
title: Definições de um módulo do Azure IoT Edge da oferta | O Azure Marketplace
description: Configure definições de oferta de um módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 75781f09f4ca1eb2c3dbd176508fb233036e6776
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942261"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Separador de definições da oferta de módulo do IoT Edge

O **módulos do IoT Edge > nova oferta** página abre-se com o foco no **oferecer definições** separador. 

![Nova página de oferta para módulos do IoT Edge](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Definições de identidade da oferta

Sob **oferecer identidade**, tem de fornecer informações para os campos descrito na tabela seguinte. Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de oferta\***       | Um identificador exclusivo (dentro de um perfil do publicador) para a oferta. Este identificador serão visível nos relatórios de URLs e as informações do produto. Ele tem um comprimento máximo de 50 carateres e pode utilizar carateres minúsculos de alfanuméricos e traços (-). (O identificador não pode terminar com um hífen.) **Nota:** Este campo não pode ser alterado depois de uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID da oferta **exemplo---módulo do iot edge**, é atribuído a ela o URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Publisher\***     | Identificador exclusivo da sua organização, no Azure Marketplace. Todas as suas ofertas devem ser associadas com o ID de publicador. Este valor não pode ser alterado depois da oferta guardadas. |
| **Nome\***          | O nome a apresentar para a sua oferta. Este nome é apresentado no Azure Marketplace e no Portal de parceiros de nuvem. Pode ter um máximo de 50 carateres. Recomendamos que utilize um nome de marca reconhecível para seu produto. Não inclua o nome da sua organização, a menos que essa é a forma como o seu produto é comercializado. Se estiver de marketing esta oferta em outros Web sites e publicações, certifique-se de que o nome é exatamente o mesmo em todas as publicações. |
|  |  |


Selecione **guardar** para guardar as definições da oferta.


## <a name="next-steps"></a>Passos Seguintes

Utilize o [SKUs](./cpp-skus-tab.md) separador para configurar os SKUs para a sua oferta.
