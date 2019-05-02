---
title: Definições para uma imagem de contentores do Azure da oferta | O Azure Marketplace
description: Configure definições de oferta para um contentor do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: b68ace0eb6f048f94f2945b7246cce7f83a8c42c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942545"
---
# <a name="container-offer-settings-tab"></a>Separador de definições da oferta de contentor

O **contentores > nova oferta** página abre-se com o foco no **oferecer definições** separador. 

![Identidade de oferta](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Definições de identidade da oferta

Sob **oferecer identidade**, tem de fornecer informações para os campos descrito na tabela seguinte. Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de oferta\***       | Um identificador exclusivo (dentro de um perfil do publicador) para a oferta. Este identificador serão visível nos relatórios de URLs e as informações do produto. Ele tem um comprimento máximo de 50 carateres e pode utilizar carateres minúsculos de alfanuméricos e traços (-). (O identificador não pode terminar com um hífen.) **Nota:** Este campo não pode ser alterado depois de uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID da oferta **exemplo-container**, é atribuído a ela o URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID de publicador\***     | Identificador exclusivo da sua organização, no Azure Marketplace. Todas as suas ofertas devem ser associadas com o ID de publicador. Este valor não pode ser alterado depois da oferta guardadas. |
| **Nome\***          | O nome a apresentar para a sua oferta. Este nome é apresentado no Azure Marketplace e no Portal de parceiros de nuvem. Pode ter um máximo de 50 carateres. Recomendamos que utilize um nome de marca reconhecível para seu produto. Não inclua o nome da sua organização, a menos que essa é a forma como o seu produto é comercializado. Se estiver de marketing esta oferta em outros Web sites e publicações, certifique-se de que o nome é exatamente o mesmo em todas as publicações. |
|  |  |

Selecione **guardar** para guardar as definições da oferta.


## <a name="next-steps"></a>Passos Seguintes

Utilize o [SKUs](./cpp-skus-tab.md) separador para configurar os SKUs para a sua oferta.
