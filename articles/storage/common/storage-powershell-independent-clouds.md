---
title: Gerir o armazenamento em independente de Azure nuvens a utilizar o Azure PowerShell | Documentos da Microsoft
description: Gerir o armazenamento na cloud na China, Government Cloud e Cloud da Alemanha utilizar o Azure PowerShell
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 69707eec0ea1f2260ee50a48ce1dcb82dc9ddd8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145873"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gerir o armazenamento em clouds independentes do Azure, com o PowerShell

A maioria das pessoas utilizar a Cloud pública do Azure para a sua implementação do Azure global. Existem também algumas implementações independentes do Microsoft Azure por motivos de soberania de dados e assim por diante. Estas implementações independentes são referidas como "ambientes". A lista seguinte enumera as nuvens independentes, atualmente disponíveis.

* [O Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Cloud do Azure na China, operado pela 21Vianet na China](http://www.windowsazure.cn/)
* [O Azure Cloud da Alemanha](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Utilizar uma cloud independente 

Para utilizar o armazenamento do Azure das nuvens independentes, vai ligar essa nuvem em vez de pública do Azure. Para utilizar um das clouds independentes em vez de pública do Azure:

* Especificar a *ambiente* para estabelecer ligação.
* Determinar e utilizar as regiões disponíveis.
* Utilize o sufixo de ponto final correto, o que é diferente do Azure público.

Os exemplos requerem o Azure PowerShell versão do módulo Az 0,7 ou posterior. Numa janela do PowerShell, execute `Get-Module -ListAvailable Az` para localizar a versão. Se nada estiver listado, ou terá de atualizar, veja [módulo de instalar o Azure PowerShell](/powershell/azure/install-Az-ps). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Executar o [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) cmdlet para ver os ambientes do Azure disponíveis:
   
```powershell
Get-AzEnvironment
```

Inicie sessão na sua conta que tenha acesso para a cloud para o qual pretende ligar e defina o ambiente. Este exemplo mostra como iniciar sessão numa conta que utiliza a Cloud do Azure Government.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Para aceder a cloud na China, utilize o ambiente **AzureChinaCloud**. Para acessar a nuvem da Alemanha, use **AzureGermanCloud**.

Neste momento, se precisar da lista de localizações para criar uma conta de armazenamento ou outro recurso, pode consultar as localizações disponíveis para a nuvem selecionada utilizando [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

A tabela seguinte mostra as localizações devolvidas para a cloud da Alemanha.

|Location | displayName |
|----|----|
| germanycentral | Alemanha Central|
| germanynortheast | Alemanha Nordeste | 


## <a name="endpoint-suffix"></a>Sufixo de ponto final

O sufixo de ponto final de cada um desses ambientes é diferente do ponto final público do Azure. Por exemplo, é o sufixo de ponto final do blob para o Azure público **blob.core.windows.net**. Para a Cloud de governo, é o sufixo de ponto final do blob **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azenvironment"></a>Obter ponto de extremidade usando Get-AzEnvironment 

Obter o sufixo de ponto final utilizando [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). O ponto final é o *StorageEndpointSuffix* propriedade do ambiente. Os fragmentos de código seguintes mostram como fazer isso. Todos esses comandos retornam algo como "core.cloudapp.net" ou "core.cloudapi.de", etc. Acrescente isso para o serviço de armazenamento para aceder a esse serviço. Por exemplo, "queue.core.cloudapi.de" terão acesso ao serviço de fila em nuvem da Alemanha.

Este fragmento de código obtém todos os ambientes e o sufixo de ponto final para cada um deles.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Este comando devolve os seguintes resultados.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Para obter todas as propriedades para o ambiente especificado, chame **Get-AzEnvironment** e especifique o nome de nuvem. Este fragmento de código devolve uma lista de propriedades; Procure **StorageEndpointSuffix** na lista. O exemplo seguinte é para a nuvem da Alemanha.

```powershell
Get-AzEnvironment -Name AzureGermanCloud 
```

Os resultados são semelhantes ao seguinte:

|Nome da propriedade|Value|
|----|----|
| Name | AzureGermanCloud |
| EnableAdfsAuthentication | Falso |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Para recuperar apenas a propriedade de sufixo de ponto final armazenamento, obter a cloud específica e peça para apenas essa propriedade de um.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Isso retorna as seguintes informações.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Obter o ponto final de uma conta de armazenamento

Também pode examinar as propriedades de uma conta de armazenamento para recuperar os pontos de extremidade. Isto pode ser útil se já estiver a utilizar uma conta de armazenamento no seu script do PowerShell; apenas pode obter o ponto final que precisa. 

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

Para uma conta de armazenamento na Cloud de governo, isso retorna o seguinte: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Depois de definir o ambiente

A partir de aqui daqui em diante, pode utilizar o mesmo PowerShell utilizado para gerir as contas de armazenamento e aceder ao plano de dados, conforme descrito no artigo [utilizar o Azure PowerShell com armazenamento do Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e uma conta de armazenamento para este exercício, pode remover todos os ativos ao remover o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Neste caso, remove a conta de armazenamento que criou e o grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

* [Inícios de sessão de utilizador persistentes entre sessões do PowerShell](/powershell/azure/context-persistence)
* [Armazenamento do Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Manual de programação do Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Notas do programador para as aplicações do Azure na China](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentação do Azure Alemanha](../../germany/germany-welcome.md)
