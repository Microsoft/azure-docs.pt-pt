---
title: Visão geral de segurança para Azure Data Share
description: Visão geral de segurança para Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: e299f63777847c557fc13afb4d36d01c12b3b52a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84637124"
---
# <a name="security-overview-for-azure-data-share"></a>Visão geral de segurança para Azure Data Share

Este artigo fornece uma visão geral de segurança do serviço Azure Data Share.

## <a name="security-overview"></a>Descrição geral da segurança

A Azure Data Share aproveita a segurança subjacente que a Azure oferece para proteger os dados em repouso e em trânsito. Os dados são encriptados em repouso, onde suportados pela loja de dados subjacente. Os dados também são encriptados em trânsito. Os metadados sobre uma partilha de dados também são encriptados em repouso e em trânsito. 

Os controlos de acesso podem ser definidos no nível de recursos Azure Data Share para garantir que é acedido por aqueles que são autorizados. 

A Azure Data Share aproveita a identidade gerida (anteriormente conhecida como MSI) para aceder a lojas de dados que estão a ser usadas para a partilha de dados. Não existe troca de credenciais entre um fornecedor de dados e um consumidor de dados. Para obter mais informações sobre a identidade gerida, consulte [identidades geridas para recursos azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) Para obter mais informações sobre funções e permissões necessárias para partilhar dados, consulte [as Funções e os requisitos](concepts-roles-permissions.md).

## <a name="next-steps"></a>Próximos passos

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)




