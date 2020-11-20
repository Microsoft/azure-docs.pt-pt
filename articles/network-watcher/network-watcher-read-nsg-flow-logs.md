---
title: Leia os registos de fluxo do NSG Microsoft Docs
description: Saiba como utilizar o Azure PowerShell para analisar os registos de fluxo do Grupo de Segurança da Rede, que são criados de hora a hora e atualizados a cada poucos minutos no Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: damendo
ms.openlocfilehash: 1f9b9e91cda93a986fdaaf0f53d8987544e783a2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966467"
---
# <a name="read-nsg-flow-logs"></a>Ler registos do fluxo do NSG

Saiba como ler entradas de registos de fluxo NSG com PowerShell.

Os registos de fluxo NSG são armazenados numa conta de armazenamento em [blocos.](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) As bolhas de bloco são compostas por blocos menores. Cada tronco é uma bolha de bloco separada que é gerada a cada hora. Novos registos são gerados a cada hora, os registos são atualizados com novas entradas a cada poucos minutos com os dados mais recentes. Neste artigo aprende-se a ler partes dos registos de fluxo.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Cenário

No cenário seguinte, tem um registo de fluxo de exemplo que é armazenado numa conta de armazenamento. Aprende-se a ler seletivamente os eventos mais recentes nos registos de fluxo do NSG. Neste artigo utiliza-se o PowerShell, no entanto, os conceitos discutidos no artigo não se limitam à linguagem de programação, e são aplicáveis a todas as línguas suportadas pelas APIs de Armazenamento Azure.

## <a name="setup"></a>Configuração

Antes de começar, tem de ter o Fluxo de Fluxo de Rede ativado em um ou em muitos Grupos de Segurança da Rede na sua conta. Para obter instruções sobre a ativação dos registos de fluxo de segurança da rede, consulte o seguinte artigo: [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Recupere a lista de blocos

O PowerShell seguinte configura as variáveis necessárias para consultar a bolha de registo de fluxo NSG e listar os blocos dentro da bolha do bloco [CloudBlockBlob.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) Atualize o script para conter valores válidos para o seu ambiente.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockListAsync()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

A `$blockList` variável devolve uma lista dos blocos na bolha. Cada bloco blob contém pelo menos dois blocos.  O primeiro bloco tem um comprimento de `12` bytes, este bloco contém os suportes de abertura do log json. O outro bloco são os suportes de fecho e tem um comprimento de `2` bytes.  Como pode ver, o seguinte registo de exemplo tem sete entradas nele, sendo cada uma uma entrada individual. Todas as novas entradas no registo são adicionadas ao fim imediatamente antes do bloco final.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Leia a bolha do bloco

Em seguida, precisa ler a `$blocklist` variável para recuperar os dados. Neste exemplo, iteramos através da lista de bloqueios, lemos os bytes de cada quarteirão e os contamos numa matriz. Utilize o método [DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray) para recuperar os dados.

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
    # Set the size of the byte array to the largest block
    $maxvalue = ($blocklist | measure Length -Maximum).Maximum

    # Create an array to store values in
    $valuearray = @()

    # Define the starting index to track the current block being read
    $index = 0

    # Loop through each block in the block list
    for($i=0; $i -lt $blocklist.count; $i++)
    {
        # Create a byte array object to story the bytes from the block
        $downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

        # Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

Agora a `$valuearray` matriz contém o valor de cadeia de cada bloco. Para verificar a entrada, obtenha o segundo ao último valor da matriz executando `$valuearray[$valuearray.Length-2]` . Não quer o último valor, porque é o suporte de fecho.

Os resultados deste valor são apresentados no seguinte exemplo:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Este cenário é um exemplo de como ler entradas em registos de fluxo NSG sem ter que analisar todo o log. Pode ler novas entradas no registo à medida que são escritas utilizando o ID do bloco ou rastreando o comprimento dos blocos armazenados na bolha de bloco. Isto permite-lhe ler apenas as novas entradas.

## <a name="next-steps"></a>Passos seguintes


Visite [Use Elástico Stack](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), Use [Grafana](network-watcher-nsg-grafana.md)e [Use Graylog](network-watcher-analyze-nsg-flow-logs-graylog.md) para saber mais sobre formas de ver os registos de fluxo NSG. Uma abordagem da Função Open Source Azure para consumir as bolhas diretamente e emitir para vários consumidores de análise de registo pode ser encontrada aqui: [Azure Network Watcher NSG Flow Logs Connector](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector).

Você pode usar [a Azure Traffic Analytics](./traffic-analytics.md) para obter informações sobre os seus fluxos de tráfego. Traffic Analytics utiliza [o Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) para tornar o seu fluxo de tráfego questionável.

Para saber mais sobre as bolhas de armazenamento visite: [Azure Functions Blob encadernações de armazenamento](../azure-functions/functions-bindings-storage-blob.md)