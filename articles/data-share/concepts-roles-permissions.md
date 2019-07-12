---
title: Funções e os requisitos para a pré-visualização de partilha de dados do Azure
description: Funções e os requisitos para a pré-visualização de partilha de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807529"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Funções e os requisitos para a pré-visualização de partilha de dados do Azure

Este artigo descreve as funções necessárias para partilhar dados com a pré-visualização de partilha de dados do Azure, bem como para aceitar e receber dados a utilizar a pré-visualização de partilhar dados do Azure. 

## <a name="roles-and-requirements"></a>Funções e requisitos

Para partilhar ou receber dados através da partilha de dados do Azure, tem de ser capaz de conceder permissões de partilha de dados de mensagens em fila para a conta de armazenamento que são dados a partir de partilha ou receber dados em para a conta de utilizador que utiliza para iniciar sessão no Azure. Normalmente, esta é uma permissão que exista na **proprietário** de função ou uma função personalizada com a permissão de escrita/atribuições Microsoft.Authorization/role atribuído. 

Para partilhar ou receber dados de ou para uma conta de armazenamento do Azure, tem de ser um proprietário da conta de armazenamento. Mesmo se o ter criado a conta de armazenamento, isso não lhe concede automaticamente propriedade da conta de armazenamento. Para adicionar a próprio à função de proprietário da conta de armazenamento do Azure, siga estes passos.

1. Navegue para a conta de armazenamento no portal do Azure
1. Selecione **controlo de acesso (IAM)**
1. Clique em **adicionar**
1. Adicionar-se em como proprietário

Para ver as permissões que tem na subscrição, no portal do Azure, selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. 

## <a name="resource-provider-registration"></a>Registo do fornecedor de recursos 

Ao aceitar um convite de partilha de dados do Azure, terá de registar manualmente o fornecedor de recursos de Microsoft.DataShare na sua subscrição. Siga estes passos para registar o fornecedor de recursos de Microsoft.DataShare na sua subscrição do Azure. 

1. No portal do Azure, navegue para **subscrições**
1. Selecione a subscrição que está a utilizar para a partilha de dados do Azure
1. Clique em **fornecedores de recursos**
1. Procure Microsoft.DataShare
1. Clique em **registar**

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as funções no Azure - [compreender as definições de função](../role-based-access-control/role-definitions.md)

