---
title: Descrição geral de segurança do Azure Data Share
description: Descrição geral de segurança do Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678421"
---
# <a name="security-overview-for-azure-data-share"></a>Descrição geral de segurança do Azure Data Share

Este artigo fornece uma visão geral de segurança do serviço Azure Data Share.

## <a name="security-overview"></a>Descrição geral da segurança

A Azure Data Share aproveita a segurança subjacente que a Azure oferece para proteger os dados em repouso e em trânsito. Os dados são encriptados em repouso, onde suportados pela loja de dados subjacente. Os dados também são encriptados em trânsito utilizando o TLS 1.2. Os metadados sobre uma partilha de dados também são encriptados em repouso e em trânsito. A Azure Data Share não armazena conteúdos dos dados do cliente partilhados.

A Azure Data Share aproveita a identidade gerida (anteriormente conhecida como MSI) para aceder a lojas de dados que estão a ser usadas para a partilha de dados. Não existe troca de credenciais entre um fornecedor de dados e um consumidor de dados. Para obter mais informações sobre a identidade gerida, consulte [identidades geridas para recursos azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) Para obter mais informações sobre funções e permissões necessárias para partilhar dados, consulte [as Funções e os requisitos](concepts-roles-permissions.md).

## <a name="access-control"></a>Controlo de acesso

Os controlos de acesso ao Azure Data Share podem ser definidos no nível de recursos de Partilha de Dados para garantir que é acedido por aqueles que são autorizados. O proprietário e colaborador de um recurso de Partilha de Dados pode partilhar dados, receber ações e fazer alterações nas ações existentes. O leitor de um recurso De partilha de dados pode visualizar ações, mas não pode fazer alterações. 

Uma vez criada ou recebida uma partilha, os utilizadores com a devida permissão para o recurso Data Share podem efeuar alterações. Quando um utilizador que cria ou recebe uma partilha sai da organização, não termina a partilha ou para o fluxo de dados. Outros utilizadores com permissão adequada para o recurso Data Share podem continuar a gerir a partilha.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Partilhar dados de ou para lojas de dados com firewall ativada
Para partilhar dados de ou para contas de armazenamento com firewall ligada, é necessário ativar **permitir serviços da Microsoft fidedignos** na sua conta de armazenamento. Consulte as firewalls de [armazenamento Configure Azure e redes virtuais](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) para obter mais detalhes.


## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)
