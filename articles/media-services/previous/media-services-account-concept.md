---
title: Gerir contas V2 da Azure Media Services Microsoft Docs
description: Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, é necessário criar uma conta de Media Services. Este artigo explica como gerir as contas V2 da Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 167028cda6962445373b22c5f3d0a9e56c3e608a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255415"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Gerir contas V2 da Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, é necessário criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Movimentar uma conta de Serviços de Mídia entre subscrições

Se precisar de mover uma conta de Serviços de Comunicação para uma nova subscrição, tem primeiro de mover todo o grupo de recursos que contém a conta de Serviços de Comunicação para a nova subscrição. Deve mover todos os recursos anexados: contas de Armazenamento Azure, perfis Azure CDN, etc. Para obter mais informações, consulte [mover recursos para novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) Como em qualquer recurso em Azure, movimentos de grupo de recursos podem levar algum tempo para completar.

Os Serviços de Comunicação Social v2 não suportam o modelo de vários arrendamentos. Se precisar de mover uma conta de Serviços de Comunicação para uma subscrição de um novo inquilino, crie um novo pedido de Diretório Ativo Azure (Azure AD) no novo inquilino. Em seguida, mude a sua conta para a subscrição do novo inquilino. Após a conclusão da mudança do arrendatário, pode começar a utilizar uma aplicação AD Azure do novo inquilino para aceder à conta dos Serviços de Comunicação Social utilizando as APIs v2.

> [!IMPORTANT]
> É necessário redefinir a informação de [autenticação AD Azure](media-services-portal-get-started-with-aad.md) para aceder aos Serviços de Media v2 API.
  
### <a name="considerations"></a>Considerações

* Crie cópias de segurança de todos os dados da sua conta antes de migrar para uma subscrição diferente.
* Tens de parar todos os pontos de streaming e recursos de streaming ao vivo. Os seus utilizadores não poderão aceder ao seu conteúdo durante a duração do movimento do grupo de recursos.

> [!IMPORTANT]
> Não inicie o Streaming Endpoint até que o movimento esteja concluído com sucesso.

### <a name="troubleshoot"></a>Resolução de problemas

Se uma conta de Serviços de Mídia ou uma conta de Armazenamento Azure associada ficar "desligada" após o movimento do grupo de recursos, tente rodar as teclas de Conta de Armazenamento. Se a rotação das teclas 'Conta de Armazenamento' não resolver o estado "desligado" da conta Serviços de Comunicação, apresente um novo pedido de apoio do menu "Suporte + resolução de problemas" na conta dos Serviços de Comunicação.  

## <a name="next-steps"></a>Passos seguintes

[Criar uma conta](media-services-portal-create-account.md)
