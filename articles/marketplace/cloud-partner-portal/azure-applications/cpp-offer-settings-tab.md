---
title: Definições da oferta de aplicação do Azure | O Azure Marketplace
description: Configure as definições de oferta para uma oferta de aplicação do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 789b783629b3cc3528eba1883b21051604cf6e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942939"
---
# <a name="azure-application-offer-settings-tab"></a>Separador de definições da oferta de aplicação do Azure

Este artigo descreve como configurar as definições de oferta para uma aplicação do Azure.

O **aplicações do Azure > nova oferta** página abre-se com o foco no **oferecer definições** separador. Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório.

![Formulário de oferta de identidade](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Definições de identidade da oferta

Sob **oferecer identidade**, tem de fornecer informações para os campos descrito na tabela seguinte.  

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
| **ID de oferta\***       | Um identificador exclusivo (dentro de um perfil do publicador) para a oferta. Este identificador serão visível nos relatórios de URLs e as informações do produto. Ele tem um comprimento máximo de 50 carateres e pode utilizar carateres minúsculos de alfanuméricos e traços (-). (O identificador não pode terminar com um hífen.) **Nota:** Este campo não pode ser alterado depois de uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID da oferta **exemplo-container**, é atribuído a ela o URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID de publicador\***     | Identificador exclusivo da sua organização, no Azure Marketplace. Todas as suas ofertas devem ser associadas com o ID de publicador. Este valor não pode ser alterado depois da oferta guardadas. |
| **Nome\***          | O nome a apresentar para a sua oferta. Este nome é apresentado no Azure Marketplace e no Portal de parceiros de nuvem. Pode ter um máximo de 50 carateres. Recomendamos que utilize um nome de marca reconhecível para seu produto. Não inclua o nome da sua organização, a menos que essa é a forma como o seu produto é comercializado. Se estiver de marketing esta oferta em outros Web sites e publicações, certifique-se de que o nome é exatamente o mesmo em todas as publicações. |
|  |  |

Selecione **guardar** para guardar as definições da oferta.

## <a name="next-steps"></a>Passos Seguintes

Utilize o [SKUs](./cpp-skus-tab.md) separador para configurar os SKUs para a sua oferta.