---
title: Ver o estado das ofertas do marketplace | O Azure Marketplace
description: Ver o estado de ofertas no Azure e mercados de AppSource com o Portal de parceiro de Cloud
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b8113552edf9dd2f886b5752b2ebc69afc0fda08
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942425"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Ver o estado de publicação de ofertas do Azure Marketplace e AppSource

Depois de criar uma oferta e, especialmente durante o processo de publicação, pode ver o estado da sua oferta no Portal de parceiros de nuvem.  Em geral está disponível no estado de publicação a [ **oferece todos os** ](../portal-tour/cpp-all-offers-page.md) e [ **aprovações** ](../portal-tour/cpp-approvals-page.md) páginas do portal.  Um dos indicadores de estado seguintes deve ser exibido para cada oferta.  

|            Estado              |   Descrição                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Oferta foi criada mas o processo de publicação não foi iniciada.            |
| **Publicar em curso**        | Oferta está a funcionar ao percorrer os passos do processo de publicação.   |
| **Falha ao publicar**             | Uma questão crítica foi descoberta durante a validação ou revisão pela Microsoft. |
| **Publicar foi cancelada**           | O publicador cancelou a processo de publicação de oferta.  Este estado não delist uma oferta existente no marketplace. | 
| **Fim de sessão de publicador espera** | A oferta foi avaliada pela Microsoft e agora awaits uma verificação final pelo editor. |
| **Delisted**                   | Uma oferta anteriormente publicada no marketplace foi removida.      | 
|  |  |


## <a name="publishing-status-details"></a>Detalhes do Estado de publicação 

Mais detalhes sobre o estado numa oferta durante o processo de publicação se encontra no **Status** separador da **nova oferta** página.  Esta página apresenta uma lista de todos os passos de publicação para esse tipo de oferta.  *Tenha em atenção que o número e os passos específicos, muitas vezes, diferem entre os tipos de oferta.*  Esta página também indica quaisquer problemas pendentes gerados pela validação da Microsoft e passos de revisão, que requerem muitas vezes, uma ação pelo publicador antes de pode continuar o processo de publicação.  Por exemplo, a imagem seguinte mostra os **estado** separador para uma nova oferta de máquina virtual. 

![Separador de estado para a oferta VM](./media/vm-offer-pub-steps1.png)

O exemplo seguinte **estado** separador para um serviço de consultoria, que mostra um erro comunicado nas definições de gestão de oportunidades potenciais.  Como gestão de oportunidades potenciais é necessário para serviços de consultoria, este erro deve ser corrigido antes de continuar com a publicação.

![Separador de estado de erro de apresentação do serviço de consultoria](./media/consulting-service-error.png)

O estado de exemplo final da aplicação do Azure mostra uma questão de revisão crítica do Microsoft.  Ela contém uma ligação de acesso frequente para o item do VSTS que contém informações detalhadas sobre este problema de revisão.  Para obter mais informações, consulte [oferta de aplicação do Azure de publicar](cpp-publish-offer.md).

![Separador de estado para a aplicação do Azure que mostra o problema de revisão](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Passos Seguintes

Para corrigir problemas pendentes ou atualizar as definições da oferta, deve [uma oferta de atualização](./cpp-update-offer.md). 
