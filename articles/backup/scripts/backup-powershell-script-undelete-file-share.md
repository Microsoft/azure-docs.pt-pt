---
title: PowerShell Script - Desdelete uma partilha de ficheiros eliminada
description: Aprenda a usar um script Azure PowerShell para desempacoar uma partilha de ficheiros acidentalmente eliminada.
ms.topic: sample
ms.date: 02/02/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99cd980e13b3b9b9a7a450520218c76ccf26fd69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079868"
---
# <a name="powershell-script-to-undelete-an-accidentally-deleted-file-share"></a>Script PowerShell para desembrulhá-lo de uma partilha de ficheiro acidentalmente eliminada

Este script ajuda-o a desempacoar uma partilha de ficheiros, se o eliminar acidentalmente. A funcionalidade de segurança de exclusão suave para ações de ficheiros proporciona-lhe a opção de desatar uma partilha de ficheiros dentro do período de retenção de 14 dias, permitindo a recuperação de todos os conteúdos, instantâneos e pontos de recuperação da sua partilha de ficheiros. Para saber mais sobre a eliminação suave, visite este [link.](../soft-delete-azure-file-share.md)

## <a name="sample-script"></a>Script de exemplo

```powershell
#Import-Module Az.Storage -MinimumVersion 1.7.0 -Scope Local
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId,
        [Parameter(Mandatory=$False)][System.Boolean] $ListOption,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
    )

Function Restore-DeletedFileShare
{
    Param(
        [Parameter(Mandatory=$True)][Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext] $Context,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
        )

    if ([string]::IsNullOrWhiteSpace($FileShareName))
    {
        Write-Error "Please specify the required input parameter: FileShareName" -ErrorAction Stop
    }

    $FileShareName = $FileShareName.ToLowerInvariant()

    Write-Verbose "Restoring a file share with the name: $FileShareName" -Verbose


    Write-Information -MessageData "Started: Creating SASToken to List File Shares" -InformationAction Continue

    $listToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Service -Permission "l" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to List File Shares" -InformationAction Continue

    Write-Information -MessageData "Started: Listing File Shares to find the deleted file share" -InformationAction Continue

    $listSharesUrl = [string]::Concat($Context.FileEndPoint, "?include=metadata,deleted&comp=list&api-version=2019-10-10&", $listToken.Substring(1))

    $listSharesResponse = Invoke-WebRequest $listSharesUrl -Method "GET" -Verbose

    if ($listSharesResponse.StatusCode -ne 200)
    {
        Write-Error "Request to list file shares failed." -ErrorAction Stop
    }

    Write-Verbose $listSharesResponse.RawContent -Verbose

    $listSharesResponseContent = $listSharesResponse.Content.Substring(3)

    Write-Information -MessageData "Completed: Listing File Shares to find the deleted file share" -InformationAction Continue

    Write-Information -MessageData "Started: Search for a deleted file share with the specified name" -InformationAction Continue

    $deletedFileShares = Select-Xml -Content $listSharesResponseContent -XPath "/EnumerationResults/Shares/Share[Deleted=""true"" and Name=""$FileShareName""]"

    $matchedCount = 0
    $deletedShareVersions = New-Object System.Collections.Generic.List[string]

    foreach($share in $deletedFileShares)
    {
        if($matchedCount -eq 0)
        {
          Write-Verbose $share.Node.InnerXml -Verbose

          Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found versions" -InformationAction Continue
        }

        $shareVer = $share.Node.Item("Version").InnerText
        $shareDelTime = $share.Node.Item("Properties").Item("DeletedTime").InnerText
        $retDays = $share.Node.Item("Properties").Item("RemainingRetentionDays").InnerText

        $deletedShareVersions.Add($share.Node.Item("Version").InnerText)

        Write-Information -MessageData "DeletedVersion: $shareVer, DeletedTime: $shareDelTime, RemainingRetentionDays: $retDays"  -InformationAction Continue

        $matchedCount++
    }

    if($ListOption -eq $True)
    {
       return;
    }

    if ($matchedCount -eq 0)
    {
        Write-Error "Deleted file share with the specified name was not found." -ErrorAction Stop
    }
    elseif($matchedCount -eq 1 -and ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or $deletedShareVersions.Contains($DeletedShareVersion)))
    {
      $DeletedShareVersion = $deletedShareVersions
    }
    elseif ($matchedCount -gt 1)
    {
      if ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or !$deletedShareVersions.Contains($DeletedShareVersion))
      {
        Write-Error "More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values." -ErrorAction Stop
      }
    }

    Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found version: $DeletedShareVersion" -InformationAction Continue

    Write-Information -MessageData "Started: Creating SASToken to Restore File Share" -InformationAction Continue

    $restoreToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Container -Permission "w" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to Restore File Share" -InformationAction Continue

    Write-Information -MessageData "Started: Restore File Share" -InformationAction Continue

 $restoreShareUrl = [string]::Concat($Context.FileEndPoint, $FileShareName, "?restype=share&comp=undelete&api-version=2019-10-10&", $restoreToken.Substring(1))

    $restoreHeaders = @{"x-ms-deleted-share-name" = $FileShareName; "x-ms-deleted-share-version" = $DeletedShareVersion}

    $restoreResponse = Invoke-WebRequest $restoreShareUrl -Headers $restoreHeaders -Method "PUT" -Verbose

    if ($restoreResponse.StatusCode -ne 201)
    {
        Write-Error "Request to restore a file share failed." -ErrorAction Stop
    }

    Write-Verbose $restoreResponse.RawContent -Verbose

    Write-Information -MessageData "Completed: Restore File Share" -InformationAction Continue
}

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName


Restore-DeletedFileShare $sa.Context $FileShareName $DeletedShareVersion
```

