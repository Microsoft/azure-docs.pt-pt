---
title: Utilize o CLI Azure para criar uma delegação de utilizador SAS para um recipiente ou bolha
titleSuffix: Azure Storage
description: Saiba como criar uma delegação de utilizador SAS com credenciais de Diretório Azure Ative utilizando o Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 536cd01fbcf2c5d18a8c12030b709427d9bb91b1
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703611"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Criar uma delegação de utilizador SAS para um recipiente ou bolha com o Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como utilizar credenciais do Azure Ative Directory (Azure AD) para criar uma delegação de utilizador SAS para um recipiente ou bolha com o CLI Azure.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Instalar a versão mais recente da CLI do Azure

Para utilizar o Azure CLI para garantir um SAS com credenciais AD AZure, certifique-se primeiro de que instalou a versão mais recente do Azure CLI. Para obter mais informações sobre a instalação do Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Para criar uma delegação de utilizador SAS utilizando o Azure CLI, certifique-se de que instalou a versão 2.0.78 ou posterior. Para verificar a sua versão instalada, utilize o `az --version` comando.

## <a name="sign-in-with-azure-ad-credentials"></a>Inscreva-se com credenciais AD AD Azure

Inscreva-se no Azure CLI com as suas credenciais AZure AD. Para obter mais informações, veja [Iniciar sessão na CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-azure-rbac"></a>Atribuir permissões com o Azure RBAC

Para criar uma delegação de utilizadores SAS da Azure PowerShell, a conta Azure AD utilizada para assinar no Azure CLI deve ser atribuída a uma função que inclua a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Esta permissão permite que a conta Azure AD solicite a chave da *delegação* do utilizador . A chave da delegação de utilizadores é utilizada para assinar a delegação de utilizadores SAS. A função que fornece a ação **Microsoft.Storage/storageAcounts/blobServices/generateUserDelegationKey** deve ser atribuída ao nível da conta de armazenamento, do grupo de recursos ou da subscrição.

Se não tiver permissões suficientes para atribuir funções Azure a um responsável de segurança Azure, poderá ter de pedir ao proprietário ou administrador da conta para atribuir as permissões necessárias.

O exemplo a seguir atribui a função **de Contribuinte de Dados blob de armazenamento,** que inclui a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** A função é traçada ao nível da conta de armazenamento.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções incorporadas que incluem a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** consulte [as funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Use credenciais AD AD para garantir um SAS

Quando cria uma delegação de utilizadores SAS com o Azure CLI, a chave da delegação do utilizador que é usada para assinar o SAS é criada implicitamente para si. O tempo de início e o prazo de validade que especifica para o SAS também são utilizados como o tempo de início e o prazo de validade para a chave da delegação do utilizador.

Uma vez que o intervalo máximo sobre o qual a chave da delegação do utilizador é válida é de 7 dias a contar da data de início, deve especificar um prazo de validade para o SAS que se encontra no prazo de 7 dias a contar da hora de início. O SAS é inválido após o termo da chave da delegação do utilizador, pelo que um SAS com um prazo de validade superior a 7 dias será válido apenas por 7 dias.

Ao criar uma delegação de utilizador SAS, `--auth-mode login` o e `--as-user parameters` é necessário. Especifique o *login* para o `--auth-mode` parâmetro de modo a que os pedidos feitos ao Azure Storage sejam autorizados com as suas credenciais AD Azure. Especifique o `--as-user` parâmetro que indica que o SAS devolvido deve ser uma delegação de utilizador SAS.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Criar uma delegação de utilizador SAS para um recipiente

Para criar uma delegação de utilizador SAS para um recipiente com o CLI Azure, ligue para o [comando de confli do recipiente de armazenamento az.](/cli/azure/storage/container#az-storage-container-generate-sas)

Permissões suportadas para uma delegação de utilizador SAS em um recipiente incluem Adicionar, Criar, Eliminar, Listar, Ler e Escrever. As permissões podem ser especificadas singly ou combinadas. Para obter mais informações sobre estas permissões, consulte [Criar uma delegação de utilizador SAS](/rest/api/storageservices/create-user-delegation-sas).

O exemplo a seguir devolve um símbolo SAS da delegação de utilizadores para um recipiente. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

A delegação de utilizadores sas token devolvida será semelhante a:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Criar uma delegação de utilizador SAS para uma bolha

Para criar uma delegação de utilizador SAS para uma bolha com o CLI Azure, ligue para o comando [az blob de armazenamento generate-sas.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

As permissões suportadas para uma delegação de utilizador SAS numa bolha incluem Adicionar, Criar, Eliminar, Ler e Escrever. As permissões podem ser especificadas singly ou combinadas. Para obter mais informações sobre estas permissões, consulte [Criar uma delegação de utilizador SAS](/rest/api/storageservices/create-user-delegation-sas).

A sintaxe que se segue devolve uma delegação de utilizador SAS para uma bolha. O exemplo especifica o `--full-uri` parâmetro, que devolve o blob URI com o símbolo SAS anexado. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

A delegação de utilizadores SAS URI devolvida será semelhante a:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Uma delegação de utilizadores SAS não suporta a definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma delegação de utilizadores SAS

Para revogar uma delegação de utilizadores SAS do Azure CLI, ligue para a conta de armazenamento AZ revogar o comando [de chaves de delegação.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) Este comando revoga todas as chaves da delegação do utilizador associadas à conta de armazenamento especificada. Quaisquer assinaturas de acesso partilhada associadas a essas chaves são invalidadas.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Tanto a chave da delegação do utilizador como as atribuições de funções Azure são armazenadas pela Azure Storage, pelo que pode haver um atraso entre quando inicia o processo de revogação e quando uma delegação de utilizador existente SAS se torna inválida.

## <a name="next-steps"></a>Próximos passos

- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Obtenha a operação chave da delegação de utilizadores](/rest/api/storageservices/get-user-delegation-key)
