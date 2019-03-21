---
title: Utilizar uma identidade gerida atribuída pelo utilizador da VM do Windows para aceder ao Azure Resource Manager
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo utilizador de uma VM do Windows, para aceder ao Azure Resource Manager.
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
ms.date: 04/10/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d44aaa96b03e188e7f502d5bf1dc1b0de48e4c4d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852326"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutorial: Utilizar uma identidade gerida atribuído ao utilizador numa VM do Windows, para aceder ao Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial explica como criar uma identidade atribuída pelo utilizador, atribuí-la a uma Máquina Virtual (VM) do Windows e, em seguida, utilizar essa identidade para aceder à API do Azure Resource Manager. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure. Permitem a autenticação em serviços que suportam a autenticação do Azure AD, sem ter de incorporar as credenciais no código. 

Saiba como:

> [!div class="checklist"]
> * Criar uma identidade gerida atribuída pelo utilizador
> * Atribuir a sua identidade atribuída pelo utilizador à sua VM do Windows
> * Conceder o acesso de identidade atribuída pelo utilizador a um Grupo de Recursos do Azure Resource Manager 
> * Obter um token de acesso com a identidade atribuída pelo utilizador e utilizá-lo para chamar o Azure Resource Manager 
> * Ler as propriedades de um Grupo de Recursos

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Iniciar sessão no portal do Azure](https://portal.azure.com)

- [Criar uma máquina virtual do Windows](/azure/virtual-machines/windows/quick-create-portal)

- Para executar os passos necessários de criação de recursos e gestão de funções neste tutorial, a sua conta precisa de permissões de "Proprietário" no âmbito adequado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de funções, veja [Utilizar o Controlo de Acesso Baseado em Funções para gerir o acesso aos recursos de subscrição do Azure](/azure/role-based-access-control/role-assignments-portal).
- [Instalar a versão mais recente do módulo Azure PowerShell](/powershell/azure/install-az-ps). 
- Execute `Connect-AzAccount` para criar uma ligação com o Azure.
- Instale a [versão mais recente do PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
- Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (poderá precisar de `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `Az.ManagedServiceIdentity`).
- Execute `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` para instalar a versão de pré-lançamento do módulo `Az.ManagedServiceIdentity` para executar operações de identidade atribuída pelo utilizador neste artigo.

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo utilizador

Uma identidade atribuída pelo utilizador é criada como um recurso autónomo do Azure. Utilizar o [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity), o Azure cria uma identidade no seu inquilino do Azure AD que pode ser atribuído a uma ou mais instâncias de serviço do Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

A resposta contém detalhes para a identidade atribuída pelo utilizador criada, semelhantes ao exemplo seguinte. Tenha em atenção os valores `Id` e `ClientId` da sua identidade atribuída pelo utilizador, uma vez que são utilizados nos passos seguintes:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Atribuir a identidade atribuída pelo utilizador a uma VM do Windows

Uma identidade atribuída pelo utilizador pode ser utilizada pelos clientes em vários recursos do Azure. Utilize os comandos seguintes para atribuir a identidade atribuída pelo utilizador a uma única VM. Utilize a propriedade `Id` devolvida no passo anterior para o parâmetro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder o acesso de identidade atribuída pelo utilizador a um Grupo de Recursos do Azure Resource Manager 

As identidades geridas dos recursos do Azure disponibilizam identidades que podem ser utilizadas pelo seu código para pedir os tokens de acesso para autenticar para API de recurso que suportem a autenticação do Azure AD. Neste tutorial, o seu código irá aceder à API do Azure Resource Manager. 

Antes de o seu código pode aceder à API, tem de conceder o acesso de identidade a um recurso no Azure Resource Manager. Neste caso, o Grupo de Recursos no qual a VM está contida. Atualize o valor de `<SUBSCRIPTION ID>`, conforme adequado para o seu ambiente.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

A resposta contém detalhes da atribuição de função criada, semelhante ao seguinte exemplo:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Resource Manager 

No resto do tutorial, vai trabalhar a partir da VM que criámos anteriormente.

1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

2. No portal, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em **Descrição Geral**, clique em **Ligar**.

3. Introduza o **Nome de Utilizador** e a **Palavra-passe** que utilizou quando criou a VM do Windows.

4. Agora que já criou uma **Ligação ao Ambiente de Trabalho Remoto** com a máquina virtual, abra o **PowerShell** na sessão remota.

5. Através de `Invoke-WebRequest` do PowerShell, envie um pedido às identidades geridas locais para o ponto final dos recursos do Azure obter um token de acesso para o Azure Resource Manager.  O valor `client_id` é o valor devolvido quando [criou a identidade gerida atribuída pelo utilizador](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Ler as propriedades de um Grupo de Recursos

Utilize o token de acesso obtido no passo anterior para aceder ao Azure Resource Manager e ler as propriedades do Grupo de Recursos ao qual concedeu o seu acesso de identidade atribuída pelo utilizador. Substitua <SUBSCRIPTION ID> pelo ID da subscrição do seu ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A resposta contém as informações específicas do Grupo de Recursos, semelhantes ao seguinte exemplo:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a criar uma identidade de utilizador atribuída e anexá-lo para uma Máquina Virtual para aceder à API do Azure Resource Manager.  Para saber mais sobre o Azure Resource Manager, veja:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
