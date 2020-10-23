---
title: Descrição geral de segurança do Azure Data Share
description: Descrição geral de segurança do Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216560"
---
# <a name="security-overview-for-azure-data-share"></a>Descrição geral de segurança do Azure Data Share

Este artigo fornece uma visão geral de segurança do serviço Azure Data Share.

## <a name="security-overview"></a>Descrição geral da segurança

A Azure Data Share aproveita a segurança subjacente que a Azure oferece para proteger os dados em repouso e em trânsito. Os dados são encriptados em repouso, onde suportados pela loja de dados subjacente. Os dados também são encriptados em trânsito. Os metadados sobre uma partilha de dados também são encriptados em repouso e em trânsito. 

Os controlos de acesso podem ser definidos no nível de recursos Azure Data Share para garantir que é acedido por aqueles que são autorizados. 

A Azure Data Share aproveita a identidade gerida (anteriormente conhecida como MSI) para aceder a lojas de dados que estão a ser usadas para a partilha de dados. Não existe troca de credenciais entre um fornecedor de dados e um consumidor de dados. Para obter mais informações sobre a identidade gerida, consulte [identidades geridas para recursos azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) Para obter mais informações sobre funções e permissões necessárias para partilhar dados, consulte [as Funções e os requisitos](concepts-roles-permissions.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)