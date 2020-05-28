---
title: Configurar a replicação do objeto (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a configurar a replicação de objetos para copiar as bolhas de bloco sincronicamente de um recipiente numa conta de armazenamento para outra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/20/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6c951d7875086658763243c7c1973f08233f96e0
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749222"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Configurar a replicação de objetos para bolhas de bloco (pré-visualização)

A replicação do objeto (pré-visualização) copia assincronicamente bolhas de blocos entre uma conta de armazenamento de origem e uma conta de destino. Para obter mais informações sobre a replicação do objeto, consulte [a replicação do objeto (pré-visualização)](object-replication-overview.md).

Ao configurar a replicação de objetos, cria-se uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um recipiente de origem e um recipiente de destino e indicam quais as bolhas de bloco no recipiente de origem que serão replicadas.

Este artigo descreve como configurar a replicação de objetos para a sua conta de armazenamento utilizando o portal Azure, PowerShell ou Azure CLI. Também pode utilizar uma das bibliotecas de clientes do fornecedor de recursos do Azure Storage para configurar a replicação de objetos.

## <a name="create-a-replication-policy-and-rules"></a>Criar uma política e regras de replicação

Antes de configurar a replicação de objetos, crie as contas de armazenamento de origem e destino se ainda não existirem. Ambas as contas devem ser contas de armazenamento v2 de uso geral. Para mais informações, consulte [Criar uma conta de Armazenamento Azure](../common/storage-account-create.md).

Além disso, certifique-se de que se registou para as seguintes pré-visualizações de funcionalidades:

- [Replicação de objetos (pré-visualização)](object-replication-overview.md)
- [Versão blob (pré-visualização)](versioning-overview.md)
- [Alterar suporte para alimentação em Armazenamento de Blob Azure (Pré-visualização)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Antes de configurar a replicação de objetos no portal Azure, crie os recipientes de origem e destino nas respetivas contas de armazenamento, caso não existam já. Além disso, habilitado a versão blob e alterar o feed na conta fonte, e permitir a versão blob na conta de destino.

Para criar uma política de replicação no portal Azure, siga estes passos:

1. Navegue para a conta de armazenamento de origem no portal Azure.
1. Em **Definições,** **selecione a replicação do objeto**.
1. **Selecione A replicação de configurar**.
1. Selecione a conta de subscrição e armazenamento de destino.
1. Na secção de pares de **contentores,** selecione um recipiente de origem da conta fonte e um recipiente de destino a partir da conta de destino. Pode criar até 10 pares de contentores por política de replicação.

    A imagem que se segue mostra um conjunto de regras de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Screenshot mostrando regras de replicação no portal Azure":::

1. Se desejar, especifique um ou mais filtros para copiar apenas bolhas que correspondam a um padrão de prefixo. Por exemplo, se especificar um `b` prefixo, apenas as bolhas cujo nome começa com essa letra são replicadas. Pode especificar um diretório virtual como parte do prefixo.

    A imagem que se segue mostra filtros que restringem as bolhas que são copiadas como parte de uma regra de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Screenshot mostrando filtros para uma regra de replicação":::

1. Por predefinição, o âmbito da cópia é definido para copiar apenas novos objetos. Para copiar todos os objetos do recipiente ou copiar objetos a partir de uma data e hora personalizadas, selecione o link de **alteração** e configure o âmbito de cópia para o par de recipientes.

    A imagem seguinte mostra um âmbito de cópia personalizado.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Screenshot mostrando o âmbito de cópia personalizado para a replicação de objetos":::

1. Selecione **Guardar e aplicar** para criar a política de replicação e começar a replicar dados.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar uma política de replicação com o PowerShell, instale primeiro a versão [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) do módulo Az.Storage PowerShell. Siga estes passos para instalar o módulo de pré-visualização:

1. Desinstale quaisquer instalações anteriores do Azure PowerShell a partir do Windows utilizando as **aplicações & definição** de funcionalidades em **Definições**.

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Feche e reabra a janela PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale o módulo de pré-visualização Az.Storage:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Para mais informações sobre a instalação do Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

O exemplo que se segue mostra como criar uma política de replicação nas contas de origem e destino. Lembre-se de substituir valores em suportes angulares por valores próprios:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma política de replicação com o Azure CLI, instale primeiro a extensão de pré-visualização para o Armazenamento Azure.:

```azurecli
az extension add -n storage-or-preview
```

Em seguida, inscreva-se com as suas credenciais Azure:

```azurecli
az login
```

Ative a versão blob nas contas de armazenamento de origem e destino e permite alterar o feed na conta fonte. Lembre-se de substituir valores em suportes angulares por valores próprios:

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Crie os recipientes de origem e destino nas respetivas contas de armazenamento.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Crie uma nova política de replicação e regras associadas na conta de destino.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Crie a política na conta fonte utilizando o ID da apólice.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Remover uma política de replicação

Para remover uma política de replicação e as suas regras associadas, utilize o portal Azure, PowerShell ou CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para remover uma política de replicação no portal Azure, siga estes passos:

1. Navegue para a conta de armazenamento de origem no portal Azure.
1. Em **Definições,** **selecione a replicação do objeto**.
1. Clique no botão **Mais** ao lado do nome da apólice.
1. Selecione **Excluir Regras**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para remover uma política de replicação, elimine a política tanto da conta fonte como da conta de destino. A eliminação da política também elimina quaisquer regras que lhe sejam associadas.

```powershell
Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName |
    Remove-AzStorageObjectReplicationPolicy
Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName |
    Remove-AzStorageObjectReplicationPolicy
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma política de replicação, elimine a política tanto da conta fonte como da conta de destino. A eliminação da política também elimina quaisquer regras que lhe sejam associadas.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Próximos passos

- [Visão geral da replicação de objetos (pré-visualização)](object-replication-overview.md)