## <a name="how-to-use-the-script-in-different-scenarios"></a>Como usar o script em diferentes cenários

### <a name="prerequisites"></a>Pré-requisitos

1. Instale os mais recentes módulos Azure PowerShell Az a partir [deste link](/powershell/azure/install-az-ps) antes de executar o script.
2. Mantenha os seguintes detalhes à mão, pois terá de os passar como valores para diferentes parâmetros do script:

    * **-SubscriptionId** - ID da subscrição onde a partilha de ficheiros está presente.
    * **-Grupo de RecursosName** - Grupo de Recursos da Conta de Armazenamento que hospeda a parte do ficheiro.
    * **-StorageAccountName** - Nome da conta de armazenamento que alberga a parte do ficheiro.
    * **-FileShareName** - Nome da partilha de ficheiros a não ser desescolhido

### <a name="execution-steps"></a>Etapas de execução

1. Guarde o script acima na sua máquina com um nome à sua escolha. Neste exemplo, guardámo-lo como *Undelete.ps1*
2. Execute o script de acordo com o cenário que se adequa às suas necessidades.

#### <a name="scenario-1"></a>Cenário 1

Não existem várias versões apagadas com o mesmo nome que a partilha de ficheiros que está a tentar desempacotar.

O exemplo a seguir não desdeleta a parte de ações *de ficheiro1* presente na conta de armazenamento *afsshare*.

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

A saída deve mostrar a mensagem `Completed:Restore File Share`

#### <a name="scenario-2"></a>Cenário 2

Existem várias versões apagadas com o mesmo nome que o ficheiro que está a tentar desfazer-se.

O exemplo a seguir desativa uma versão da partilha de ações de *ficheiro1*

##### <a name="step-1"></a>Passo 1

Execute o script da seguinte forma, fornecendo o nome da partilha de ficheiros.

```PowerShell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

```Output
Completed: Search for a deleted file share with the specified name and Found versions
DeletedVersion: 01D5D7F77ACC7864, DeletedTime: Fri, 31 Jan 2020 05:30:33 GMT, RemainingRetentionDays: 14
DeletedVersion: 01D5D7F7A76CAF42, DeletedTime: Fri, 31 Jan 2020 05:31:25 GMT, RemainingRetentionDays: 14
Restore-DeletedFileShare : More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values.
```

##### <a name="step-2"></a>Passo 2

Escolha a versão a partir da saída do passo 1 que pretende desempacoá-la e passá-la como um valor para o parâmetro **-DeletedShareVersion.**

O exemplo a seguir não desativa a versão *01D5D7F7ACC7864* da partilha de *ficheiros.*

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare-StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1 -DeletedShareVersion 01D5D7F77ACC7864
```
