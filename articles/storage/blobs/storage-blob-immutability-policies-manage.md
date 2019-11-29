---
title: Definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs-armazenamento do Azure
description: Saiba como usar o suporte de WORM (gravar uma vez, ler muitos) para armazenamento de BLOB (objeto) para armazenar dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 79d7454722900eb1d9d6280e35313ef2f4a5cd54
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555681"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs

O armazenamento imutável para o armazenamento de BLOBs do Azure permite que os usuários armazenem objetos de dados críticos para os negócios em um estado de WORM (gravar uma vez, ler muitos). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Durante a duração do intervalo de retenção, os BLOBs podem ser criados e lidos, mas não podem ser modificados ou excluídos. O armazenamento imutável está disponível para contas de armazenamento de BLOBs V2 e de uso geral em todas as regiões do Azure.

Este artigo mostra como definir e gerenciar políticas de imutabilidade e isenções legais para dados no armazenamento de BLOBs usando o portal do Azure, o PowerShell ou o CLI do Azure. Para obter mais informações sobre armazenamento imutável, consulte [armazenar dados de blob críticos para os negócios com armazenamento imutável](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Definir políticas de retenção e isenções legais

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Crie um novo contentor ou selecione um contentor existente para armazenar os blobs que precisam de ser mantidos no estado imutável. O contêiner deve estar em uma conta de armazenamento de BLOBs v2 ou de uso geral.

2. Selecione **política de acesso** nas configurações do contêiner. Em seguida, selecione **Adicionar política** em **armazenamento de BLOBs imutável**.

    ![Configurações de contêiner no portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Para habilitar a retenção baseada em tempo, selecione **retenção baseada em tempo** no menu suspenso.

    !["Retenção baseada em tempo" selecionada em "tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Insira o intervalo de retenção em dias (os valores aceitáveis são de 1 a 146000 dias).

    ![Caixa "atualizar o período de retenção para"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    O estado inicial da política é desbloqueado, permitindo que você teste o recurso e faça alterações na política antes de bloqueá-lo. O bloqueio da política é essencial para conformidade com regulamentos, como SEC 17a-4.

5. Bloquear a política. Clique com o botão direito do mouse nas reticências ( **...** ) e o menu a seguir aparecerá com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selecione **Bloquear política** e confirmar o bloqueio. A política agora está bloqueada e não pode ser excluída, somente as extensões do intervalo de retenção serão permitidas. Não são permitidas exclusões e substituições de BLOB. 

    ![Confirmar "política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Para habilitar as isenções legais, selecione **Adicionar política**. Selecione **reter legal** no menu suspenso.

    !["Retenção legal" no menu em "tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Crie uma retenção legal com uma ou mais marcas.

    ![Caixa "nome da marca" sob o tipo de política](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Para limpar uma retenção legal, basta remover a marca de identificador de retenção legal aplicada.

### <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy` e `az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O módulo AZ. Storage dá suporte ao armazenamento imutável.  Para habilitar o recurso, siga estas etapas:

1. Verifique se você tem a versão mais recente do PowerShellGet instalada: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Remova qualquer instalação anterior do Azure PowerShell.
3. Instalar o Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

O exemplo de script do PowerShell a seguir é para referência. Esse script cria uma nova conta de armazenamento e um contêiner. Em seguida, ele mostra como definir e limpar as isenções legais, criar e bloquear uma política de retenção baseada em tempo (também conhecida como uma política de imutabilidade) e estender o intervalo de retenção.

Primeiro, crie uma conta de armazenamento do Azure:

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

Definir e limpar as isenções legais:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Criar ou atualizar políticas de imutabilidade:

```powershell
# Create an immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperar políticas de imutabilidade:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloquear políticas de imutabilidade (adicione `-Force` para ignorar o prompt):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Estender políticas de imutabilidade:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Remover uma política de imutabilidade desbloqueada (adicionar `-Force` para ignorar o prompt):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="next-steps"></a>Passos seguintes

[Armazene dados de blob críticos para os negócios com armazenamento imutável](storage-blob-immutable-storage.md)