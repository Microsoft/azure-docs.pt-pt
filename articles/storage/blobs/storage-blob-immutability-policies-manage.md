---
title: Definir e gerir políticas de imutabilidade para armazenamento blob - Armazenamento Azure
description: Aprenda a usar o suporte WORM (Write Once, Read Many) para armazenamento blob (objeto) para armazenar dados num estado não apagamento e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970106"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Definir e gerir políticas de imutabilidade para armazenamento blob

O armazenamento imutável para armazenamento De Blob Azure permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagamentos e não modificáveis para um intervalo especificado pelo utilizador. Durante a duração do intervalo de retenção, as bolhas podem ser criadas e lidas, mas não podem ser modificadas ou eliminadas. O armazenamento imutável está disponível para contas de armazenamento v2 e Blob em todas as regiões de Azure.

Este artigo mostra como definir e gerir políticas de imutabilidade e detém dados no armazenamento blob usando o portal Azure, PowerShell ou Azure CLI. Para obter mais informações sobre o armazenamento imutável, consulte os [dados críticos do negócio da loja com armazenamento imutável](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Definir políticas de retenção e detenção legal

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Crie um novo contentor ou selecione um contentor existente para armazenar os blobs que precisam de ser mantidos no estado imutável. O recipiente deve estar numa conta de armazenamento v2 ou Blob.

2. Selecione **a política** de acesso nas definições do recipiente. Em seguida, **selecione Adicionar a política** sob armazenamento de bolhas **imutáveis**.

    ![Definições do recipiente no portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Para permitir a retenção baseada no tempo, selecione **a retenção baseada no tempo** a partir do menu suspenso.

    !["Retenção baseada no tempo" selecionada em "Tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Introduza o intervalo de retenção em dias (os valores aceitáveis são de 1 a 146000 dias).

    ![Caixa "Atualizar período de retenção para"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    O estado inicial da apólice está desbloqueado, permitindo-lhe testar a funcionalidade e fazer alterações na política antes de a bloquear. O bloqueio da política é essencial para o cumprimento de regulamentos como o SEC 17a-4.

5. Bloqueie a apólice. Clique na elipse direita (**...**), e o seguinte menu aparece com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selecione **'Lock Policy'** e confirme o bloqueio. A política está agora bloqueada e não pode ser suprimida, só serão permitidas prorrogações do intervalo de retenção. Não são permitidas as exclusões e substituições de blob. 

    ![Confirme "Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Para permitir a devidação legal, selecione **Adicionar Política**. Selecione **Legal hold** a partir do menu suspenso.

    !["Legal hold" no menu em "Tipo de Política"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Crie um porão legal com uma ou mais etiquetas.

    ![Caixa de "tag name" sob o tipo de política](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Para limpar um porão legal, remova a etiqueta de identificação legal aplicada.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A funcionalidade está incluída nos seguintes grupos de comando: `az storage container immutability-policy` e `az storage container legal-hold`. Corre `-h` para eles ver os comandos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O módulo Az.Storage suporta armazenamento imutável.  Para ativar a funcionalidade, siga estes passos:

1. Certifique-se de que tem a versão `Install-Module PowerShellGet –Repository PSGallery –Force`mais recente do PowerShellGet instalada: .
2. Remova qualquer instalação anterior da Azure PowerShell.
3. Instale o Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

O seguinte script PowerShell é para referência. Este script cria uma nova conta de armazenamento e recipiente. Mostra-lhe então como definir e limpar os apoios legais, criar e bloquear uma política de retenção baseada no tempo (também conhecida como uma política de imutabilidade) e alargar o intervalo de retenção.

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
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Definir e limpar os deters legais:

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
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperar políticas de imutabilidade:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloqueie as políticas de `-Force` imutabilidade (adicione a descartar a solicitação):

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

Remova uma política de imutabilidade desbloqueada (adicione `-Force` para descartar a solicitação):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Permitir permitir a aplicação de bolhas de apêndice protegidos escreve

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Permitir escritos adicionais de apêndice](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A funcionalidade está incluída nos seguintes grupos de comando: `az storage container immutability-policy` e `az storage container legal-hold`. Corre `-h` para eles ver os comandos.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Passos seguintes

[Armazenar dados de blob críticos de negócios com armazenamento imutável](storage-blob-immutable-storage.md)
