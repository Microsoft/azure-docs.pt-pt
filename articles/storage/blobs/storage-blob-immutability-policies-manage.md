---
title: Definir e gerir políticas de imutabilidade para armazenamento blob - Armazenamento Azure
description: Aprenda a utilizar o suporte WORM (Write Once, Read Many) para o armazenamento blob (objeto) para armazenar dados num estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84463642"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Definir e gerir políticas de imutabilidade para armazenamento blob

O armazenamento imutável para o armazenamento Azure Blob permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo utilizador. Durante o intervalo de retenção, as bolhas podem ser criadas e lidas, mas não podem ser modificadas ou eliminadas. O armazenamento imutável está disponível para contas de armazenamento v2 e Blob em todas as regiões de Azure.

Este artigo mostra como definir e gerir políticas de imutabilidade e detém os dados legais para os dados no armazenamento blob usando o portal Azure, PowerShell ou Azure CLI. Para obter mais informações sobre o armazenamento imutável, consulte [os dados de blob críticos de negócio da Loja com armazenamento imutável](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Definir políticas de retenção e detenções legais

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Crie um novo contentor ou selecione um contentor existente para armazenar os blobs que precisam de ser mantidos no estado imutável. O recipiente deve estar numa conta de armazenamento v2 ou Blob para fins gerais.

2. Selecione **a política de acesso** nas definições do recipiente. Em seguida, **selecione Adicionar a política** no armazenamento de **bolhas imutáveis**.

    ![Configurações de contentores no portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Para ativar a retenção baseada no tempo, selecione **a retenção baseada no tempo** a partir do menu suspenso.

    !["Retenção baseada no tempo" selecionada em "Tipo de Política"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Introduza o intervalo de retenção em dias (os valores aceitáveis são de 1 a 146000 dias).

    ![Caixa "Atualizar período de retenção para"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    O estado inicial da apólice é desbloqueado permitindo-lhe testar a funcionalidade e fazer alterações na política antes de a bloquear. O bloqueio da política é essencial para o cumprimento de regulamentos como a SEC 17a-4.

5. Bloqueie a apólice. Clique com o botão direito na elipse **(...),** e o seguinte menu aparece com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selecione **a Política de Bloqueio** e confirme a fechadura. A política está agora bloqueada e não pode ser suprimida, apenas serão permitidas prorrogações do intervalo de retenção. Não são permitidas eliminações e substituições blob. 

    ![Confirme "Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Para ativar os detenhões legais, **selecione Adicionar Política**. Selecione **Legal hold** a partir do menu suspenso.

    !["Legal hold" no menu em "Tipo de Política"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Crie um porão legal com uma ou mais etiquetas.

    ![Caixa "Tag name" sob o tipo de política](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Para limpar um porão legal, remova a etiqueta de identificação legal aplicada.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A funcionalidade está incluída nos seguintes grupos de comando: `az storage container immutability-policy`  e `az storage container legal-hold` . Corre `-h` para eles para ver os comandos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O módulo Az.Storage suporta armazenamento imutável.  Para ativar a funcionalidade, siga estes passos:

1. Certifique-se de que tem a versão mais recente do PowerShellGet `Install-Module PowerShellGet –Repository PSGallery –Force` instalada:
2. Remova qualquer instalação anterior da Azure PowerShell.
3. Instalar Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber` .

A amostra seguinte O script PowerShell é para referência. Este script cria uma nova conta de armazenamento e um recipiente. Em seguida, mostra-lhe como definir e limpar os sistemas legais, criar e bloquear uma política de retenção baseada no tempo (também conhecida como uma política de imutabilidade) e estender o intervalo de retenção.

Primeiro, crie uma conta de Armazenamento Azure:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Conjunto e clara detenção legal:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Criar ou atualizar políticas de imutabilidade baseadas no tempo:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperar políticas de imutabilidade:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloquear políticas de imutabilidade (adicionar `-Force` para descartar o pedido):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Alargar as políticas de imutabilidade:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Remover uma política de imutabilidade desbloqueada (adicione `-Force` para dispensar o pedido):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Habilitando permitir que as bolhas de apêndice protegidos escrevam

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Permitir escritas adicionais de apêndice](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A funcionalidade está incluída nos seguintes grupos de comando: `az storage container immutability-policy`  e `az storage container legal-hold` . Corre `-h` para eles para ver os comandos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Passos seguintes

[Armazenar dados de blob críticos de negócio com armazenamento imutável](storage-blob-immutable-storage.md)
