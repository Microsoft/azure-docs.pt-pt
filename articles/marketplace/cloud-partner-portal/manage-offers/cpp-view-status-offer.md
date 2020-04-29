---
title: Veja o estado das ofertas de marketplace [ Mercado Azure
description: Veja o estado das ofertas nos Mercados Azure e AppSource utilizando o Portal do Parceiro cloud
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275973"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Ver o estado editorial das ofertas do Azure Marketplace e appSource

Depois de criar uma oferta, e especialmente durante o processo de publicação, pode ver o estado da sua oferta no Portal do Parceiro cloud.  O estado global de publicação está disponível nas páginas [**All Offers**](../portal-tour/cpp-all-offers-page.md) and [**Approvals**](../portal-tour/cpp-approvals-page.md) do portal.  Um dos seguintes indicadores de estado deve ser apresentado para cada oferta.  

|            Estado              |   Descrição                                                           |
|            ------              |   -----------                                                           |
| **-**                          | A oferta foi criada, mas o processo de publicação ainda não começou.            |
| **Publicar em curso**        | A oferta está a percorrer os passos do processo de publicação.   |
| **Publicação falhada**             | Um problema crítico foi descoberto durante a validação ou revisão pela Microsoft. |
| **Publicar cancelado**           | A editora cancelou o processo de publicação da oferta.  Este Estado não deslista uma oferta existente no mercado. | 
| **Aguardando a assinatura da editora** | A oferta foi revista pela Microsoft, e aguarda agora uma verificação final pela editora. |
| **Despromovido**                   | Uma oferta anteriormente publicada no mercado foi removida.      | 
|  |  |


## <a name="publishing-status-details"></a>Detalhes do estado de publicação 

Mais detalhes sobre o estado de uma oferta à medida que passa pelo processo de publicação encontra-se no separador **Status** da página **New Offer.**  Esta página lista todos os passos de publicação para esse tipo de oferta.  *Note que o número e os passos específicos muitas vezes diferem entre os tipos de oferta.*  Esta página também indica quaisquer questões pendentes levantadas pelas etapas de validação e revisão da Microsoft, que muitas vezes requerem ação por parte da editora antes que o processo de publicação possa prosseguir.  Por exemplo, a imagem que se segue mostra o separador **Status** para uma nova oferta de máquina virtual. 

![Separador de estado para oferta VM](./media/vm-offer-pub-steps1.png)

O separador **status** seguinte para um serviço de consultoria, mostrando um erro reportado nas definições de gestão de chumbo.  Uma vez que a gestão de chumbo é necessária para serviços de consultoria, este erro deve ser corrigido antes de a publicação poder continuar.

![Separador de estado para serviço de consultoria mostrando erro](./media/consulting-service-error.png)

O estatuto final de uma aplicação Azure mostra um problema crítico de revisão da Microsoft.  Contém uma ligação quente com o item Azure DevOps que contém informações detalhadas sobre este problema de revisão.  Para mais informações, consulte a oferta de [aplicação da Publish Azure.](cpp-publish-offer.md)

![Separador de estado para app Azure que mostra problema de revisão](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Passos seguintes

Para corrigir as definições de oferta de problemas pendentes ou atualizar, tem de [atualizar uma oferta.](./cpp-update-offer.md) 
