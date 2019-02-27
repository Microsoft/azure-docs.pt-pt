---
title: Utilizar uma identidade gerida atribuída pelo sistema de uma VM do Windows para aceder à Graph API do Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Windows para aceder à Graph API do Azure AD.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10b74b85235cc47375f6289b52371bc588105ad9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890101"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema de uma VM do Windows para aceder à Graph API do Azure AD

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como utilizar uma identidade gerida atribuída pelo sistema numa máquina virtual (VM) do Windows para aceder à Microsoft Graph API, de modo a obter as associações a grupos. As identidades geridas dos recursos do Azure são geridas automaticamente pelo Azure e permitem que se autentique perante serviços que suportem Autenticação do Azure AD sem que seja necessário inserir as credenciais no seu código.  Neste tutorial, irá consultar a associação da identidade da sua VM em grupos do Azure AD. As informações de grupo são frequentemente utilizadas em decisões de autorização, por exemplo. Nos bastidores, a identidade gerida da sua VM é representada por um **Principal de Serviço** no Azure AD. Antes de realizar a consulta de grupo, adicione o principal de serviço que representa a identidade da VM a um grupo no Azure AD. Para fazê-lo, pode utilizar o Azure PowerShell, o Azure AD PowerShell ou a CLI do Azure.

> [!div class="checklist"]
> * Ligar ao Azure AD
> * Adicionar a identidade da VM a um grupo no Azure AD 
> * Conceder acesso à identidade da VM ao Azure AD Graph 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Azure AD Graph

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Para conceder acesso à identidade da VM ao Azure AD Graph, tem de atribuir a função **Administrador Global** à conta no Azure AD.
- Instalar a versão mais recente [do Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) se ainda não o fez. 

## <a name="connect-to-azure-ad"></a>Ligar ao Azure AD

Tem de ligar ao Azure AD para atribuir a VM a um grupo, bem como conceder à VM permissão para obter as respetivas associações a grupos. Pode utilizar o cmdlet Connect-AzureAD diretamente ou com o parâmetro do TenantId no caso de ter vários inquilinos.

```powershell
Connect-AzureAD
```
OU
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Adicionar a identidade da VM a um grupo no Azure AD

Quando ativou a identidade gerida atribuída pelo sistema na VM do Windows, criou um principal de serviço no Azure AD.  Agora, tem de adicionar a VM a um grupo.  O exemplo seguinte cria um novo grupo no Azure AD e adiciona o principal de serviço da sua VM a esse grupo:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Conceder acesso à VM ao Graph API do Azure AD

Com as identidades geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação perante recursos que suportem a Autenticação do Azure AD. A API Graph do Microsoft Azure Active Directory suporta a autenticação do Azure AD. Neste passo, vai conceder acesso ao principal de serviço da identidade da VM ao Azure AD Graph, para que possa consultar as associações a grupos. É concedido acesso aos principais de serviços à Microsoft ou ao Azure AD Graph através das **Permissões de Aplicação**. O tipo de permissão de aplicação que tem de conceder depende da entidade à qual pretende aceder na MS ou no Azure AD Graph.

Neste tutorial, vai conceder à identidade da VM a capacidade de consultar associações a grupos com a permissão de aplicação ```Directory.Read.All```. Para conceder esta permissão, irá precisar de uma conta de utilizador que tenha atribuída a função de Administrador Global do Azure AD. Normalmente, concederia uma permissão de aplicação ao visitar o registo da sua aplicação no portal do Azure e adicionaria aí a permissão. No entanto, as identidades geridas de recursos do Azure não registam os objetos da aplicação no Azure AD, só são registados os principais de serviço. Para registar a permissão de aplicação vai utilizar a ferramenta de linha de comandos do Azure AD PowerShell. 

Azure AD Graph:
- AppId de Principal de serviço (utilizado quando a concessão de permissão da aplicação): 00000002-0000-0000-c000-000000000000
- ID do recurso (utilizado ao pedir o token de acesso de identidades geridas dos recursos do Azure): https://graph.windows.net
- Referência de âmbito de permissão: [Referência de permissões do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Conceder permissões de aplicação com o Azure AD PowerShell

Vai precisar do Azure AD PowerShell para utilizar esta opção. Se não o tiver instalado, [transfira a versão mais recente](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) antes de continuar.

1. Abra uma janela do PowerShell e ligue ao Azure AD:

   ```powershell
   Connect-AzureAD
   ```
   Para ligar a um específicos do Azure Active Directory, utilize o _TenantId_ parâmetro, da seguinte forma:

   ```PowerShell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Execute os seguintes comandos do PowerShell para atribuir a permissão de aplicação ``Directory.Read.All`` ao principal de serviço que representa a identidade da sua VM.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   A saída do comando final deve ser semelhante a esta, que devolve o ID da atribuição:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Se a chamada a `New-AzureAdServiceAppRoleAssignment` falhar com o erro `bad request, one or more properties are invalid`, a permissão de aplicação pode já estar atribuída ao principal de serviço da identidade da VM. Pode utilizar os seguintes comandos do PowerShell para verificar se a permissão de aplicação já existe entre a identidade da sua VM e o Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   Pode utilizar os seguintes comandos do PowerShell para listar todas as permissões de aplicação que foram concedidas ao Azure AD Graph:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   Pode utilizar os seguintes comandos do PowerShell para remover as permissões de aplicação que foram concedidas à identidade da sua VM para o Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Obter um token de acesso com a identidade da VM para chamar o Microsoft Azure AD Graph 

Para utilizar a identidade gerida atribuída pelo sistema da VM para a autenticação no Azure AD Graph, terá de fazer pedidos a partir da VM.

1. No portal, navegue para **Máquinas Virtuais**, aceda à sua VM do Windows e, no painel **Descrição Geral**, clique em **Ligar**.  
2. Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que utilizou quando criou a VM do Windows.
3. Agora que já criou uma Ligação ao Ambiente de Trabalho Remoto com a máquina virtual, abra o PowerShell na sessão remota.  
4. Com o comando Invoke-WebRequest do PowerShell, faça um pedido às identidades geridas locais para o ponto final dos recursos do Azure obter um token de acesso para o Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Converta a resposta de um objeto JSON num objeto do PowerShell.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraia o token de acesso da resposta.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Com o ID de Objeto do principal de serviço da identidade da VM (pode obter este valor a partir da variável declarada nos passos anteriores: ``$ManagedIdentitiesServicePrincipal.ObjectId``), pode consultar a Graph API do Azure AD para obter as respetivas associações a grupos. Substitua <OBJECT ID> pelo ID de Objeto do passo anterior e <ACCESS-TOKEN> pelo token de acesso obtido anteriormente:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   A resposta contém o `Object ID` do grupo ao qual adicionou o principal de serviço da VM nos passos anteriores.

   Resposta:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema da VM do Windows para aceder ao Azure AD Graph.  Para saber mais sobre o Azure AD Graph, veja:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
