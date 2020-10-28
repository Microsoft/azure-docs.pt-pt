---
title: Use o PowerShell para gerir dados em nuvens independentes do Azure
titleSuffix: Azure Storage
description: Gerir o armazenamento na Nuvem da China, nuvem do governo e nuvem alemã usando Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783577"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gerir o Armazenamento nas nuvens independentes do Azure utilizando o PowerShell

A maioria das pessoas usa a Nuvem Pública Azure para a sua implantação global do Azure. Há também algumas implementações independentes do Microsoft Azure por razões de soberania e assim por diante. Estas implementações independentes são referidas como "ambientes". A lista a seguir detalha as nuvens independentes atualmente disponíveis.

* [Nuvem do Governo de Azure](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud operado pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem Alemã Azure](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Usando uma nuvem independente

Para utilizar o Azure Storage numa das nuvens independentes, liga-se a essa nuvem em vez do Público Azure. Para usar uma das nuvens independentes em vez de Azure Public:

* Especifica o *ambiente* a que se deve ligar.
* Você determina e usa as regiões disponíveis.
* Utilize o sufixo de ponta final correto, que é diferente do Público Azure.

Os exemplos requerem a versão Azure PowerShell Az ou mais tarde. Numa janela PowerShell, corra `Get-Module -ListAvailable Az` para encontrar a versão. Se nada estiver listado ou precisar de atualizar, consulte [o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Executar o [cmdlet Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) para ver os ambientes Azure disponíveis:

```powershell
Get-AzEnvironment
```

Faça o sômis à sua conta que tenha acesso à nuvem à qual pretende ligar e definir o ambiente. Este exemplo mostra como assinar numa conta que utiliza a Nuvem do Governo Azure.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Para aceder à Nuvem Chinesa, utilize o ambiente **AzureChinaCloud.** Para aceder à Nuvem Alemã, utilize o **AzureGermanCloud** .

Neste momento, se necessitar da lista de locais para criar uma conta de armazenamento ou outro recurso, pode consultar as localizações disponíveis para a nuvem selecionada utilizando o [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

```powershell
Get-AzLocation | select Location, DisplayName
```

A tabela seguinte mostra os locais devolvidos para a nuvem alemã.

|Localização | Nome a Apresentar |
|----|----|
| `germanycentral` | Alemanha Central|
| `germanynortheast` | Nordeste da Alemanha |


## <a name="endpoint-suffix"></a>Sufixo de ponto final

O sufixo final para cada um destes ambientes é diferente do ponto final do Azure Public. Por exemplo, o sufixo blob endpoint para O Público azul é **blob.core.windows.net** . Para a Nuvem do Governo, o sufixo do ponto final blob é **blob.core.usgovcloudapi.net** .

### <a name="get-endpoint-using-get-azenvironment"></a>Obtenha o ponto final usando Get-AzEnvironment

Recupere o sufixo do ponto final utilizando [o Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). O ponto final é a propriedade *StorageEndpointSuffix* do ambiente.

Os seguintes fragmentos de código mostram como recuperar o sufixo do ponto final. Todos estes comandos devolvem algo como "core.cloudapp.net" ou "core.cloudapi.de", etc. Aped o sufixo ao serviço de armazenamento para aceder a esse serviço. Por exemplo, "queue.core.cloudapi.de" acederá ao serviço de fila na Nuvem Alemã.

Este código corta-se para todos os ambientes e o sufixo do ponto final para cada um.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Este comando retorna os seguintes resultados.

| Nome| StorageEndpointSuléix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Para recuperar todas as propriedades para o ambiente especificado, ligue para **o Get-AzEnvironment** e especifique o nome da nuvem. Este código corta-código devolve uma lista de propriedades; procure **por StorageEndpointS sufixo** na lista. O exemplo a seguir é para a Nuvem Alemã.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Os resultados são semelhantes aos seguintes valores:

|Nome da Propriedade|Valor|
|----|----|
| Nome | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourcei | `http://management.core.cloudapi.de/` |
| GaleriaURL | `https://gallery.cloudapi.de/` |
| GestãoPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublicarSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsS sulés | `.database.cloudapi.de` |
| **StorageEndpointSuléix** | `core.cloudapi.de` |
| ... | ... |

Para recuperar apenas a propriedade de sufixo de ponto final de armazenamento, recupere a nuvem específica e peça apenas essa propriedade.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Este comando devolve as seguintes informações:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Obtenha o ponto final de uma conta de armazenamento

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

Agora pode utilizar o PowerShell para gerir as suas contas de armazenamento e aceder a bolhas, filas, ficheiros e dados de tabela. Para mais informações, consulte [Az.Storage.](/powershell/module/az.storage)

## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um novo grupo de recursos e uma conta de armazenamento para este exercício, pode remover ambos os ativos eliminando o grupo de recursos. Eliminar o grupo de recursos elimina todos os recursos incluídos no grupo.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

* [Inícios de sessão do utilizador persistentes entre sessões do PowerShell](/powershell/azure/context-persistence)
* [Armazenamento do Governo de Azure](../../azure-government/compare-azure-government-global-azure.md)
* [Guia de desenvolvimento do governo da Microsoft Azure](../../azure-government/documentation-government-developer-guide.md)
* [Notas de desenvolvimento para aplicações Azure China 21Vianet](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentação Azure Alemanha](../../germany/germany-welcome.md)