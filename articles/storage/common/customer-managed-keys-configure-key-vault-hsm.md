---
title: Configure a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault Managed HSM (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a configurar a encriptação de armazenamento Azure com teclas geridas pelo cliente armazenadas no Azure Key Vault Managed HSM (pré-visualização) utilizando o Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218643"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Configure a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault Managed HSM (pré-visualização)

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode gerir as suas próprias chaves. As chaves geridas pelo cliente devem ser armazenadas no Cofre da Chave Azure ou no Modelo de Segurança Gerida do Cofre de Chaves (HSM) (pré-visualização). Um Cofre de Chave Azure Gerido HSM é um HSM validado fips 140-2 Nível 3.

Este artigo mostra como configurar a encriptação com chaves geridas pelo cliente armazenadas num HSM gerido através da utilização do Azure CLI. Para aprender a configurar a encriptação com chaves geridas pelo cliente armazenadas num cofre de chaves, consulte [a encriptação Configure com as teclas geridas pelo cliente armazenadas no Cofre da Chave Azure](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> A encriptação com teclas geridas pelo cliente armazenadas no Azure Key Vault Managed HSM está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.
>
> Azure Key Vault e Azure Key Vault Gerido HSM suportam as mesmas APIs e interfaces de gestão para configuração.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Primeiro, atribua uma identidade gerida atribuída ao sistema para a conta de armazenamento. Você usará esta identidade gerida para conceder as permissões da conta de armazenamento para aceder ao HSM gerido. Para obter mais informações sobre identidades geridas atribuídas pelo sistema, veja [quais são as identidades geridas para os recursos Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

Para atribuir uma identidade gerida utilizando o Azure CLI, ligue para [a atualização da conta de armazenamento AZ](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Atribuir uma função à conta de armazenamento para acesso ao HSM gerido

Em seguida, atribua a **função de encriptação do serviço crypto gerido do HSM** para a identidade gerida da conta de armazenamento para que a conta de armazenamento tenha permissões para o HSM gerido. A Microsoft recomenda que aloteia a atribuição de funções ao nível da chave individual, de modo a conceder o menor número possível de privilégios à identidade gerida.

Para criar a atribuição de funções para a conta de armazenamento, chame [a az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Configurar encriptação com uma chave no HSM gerido

Por fim, configurar a encriptação do Armazenamento Azure com as chaves geridas pelo cliente para utilizar uma chave armazenada no HSM gerido. Os tipos de chaves suportados incluem as chaves RSA-HSM dos tamanhos 2048, 3072 e 4096. Instale o Azure CLI 2.12.0 ou posteriormente para configurar a encriptação para utilizar uma chave gerida pelo cliente num HSM gerido por um HSM gerido. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Para atualizar automaticamente a versão chave para uma chave gerida pelo cliente, omita a versão chave quando configurar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Ligue para a atualização da [conta de armazenamento AZ](/cli/azure/storage/account#az_storage_account_update) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte. `--encryption-key-source parameter`Inclua-o e desa cotado `Microsoft.Keyvault` para ativar as chaves geridas pelo cliente para a conta. Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Para atualizar manualmente a versão para uma chave gerida pelo cliente, inclua a versão chave quando configurar encriptação para a conta de armazenamento:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Quando atualizar manualmente a versão chave, terá de atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão. Em primeiro lugar, consulta para o cofre-chave URI chamando [az keyvault show](/cli/azure/keyvault#az-keyvault-show), e para a versão chave, chamando [az key-key-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em [seguida,](/cli/azure/storage/account#az-storage-account-update) ligue para a atualização da conta de armazenamento AZ para atualizar as definições de encriptação da conta de armazenamento para usar a nova versão da chave, como mostrado no exemplo anterior.

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](customer-managed-keys-overview.md)
