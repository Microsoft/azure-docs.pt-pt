---
title: Configure a replicação do objeto (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a configurar a replicação de objetos para copiar assíncronamente bolhas de blocos de um recipiente numa conta de armazenamento para outra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ddf9a1309cb4f9156cec3aeb5d2ddd9d22cde485
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011453"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Configure a replicação do objeto para as bolhas de bloco (pré-visualização)

A replicação de objetos (pré-visualização) assíncroneamente copia blobs de bloco entre uma conta de armazenamento de origem e uma conta de destino. Para obter mais informações sobre a replicação do objeto, consulte [a replicação do objeto (pré-visualização)](object-replication-overview.md).

Ao configurar a replicação de objetos, cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contentor de origem e um contentor de destino e indicam quais as bolhas de bloqueio no recipiente de origem que serão replicadas.

Este artigo descreve como configurar a replicação de objetos para a sua conta de armazenamento utilizando o portal Azure, PowerShell ou Azure CLI. Também pode utilizar uma das bibliotecas de clientes do fornecedor de recursos Azure Storage para configurar a replicação de objetos.

## <a name="create-a-replication-policy-and-rules"></a>Criar uma política de replicação e regras

Antes de configurar a replicação de objetos, crie as contas de armazenamento de origem e destino se elas ainda não existirem. Ambas as contas devem ser contas de armazenamento v2 para fins gerais. Para obter mais informações, consulte [Criar uma conta de Armazenamento Azure](../common/storage-account-create.md).

Uma conta de armazenamento pode servir como a conta de origem de até duas contas de destino. E uma conta de destino pode não ter mais do que duas contas de origem. As contas de origem e de destino podem estar todas em regiões diferentes. Pode configurar políticas de replicação separadas para replicar dados em cada uma das contas de destino.

Antes de começar, certifique-se de que se registou para as seguintes pré-visualizações do recurso:

- [Replicação de objetos (pré-visualização)](object-replication-overview.md)
- [Veragem blob (pré-visualização)](versioning-overview.md)
- [Alterar suporte de feed no armazenamento de blob Azure (pré-visualização)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Antes de configurar a replicação de objetos no portal Azure, crie os recipientes de origem e destino nas respetivas contas de armazenamento, caso já não existam. Além disso, ativou a versão blob e alterou o feed na conta de origem e permitiu a versão blob na conta de destino.

Para criar uma política de replicação no portal Azure, siga estes passos:

1. Navegue para a conta de armazenamento de fontes no portal Azure.
1. No **serviço Blob**, selecione **a replicação do objeto.**
1. **Selecione Configurar a replicação**.
1. Selecione a conta de subscrição e armazenamento de destino.
1. Na secção **de pares de contentores,** selecione um recipiente de origem a partir da conta de origem e um contentor de destino a partir da conta de destino. Pode criar até 10 pares de contentores por política de replicação.

    A imagem a seguir mostra um conjunto de regras de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Screenshot mostrando regras de replicação no portal Azure":::

1. Se desejar, especifique um ou mais filtros para copiar apenas bolhas que correspondam a um padrão de prefixo. Por exemplo, se especificar um prefixo, `b` apenas bolhas cujo nome começa com essa letra são replicadas. Pode especificar um diretório virtual como parte do prefixo. A cadeia de prefixo não suporta caracteres wildcard.

    A imagem a seguir mostra filtros que restringem quais as bolhas que são copiadas como parte de uma regra de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Screenshot mostrando filtros para uma regra de replicação":::

1. Por predefinição, o âmbito de cópia está definido para copiar apenas novos objetos. Para copiar todos os objetos do recipiente ou copiar objetos a partir de uma data e hora personalizadas, selecione o link **de alteração** e configuure o âmbito de cópia do par do recipiente.

    A imagem a seguir mostra um âmbito de cópia personalizado.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Screenshot mostrando o alcance de cópia personalizado para a replicação do objeto":::

1. **Selecione Guardar e aplicar** para criar a política de replicação e começar a replicar dados.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar uma política de replicação com o PowerShell, instale pela primeira vez a versão [2.0.1 ou](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) posterior do módulo Az.Storage PowerShell. Siga estes passos para instalar o módulo de pré-visualização:

1. Desinstalar quaisquer instalações anteriores do Azure PowerShell a partir do Windows utilizando as **funcionalidades & aplicações** em **Definições**.

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

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

Para obter mais informações sobre a instalação do Azure PowerShell, consulte [instalar a Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

O exemplo a seguir mostra como criar uma política de replicação nas contas de origem e destino. Lembre-se de substituir valores em suportes angulares por seus próprios valores:

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

Para criar uma política de replicação com o Azure CLI, instale primeiro a extensão de pré-visualização para Azure Storage.:

```azurecli
az extension add -n storage-or-preview
```

Em seguida, inscreva-se com as suas credenciais Azure:

```azurecli
az login
```

Ativar a versão blob nas contas de armazenamento de origem e destino e ativar o feed de alteração na conta de origem. Lembre-se de substituir valores em suportes angulares por seus próprios valores:

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

Crie a política na conta de origem utilizando o ID da política.

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

1. Navegue para a conta de armazenamento de fontes no portal Azure.
1. Em **Definições**, selecione **replicação de objetos**.
1. Clique no botão **Mais** ao lado do nome da apólice.
1. Selecione **Eliminar Regras**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para remover uma política de replicação, elimine a política tanto da conta de origem como da conta de destino. A supressão da política também elimina quaisquer regras que lhe sejam associadas.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma política de replicação, elimine a política tanto da conta de origem como da conta de destino. A supressão da política também elimina quaisquer regras que lhe sejam associadas.

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

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da replicação do objeto (pré-visualização)](object-replication-overview.md)
