---
title: Gerir a CDN do Azure com o PowerShell | Documentos da Microsoft
description: Saiba como utilizar cmdlets do Azure PowerShell para gerir a CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 0ad3d1693e2dbf1c4f5329ec23265ea1b3469e2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66125448"
---
# <a name="manage-azure-cdn-with-powershell"></a>Gerir a CDN do Azure com o PowerShell
PowerShell fornece um dos métodos mais flexíveis para gerir os seus perfis de CDN do Azure e pontos de extremidade.  Pode utilizar o PowerShell interativamente ou ao escrever scripts para automatizar tarefas de gestão.  Este tutorial demonstra várias das tarefas mais comuns que pode realizar com o PowerShell para gerir os seus perfis de CDN do Azure e pontos de extremidade.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para utilizar o PowerShell para gerir os seus perfis de CDN do Azure e os pontos finais, tem de ter o módulo Azure PowerShell instalado.  Para saber como instalar o Azure PowerShell e ligue-se para o Azure com o `Connect-AzAccount` cmdlet, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Tem de iniciar sessão com `Connect-AzAccount` antes de pode executar cmdlets do PowerShell do Azure.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Listar os cmdlets do CDN do Azure
Pode listar todos os cmdlets de CDN do Azure com o `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Obter ajuda
Pode obter ajuda com qualquer um destes cmdlets com a `Get-Help` cmdlet.  `Get-Help` fornece sintaxe e de utilização e, opcionalmente, mostra exemplos.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Lista de perfis de CDN do Azure existentes
O `Get-AzCdnProfile` cmdlet sem quaisquer parâmetros obtém todos os seus perfis CDN existentes.

```powershell
Get-AzCdnProfile
```

Este resultado pode ser enviada por pipe para cmdlets de enumeração.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Também podem retornar um único perfil ao especificar o grupo de recursos e nome de perfil.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> É possível ter vários perfis da CDN com o mesmo nome, desde que eles estão em grupos de recursos diferentes.  Omitindo o `ResourceGroupName` parâmetro retorna todos os perfis com um nome correspondente.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Lista de pontos finais CDN existentes
`Get-AzCdnEndpoint` Pode obter um ponto de extremidade individual ou de todos os pontos finais num perfil.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Criação de perfis da CDN e os pontos finais
`New-AzCdnProfile` e `New-AzCdnEndpoint` são utilizados para criar perfis de CDN e os pontos finais. São suportados os SKUs seguintes:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>A verificar a disponibilidade de nome de ponto final
`Get-AzCdnEndpointNameAvailability` Devolve um objeto que indica se um nome de ponto final está disponível.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Adicionar um domínio personalizado
`New-AzCdnCustomDomain` Adiciona um nome de domínio personalizado para um ponto final existente.

> [!IMPORTANT]
> Tem de configurar o CNAME com o fornecedor DNS conforme descrito em [como mapear um domínio personalizado ao ponto final de rede de entrega de conteúdos (CDN)](cdn-map-content-to-custom-domain.md).  Pode testar o mapeamento antes de modificar seu ponto final com `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modificar um ponto final
`Set-AzCdnEndpoint` Modifica um ponto final existente.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Remover/Pre-loading CDN ativos
`Unpublish-AzCdnEndpointContent` limpezas de cache ativos, enquanto `Publish-AzCdnEndpointContent` pré-carregar ativos em pontos finais suportados.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Pontos finais da CDN do iniciar/parar
`Start-AzCdnEndpoint` e `Stop-AzCdnEndpoint` pode ser utilizado para iniciar e parar pontos finais individuais ou grupos de pontos de extremidade.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Eliminar recursos CDN
`Remove-AzCdnProfile` e `Remove-AzCdnEndpoint` pode ser utilizado para remover perfis e pontos finais.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Próximos Passos
Saiba como automatizar o CDN do Azure com [.NET](cdn-app-dev-net.md) ou [node. js](cdn-app-dev-node.md).

Para saber mais sobre as funcionalidades CDN, veja [descrição geral da CDN](cdn-overview.md).

