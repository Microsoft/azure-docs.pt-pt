---
title: Script do Azure PowerShell de exemplo - calcular o tamanho total de faturação de um contentor de BLOBs | Documentos da Microsoft
description: Calcule o tamanho total de um contentor no armazenamento de Blobs do Azure para efeitos de faturação.
services: storage
documentationcenter: na
author: WenJason
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
origin.date: 11/07/2017
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 02b4cfcc6d88430701f653665269532a4eb7092f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60460644"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Calcular o tamanho total de faturação de um contentor de BLOBs

Este script calcula o tamanho de um contentor no armazenamento de Blobs do Azure com o objetivo de estimar os custos de faturação. O script os totais do tamanho dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Este script do PowerShell calcula o tamanho de um contentor para efeitos de faturação. Se estiver a calcular o tamanho do contentor para outros fins, consulte [calcular o tamanho total de um contentor de armazenamento de BLOBs](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script mais simples que fornece uma estimativa.

## <a name="determine-the-size-of-the-blob-container"></a>Determinar o tamanho do contentor de BLOBs

O tamanho total do contentor de BLOBs inclui o tamanho do contentor em si e o tamanho de todos os blobs no contentor.

As seções a seguir descreve como a capacidade de armazenamento é calculada para contentores de BLOBs e blobs. Na secção seguinte, Len(X) significa que o número de carateres na cadeia de caracteres.

### <a name="blob-containers"></a>Contentores de BLOBs

O cálculo seguinte descreve como estimar a quantidade de armazenamento é consumido por contentor de BLOBs:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Segue-se a análise detalhada:
* 48 bytes de overhead para cada contentor inclui a hora da última modificação, permissões, as definições de público e alguns metadados do sistema.

* O nome do contentor é armazenado como Unicode, por isso, coloque o número de carateres e multiplicar por dois.

* Para cada bloco de metadados do contentor de blob armazenado, armazenamos o comprimento do nome (ASCII), além do comprimento do valor de cadeia.

* As 512 bytes por identificador assinado inclui o nome do identificador assinado, hora de início, hora de expiração e as permissões.

### <a name="blobs"></a>Blobs

Os cálculos seguintes mostram como estimar a quantidade de armazenamento consumido por blob.

* Blob de blocos (ou instantâneo de blob de base):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Blob de página (blob de base ou instantâneo):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Segue-se a análise detalhada:

* bytes 124 de sobrecarga para o blob, que inclui:
    - Hora da Última Modificação
    - Tamanho
    - Cache-Control
    - Content-Type
    - Idioma do conteúdo
    - Codificação de conteúdo
    - Content-MD5
    - Permissões
    - Informações de instantâneo
    - Concessão
    - Alguns metadados do sistema

* O nome do blob é armazenado como Unicode, por isso, coloque o número de carateres e multiplicar por dois.

* Para cada bloco de metadados armazenados, adicione o comprimento do nome (armazenado como ASCII), além do comprimento do valor de cadeia.

* Para os blobs de bloco:
  * 8 bytes para a lista de bloqueios.
  * Número de blocos vezes o tamanho do ID de bloco em bytes.
  * O tamanho dos dados em todos os blocos consolidados e não consolidados.

    >[!NOTE]
    >Quando os instantâneos são usados, este tamanho inclui apenas os dados exclusivos para este blob base ou de instantâneo. Se os blocos não consolidados não forem utilizados após uma semana, são coletado pelo lixo. Depois disso, eles não contam para faturação.

* Para blobs de página:
  * O número de intervalos de página não consecutivos com dados vezes 12 bytes. Este é o número de intervalos de página exclusiva vir ao chamar o **GetPageRanges** API.

  * O tamanho dos dados em bytes de todas as páginas armazenadas.

    >[!NOTE]
    >Quando os instantâneos são usados, este tamanho inclui somente as páginas exclusivas para o blob de base ou o blob de instantâneo que está a ser contabilizado.

## <a name="sample-script"></a>Script de exemplo

```powershell

# this script will show how to get the total size of the blobs in a container
# before running this, you need to create a storage account, create a container,
#    and upload some blobs into the container
# note: this retrieves all of the blobs in the container in one command.
#       connect Azure with Login-AzAccount -EnvironmentName AzureChinaCloud before you run the script.
#       requests sent as part of this tool will incur transactional costs. 
# command line usage: script.ps1 -ResourceGroup {YourResourceGroupName} -StorageAccountName {YourAccountName} -ContainerName {YourContainerName}
#


param(
    [Parameter(Mandatory=$true)]
    [string]$ResourceGroup,

    [Parameter(Mandatory=$true)]
    [string]$StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]$ContainerName
)

#Set-StrictMode will cause Get-AzureStorageBlob returns result in different data types when there is only one blob
#Set-StrictMode -Version 2

$VerbosePreference = "Continue"

if((Get-Module -ListAvailable Az.Storage) -eq $null)
{
    throw "Azure Powershell not found! Please install from https://docs.microsoft.com/en-us/powershell/azure/install-Az-ps"
}

# function Retry-OnRequest
function Retry-OnRequest
{
    param(
        [Parameter(Mandatory=$true)]
        $Action)
    
    # It could encounter various of temporary errors, like network errors, or storage server busy errors.
    # Should retry the request on transient errors

    # Retry on storage server timeout errors
    $clientTimeOut = New-TimeSpan -Minutes 15
    $retryPolicy = New-Object -TypeName Microsoft.WindowsAzure.Storage.RetryPolicies.ExponentialRetry -ArgumentList @($clientTimeOut, 10)        
    $requestOption = @{}
    $requestOption.RetryPolicy = $retryPolicy

    # Retry on temporary network errors
    $shouldRetryOnException = $false
    $maxRetryCountOnException = 3

    do
    {
        try
        {
            return $Action.Invoke($requestOption)
        }
        catch
        {
            if ($_.Exception.InnerException -ne $null -And $_.Exception.InnerException.GetType() -Eq [System.TimeoutException] -And $maxRetryCountOnException -gt 0)
            {
                $shouldRetryOnException = $true
                $maxRetryCountOnException--
            }
            else
            {
                $shouldRetryOnException = $false
                throw
            }
        }
    }
    while ($shouldRetryOnException)

}

# function Get-BlobBytes

function Get-BlobBytes
{
    param(
        [Parameter(Mandatory=$true)]
        $Blob,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + blobname
    $blobSizeInBytes = 124 + $Blob.Name.Length * 2

    # Get size of metadata
    $metadataEnumerator=$Blob.ICloudBlob.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $blobSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    if (!$IsPremiumAccount)
    {
        if($Blob.BlobType -eq [Microsoft.WindowsAzure.Storage.Blob.BlobType]::BlockBlob)
        {
            $blobSizeInBytes += 8
            # Default is Microsoft.WindowsAzure.Storage.Blob.BlockListingFilter.Committed. Need All
            $action = { param($requestOption) return $Blob.ICloudBlob.DownloadBlockList([Microsoft.WindowsAzure.Storage.Blob.BlockListingFilter]::All, $null, $requestOption) }                

            $blocks=Retry-OnRequest $action      

            if ($null -eq $blocks)
            {
                $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
            }
            else
            {
                $blocks | ForEach-Object { $blobSizeInBytes += $_.Length + $_.Name.Length }
            }  
        }
        elseif($Blob.BlobType -eq [Microsoft.WindowsAzure.Storage.Blob.BlobType]::PageBlob)
        {
            # It could cause server time out issue when trying to get page ranges of highly fragmented page blob 
            # Get page ranges in segment can mitigate chance of meeting such kind of server time out issue
            # See https://blogs.msdn.microsoft.com/windowsazurestorage/2012/03/26/getting-the-page-ranges-of-a-large-page-blob-in-segments/ for details.
            $pageRangesSegSize = 148 * 1024 * 1024L
            $totalSize = $Blob.ICloudBlob.Properties.Length
            $pageRangeSegOffset = 0
        
            $pageRangesTemp = New-Object System.Collections.ArrayList
        
            while ($pageRangeSegOffset -lt $totalSize)
            {
                $action = {param($requestOption) return $Blob.ICloudBlob.GetPageRanges($pageRangeSegOffset, $pageRangesSegSize, $null, $requestOption) }

                Retry-OnRequest $action | ForEach-Object { $pageRangesTemp.Add($_) }  | Out-Null
                $pageRangeSegOffset += $pageRangesSegSize
            }

            $pageRanges = New-Object System.Collections.ArrayList

            foreach ($pageRange in $pageRangesTemp)
            {
                if($lastRange -eq $Null)
                {
                    $lastRange = New-Object PageRange
                    $lastRange.StartOffset = $pageRange.StartOffset
                    $lastRange.EndOffset =  $pageRange.EndOffset
                }
                else
                {
                    if (($lastRange.EndOffset + 1) -eq $pageRange.StartOffset)
                    {
                        $lastRange.EndOffset = $pageRange.EndOffset
                    }
                    else
                    {
                        $pageRanges.Add($lastRange)  | Out-Null
                        $lastRange = New-Object PageRange
                        $lastRange.StartOffset = $pageRange.StartOffset
                        $lastRange.EndOffset =  $pageRange.EndOffset
                    }
                }
            }

            $pageRanges.Add($lastRange) | Out-Null
            $pageRanges |  ForEach-Object { 
                    $blobSizeInBytes += 12 + $_.EndOffset - $_.StartOffset 
                }
        }
        else
        {
            $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
        }
        return $blobSizeInBytes
    }
    else
    {
        $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
    }
    return $blobSizeInBytes
}

# function Get-ContainerBytes

function Get-ContainerBytes
{
    param(
        [Parameter(Mandatory=$true)]
        [Microsoft.WindowsAzure.Storage.Blob.CloudBlobContainer]$Container,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + name of container
    $containerSizeInBytes = 48 + $Container.Name.Length*2

    # Get size of metadata
    $metadataEnumerator = $Container.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $containerSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    # Get size for SharedAccessPolicies
    $containerSizeInBytes += $Container.GetPermissions().SharedAccessPolicies.Count * 512

    # Calculate size of all blobs.
    $blobCount = 0
    $Token = $Null
    $MaxReturn = 5000

    do {
        $Blobs = Get-AzStorageBlob -Context $storageContext -Container $Container.Name -MaxCount $MaxReturn -ContinuationToken $Token
        if($Blobs -eq $Null) { break }

        #Set-StrictMode will cause Get-AzureStorageBlob returns result in different data types when there is only one blob
        if($Blobs.GetType().Name -eq "AzureStorageBlob")
        {
            $Token = $Null
        }
        else
        {
            $Token = $Blobs[$Blobs.Count - 1].ContinuationToken;
        }

        $Blobs | ForEach-Object {
                $blobSize = Get-BlobBytes $_ $IsPremiumAccount
                $containerSizeInBytes += $blobSize
                $blobCount++

                if(($blobCount % 1000) -eq 0)
                {
                    Write-Verbose("Counting {0} Sizing {1} " -f $blobCount, $containerSizeInBytes)
                }
            }
    }
    While ($Token -ne $Null)

    return @{ "containerSize" = $containerSizeInBytes; "blobCount" = $blobCount }
}

#Login-AzAccount -EnvironmentName AzureChinaCloud

$storageAccount = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -ErrorAction SilentlyContinue
if($storageAccount -eq $null)
{
    throw "The storage account specified does not exist in this subscription."
}

$storageContext = $storageAccount.Context

if (-not ([System.Management.Automation.PSTypeName]'PageRange').Type)
{
    $Source = "
        public class PageRange
        {
            public long StartOffset;
            public long EndOffset;
        }"
    Add-Type -TypeDefinition $Source
}

$containers = New-Object System.Collections.ArrayList
if($ContainerName.Length -ne 0)
{
    $container = Get-AzStorageContainer -Context $storageContext -Name $ContainerName -ErrorAction SilentlyContinue |
        ForEach-Object { $containers.Add($_) } | Out-Null
}
else
{
    Get-AzStorageContainer -Context $storageContext | ForEach-Object { $containers.Add($_) } | Out-Null
}

$sizeInBytes = 0
$IsPremiumAccount = ($storageAccount.Sku.Tier -eq "Premium")

if($containers.Count -gt 0)
{
    $containers | ForEach-Object {
        Write-Output("Calculating container {0} ..." -f $_.CloudBlobContainer.Name)
        $result = Get-ContainerBytes $_.CloudBlobContainer $IsPremiumAccount
        $sizeInBytes += $result.containerSize

        Write-Output("Container '{0}' with {1} blobs has a sizeof {2:F2} MB." -f $_.CloudBlobContainer.Name,$result.blobCount,($result.containerSize/1MB))
    }
}
else
{
    Write-Warning "No containers found to process in storage account '$StorageAccountName'."
}
```

## <a name="next-steps"></a>Passos Seguintes

- Ver [calcular o tamanho total de um contentor de armazenamento de BLOBs](../scripts/storage-blobs-container-calculate-size-powershell.md) para um script simple que fornece uma estimativa do tamanho do contentor.

- Para obter mais informações sobre a faturação do armazenamento do Azure, consulte [Noções básicas sobre Windows Azure a cobrança de armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

- Pode encontrar exemplos de scripts do PowerShell de armazenamento adicionais no [exemplos do PowerShell do armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
