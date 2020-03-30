---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297919"
---
O Azure Data Explorer encripta todos os dados numa conta de armazenamento em repouso. Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados. 

As chaves geridas pelo cliente devem ser armazenadas num [Cofre de Chaves Azure](/azure/key-vault/key-vault-overview). Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar um Cofre de Chave Azure para gerar chaves. O cluster Azure Data Explorer e o cofre chave devem estar na mesma região, mas podem estar em diferentes subscrições. Para obter uma explicação detalhada sobre as chaves geridas pelo cliente, consulte [as chaves geridas pelo cliente com](/azure/storage/common/storage-service-encryption)o Cofre de Chaves Azure . 

Este artigo mostra-lhe como configurar as chaves geridas pelo cliente.

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

Para configurar as chaves geridas pelo cliente com o Azure Data Explorer, tem de [definir duas propriedades no cofre da chave:](/azure/key-vault/key-vault-ovw-soft-delete)Soft **Delete** e **Não Purgar**. Estas propriedades não são ativadas por defeito. Para ativar estas propriedades, execute **a eliminação suave** e **a proteção da purga** ativa em [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) ou [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) num cofre de chaves novo ou existente. Apenas são suportadas chaves RSA do tamanho 2048. Para mais informações sobre as chaves, consulte [as chaves do Cofre chave](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> A encriptação de dados utilizando chaves geridas pelo cliente não é suportada em [clusters líderes e seguidores](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Atribuir uma identidade ao cluster

Para ativar as chaves geridas pelo cliente para o seu cluster, primeiro atribua uma identidade gerida atribuída pelo sistema ao cluster. Vaiusar esta identidade gerida para conceder permissões ao cluster para aceder ao cofre chave. Para configurar as identidades geridas atribuídas pelo sistema, consulte [identidades geridas](/azure/data-explorer/managed-identities).