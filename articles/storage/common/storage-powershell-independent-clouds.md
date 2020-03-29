---
title: Use o PowerShell para gerir dados em nuvens independentes do Azure
titleSuffix: Azure Storage
description: Gerir o armazenamento na Nuvem chinesa, nuvem governamental e nuvem alemã usando azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895240"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gerir o armazenamento nas nuvens independentes do Azure usando a PowerShell

A maioria das pessoas usa a Nuvem Pública Azure para a sua implantação global do Azure. Existem também algumas implementações independentes do Microsoft Azure por razões de soberania e assim por diante. Estas implantações independentes são referidas como "ambientes". A lista seguinte detalha as nuvens independentes atualmente disponíveis.

* [Nuvem do Governo de Azure](https://azure.microsoft.com/features/gov/)
* [Nuvem Azure China 21Vianet operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem Alemã Azul](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Usando uma nuvem independente

Para utilizar o Armazenamento Azure numa das nuvens independentes, liga-se a essa nuvem em vez do Azure Public. Para usar uma das nuvens independentes em vez de Azure Public:

* Especifica-se o *ambiente* para se ligar.
* Determina e utiliza as regiões disponíveis.
* Usa o sufixo final correto, que é diferente do Azure Public.

Os exemplos requerem o módulo Azure PowerShell Az versão 0.7 ou posterior. Numa janela powerShell, `Get-Module -ListAvailable Az` corra para encontrar a versão. Se nada estiver listado, ou precisar de fazer upgrade, consulte Instalar o [módulo PowerShell Azure](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Executar o cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) para ver os ambientes Azure disponíveis:

```powershell
Get-AzEnvironment
```

Inscreva-se na sua conta que tenha acesso à nuvem à qual pretende ligar e definir o ambiente. Este exemplo mostra como assinar uma conta que usa a Nuvem do Governo Azure.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Para aceder à Nuvem Chinesa, use o ambiente **AzureChinaCloud**. Para aceder à Nuvem Alemã, utilize **o AzureGermanCloud**.

Neste ponto, se precisar da lista de locais para criar uma conta de armazenamento ou outro recurso, pode consultar os locais disponíveis para a nuvem selecionada utilizando [o Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

A seguinte tabela mostra os locais devolvidos para a nuvem alemã.

|Localização | Nome a Apresentar |
|----|----|
| `germanycentral` | Alemanha Central|
| `germanynortheast` | Nordeste da Alemanha |


## <a name="endpoint-suffix"></a>Sufixo endpoint

O sufixo final para cada um destes ambientes é diferente do ponto final do Azure Public. Por exemplo, o sufixo final de blob para o Azure Public é **blob.core.windows.net**. Para a Nuvem do Governo, o sufixo final de blob é **blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Obtenha ponto final usando Get-AzEnvironment

Recupere o sufixo de ponto final utilizando [o Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). O ponto final é a propriedade *StorageEndpointSuffix* do ambiente.

Os seguintes fragmentos de código mostram como recuperar o sufixo final. Todos estes comandos devolvem algo como "core.cloudapp.net" ou "core.cloudapi.de", etc. Acomode o sufixo ao serviço de armazenamento para aceder a esse serviço. Por exemplo, "queue.core.cloudapi.de" acederá ao serviço de fila na Cloud Alemã.

Este fragmento de código recupera todos os ambientes e o sufixo final para cada um.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Este comando devolve os seguintes resultados.

| Nome| ArmazenamentoEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Para recuperar todas as propriedades para o ambiente especificado, ligue para **Get-AzEnvironment** e especifique o nome da nuvem. Este código de snippet devolve uma lista de propriedades; procure **StorageEndpointSuffix** na lista. O exemplo que se segue é para a Nuvem Alemã.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Os resultados são semelhantes aos seguintes valores:

|Nome da Propriedade|Valor|
|----|----|
| Nome | `AzureGermanCloud` |
| Autenticação EnableAdfs | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GaleriaURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublicaçãoDefiniçõesFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| Url de Gestor de Recursos | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **ArmazenamentoEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
Para recuperar apenas a propriedade de sufixo de ponto final de armazenamento, recupere a nuvem específica e peça apenas aquela propriedade.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Este comando devolve as seguintes informações:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Obtenha ponto final de uma conta de armazenamento

Também pode examinar as propriedades de uma conta de armazenamento para recuperar os pontos finais:

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

Para uma conta de armazenamento na Nuvem do Governo, este comando devolve a seguinte saída:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Depois de definir o ambiente

A partir daqui para a frente, pode utilizar o mesmo PowerShell usado para gerir as suas contas de armazenamento e aceder ao plano de dados, conforme descrito no artigo [Utilizando o Azure PowerShell com o Armazenamento Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se criou um novo grupo de recursos e uma conta de armazenamento para este exercício, pode remover ambos os ativos eliminando o grupo de recursos. Eliminar o grupo de recursos elimina todos os recursos incluídos no grupo.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

* [Inícios de sessão do utilizador persistentes entre sessões do PowerShell](/powershell/azure/context-persistence)
* [Armazenamento do Governo de Azure](../../azure-government/documentation-government-services-storage.md)
* [Guia de desenvolvimento do governo da Microsoft Azure](../../azure-government/documentation-government-developer-guide.md)
* [Notas de desenvolvimento para aplicações Azure China 21Vianet](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentação Azure Germany](../../germany/germany-welcome.md)
