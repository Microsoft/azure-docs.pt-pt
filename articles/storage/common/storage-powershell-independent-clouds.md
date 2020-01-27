---
title: Usar o PowerShell para gerenciar dados em nuvens independentes do Azure
titleSuffix: Azure Storage
description: Gerenciamento de armazenamento na nuvem da China, na nuvem governamental e na nuvem alemã usando o Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895240"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gerenciando o armazenamento nas nuvens independentes do Azure usando o PowerShell

A maioria das pessoas usa a nuvem pública do Azure para sua implantação global do Azure. Há também algumas implantações independentes de Microsoft Azure por razões da soberania e assim por diante. Essas implantações independentes são chamadas de "ambientes". A lista a seguir detalha as nuvens independentes disponíveis no momento.

* [Nuvem do Azure governamental](https://azure.microsoft.com/features/gov/)
* [Nuvem do Azure China 21Vianet operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem alemã do Azure](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Usando uma nuvem independente

Para usar o armazenamento do Azure em uma das nuvens independentes, você se conecta a essa nuvem em vez de ao público do Azure. Para usar uma das nuvens independentes em vez do público do Azure:

* Especifique o *ambiente* ao qual deseja se conectar.
* Você determina e usa as regiões disponíveis.
* Você usa o sufixo de ponto de extremidade correto, que é diferente do público do Azure.

Os exemplos exigem Azure PowerShell módulo AZ versão 0,7 ou posterior. Em uma janela do PowerShell, execute `Get-Module -ListAvailable Az` para localizar a versão. Se nada estiver listado ou você precisar atualizar, consulte [instalar Azure PowerShell Module](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Execute o cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) para ver os ambientes disponíveis do Azure:

```powershell
Get-AzEnvironment
```

Entre em sua conta que tenha acesso à nuvem à qual você deseja se conectar e definir o ambiente. Este exemplo mostra como entrar em uma conta que usa a nuvem do Azure governamental.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Para acessar a nuvem da China, use o ambiente **AzureChinaCloud**. Para acessar a nuvem em alemão, use **AzureGermanCloud**.

Neste ponto, se você precisar da lista de locais para criar uma conta de armazenamento ou outro recurso, poderá consultar os locais disponíveis para a nuvem selecionada usando [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

A tabela a seguir mostra os locais retornados para a nuvem alemã.

|Localização | Nome a Apresentar |
|----|----|
| `germanycentral` | Alemanha Central|
| `germanynortheast` | Nordeste da Alemanha |


## <a name="endpoint-suffix"></a>Sufixo do ponto de extremidade

O sufixo de ponto de extremidade para cada um desses ambientes é diferente do ponto de extremidade público do Azure. Por exemplo, o sufixo de ponto de extremidade de BLOB para o público do Azure é **BLOB.Core.Windows.net**. Para a nuvem governamental, o sufixo de ponto de extremidade de blob é **BLOB.Core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Obter ponto de extremidade usando Get-AzEnvironment

Recupere o sufixo do ponto de extremidade usando [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). O ponto de extremidade é a propriedade *StorageEndpointSuffix* do ambiente.

Os trechos de código a seguir mostram como recuperar o sufixo do ponto de extremidade. Todos esses comandos retornam algo como "core.cloudapp.net" ou "core.cloudapi.de", etc. Acrescente o sufixo ao serviço de armazenamento para acessar esse serviço. Por exemplo, "queue.core.cloudapi.de" acessará o serviço fila na nuvem alemã.

Esse trecho de código recupera todos os ambientes e o sufixo do ponto de extremidade para cada um.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Esse comando retorna os seguintes resultados.

| Nome| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Para recuperar todas as propriedades do ambiente especificado, chame **Get-AzEnvironment** e especifique o nome da nuvem. Este trecho de código retorna uma lista de propriedades; Procure **StorageEndpointSuffix** na lista. O exemplo a seguir é para a nuvem em alemão.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Os resultados são semelhantes aos seguintes valores:

|Nome da propriedade|Valor|
|----|----|
| Nome | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
Para recuperar apenas a propriedade de sufixo de ponto de extremidade de armazenamento, recupere a nuvem específica e solicite apenas essa propriedade.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Esse comando retorna as seguintes informações:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Obter ponto de extremidade de uma conta de armazenamento

Você também pode examinar as propriedades de uma conta de armazenamento para recuperar os pontos de extremidade:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Para uma conta de armazenamento na nuvem governamental, esse comando retorna a seguinte saída:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Depois de definir o ambiente

Daqui em diante, você pode usar o mesmo PowerShell usado para gerenciar suas contas de armazenamento e acessar o plano de dados, conforme descrito no artigo [usando Azure PowerShell com o armazenamento do Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou um novo grupo de recursos e uma conta de armazenamento para este exercício, você pode remover ambos os ativos excluindo o grupo de recursos. Eliminar o grupo de recursos elimina todos os recursos incluídos no grupo.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

* [Persistência de logons de usuário entre sessões do PowerShell](/powershell/azure/context-persistence)
* [Armazenamento do Azure governamental](../../azure-government/documentation-government-services-storage.md)
* [Guia do desenvolvedor do Microsoft Azure Governamental](../../azure-government/documentation-government-developer-guide.md)
* [Notas do desenvolvedor para aplicativos da 21Vianet do Azure na China](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentação do Azure Alemanha](../../germany/germany-welcome.md)
