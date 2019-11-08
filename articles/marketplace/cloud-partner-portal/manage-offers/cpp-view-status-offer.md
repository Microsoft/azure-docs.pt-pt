---
title: Exibir o status das ofertas do Marketplace | Azure Marketplace
description: Exibir o status das ofertas no Azure e AppSource Marketplaces usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: e58670d37e01bb6e453b73e42a87e42e890d10d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826709"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Exibir o status de publicação das ofertas do Azure Marketplace e do AppSource

Depois de criar uma oferta e, especialmente durante o processo de publicação, você pode exibir o status de sua oferta no Portal do Cloud Partner.  O status de publicação geral está disponível nas páginas [**todas as ofertas**](../portal-tour/cpp-all-offers-page.md) e [**aprovações**](../portal-tour/cpp-approvals-page.md) do Portal.  Um dos seguintes indicadores de status deve ser exibido para cada oferta.  

|            Estado              |   Descrição                                                           |
|            ------              |   -----------                                                           |
| **-**                          | A oferta foi criada, mas o processo de publicação não foi iniciado.            |
| **Publicação em andamento**        | A oferta está funcionando de forma correspondente pelas etapas do processo de publicação.   |
| **Falha na publicação**             | Foi descoberto um problema crítico durante a validação ou análise da Microsoft. |
| **Publicação cancelada**           | O Publicador cancelou o processo de publicação da oferta.  Esse estado não lista uma oferta existente no Marketplace. | 
| **Aguardando a saída do Publicador** | A oferta foi revisada pela Microsoft e agora aguarda uma verificação final pelo Publicador. |
| **Deslistado**                   | Uma oferta publicada anteriormente no Marketplace foi removida.      | 
|  |  |


## <a name="publishing-status-details"></a>Detalhes do status da publicação 

Mais detalhes sobre o status em uma oferta à medida que eles passam pelo processo de publicação são encontrados na guia **status** da página **nova oferta** .  Esta página lista todas as etapas de publicação para esse tipo de oferta.  *Observe que o número e as etapas específicas muitas vezes são diferentes entre os tipos de oferta.*  Essa página também indica quaisquer problemas pendentes gerados pelas etapas de validação e revisão da Microsoft, que geralmente exigem ação do Publicador antes que o processo de publicação possa continuar.  Por exemplo, a imagem a seguir mostra a guia **status** para uma nova oferta de máquina virtual. 

![Guia status da oferta de VM](./media/vm-offer-pub-steps1.png)

A próxima guia **status** de exemplo para um serviço de consultoria, mostrando um erro relatado nas configurações de gerenciamento de Lead.  Como o gerenciamento de Lead é necessário para os serviços de consultoria, esse erro deve ser corrigido para que a publicação possa continuar.

![Guia status para serviço de consultoria mostrando erro](./media/consulting-service-error.png)

O status de exemplo final de um aplicativo do Azure mostra um problema crítico de revisão da Microsoft.  Ele contém um link ativo para o item DevOps do Azure que contém informações detalhadas sobre esse problema de revisão.  Para obter mais informações, consulte [publicar a oferta de aplicativo do Azure](cpp-publish-offer.md).

![Guia status do aplicativo do Azure mostrando o problema de revisão](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Passos seguintes

Para corrigir problemas pendentes ou configurações da oferta de atualização, você deve [atualizar uma oferta](./cpp-update-offer.md). 
