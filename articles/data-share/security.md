---
title: Descrição geral de segurança do Azure Data Share
description: Descrição geral de segurança do Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578098"
---
# <a name="security-overview-for-azure-data-share"></a>Descrição geral de segurança do Azure Data Share

Este artigo fornece uma visão geral de segurança do serviço Azure Data Share.

## <a name="security-overview"></a>Descrição geral da segurança

A Azure Data Share aproveita a segurança subjacente que a Azure oferece para proteger os dados em repouso e em trânsito. Os dados são encriptados em repouso, onde suportados pela loja de dados subjacente. Os dados também são encriptados em trânsito utilizando o TLS 1.2. Os metadados sobre uma partilha de dados também são encriptados em repouso e em trânsito. A Azure Data Share não armazena conteúdos dos dados do cliente partilhados.

A Azure Data Share aproveita a identidade gerida (anteriormente conhecida como MSI) para aceder a lojas de dados que estão a ser usadas para a partilha de dados. Não existe troca de credenciais entre um fornecedor de dados e um consumidor de dados. Para obter mais informações sobre a identidade gerida, consulte [identidades geridas para recursos azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) Para obter mais informações sobre funções e permissões necessárias para partilhar dados, consulte [as Funções e os requisitos](concepts-roles-permissions.md).

Os controlos de acesso ao Azure Data Share podem ser definidos no nível de recursos de Partilha de Dados para garantir que é acedido por aqueles que são autorizados. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Partilhar dados de ou para lojas de dados com firewall ativada
Para partilhar dados de ou para contas de armazenamento com firewall ligada, é necessário ativar **permitir serviços da Microsoft fidedignos** na sua conta de armazenamento. Consulte as firewalls de [armazenamento Configure Azure e redes virtuais](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) para obter mais detalhes.


## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)
