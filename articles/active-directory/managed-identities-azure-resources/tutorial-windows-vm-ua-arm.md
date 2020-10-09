---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Azure Resource Manager - Windows - Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo utilizador de uma VM do Windows, para aceder ao Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d26c7f544c9754f455b67aadf9e923344cda3fdf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968683"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutorial: Utilize uma identidade gerida atribuída pelo utilizador num Windows VM para aceder ao Gestor de Recursos Azure

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

- [Inscreva-se no portal Azure](https://portal.azure.com)

- [Criar uma máquina virtual do Windows](../../virtual-machines/windows/quick-create-portal.md)

- Para executar os passos necessários de criação de recursos e gestão de funções neste tutorial, a sua conta precisa de permissões de "Proprietário" no âmbito adequado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de função, veja [Utilizar Controlo de Acesso Baseado em Funções para gerir o acesso aos recursos da sua subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).

- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Execute scripts localmente com Azure PowerShell, conforme descrito na secção seguinte.

### <a name="configure-azure-powershell-locally"></a>Configure Azure PowerShell localmente

Para utilizar a Azure PowerShell localmente para este artigo (em vez de utilizar a Cloud Shell), complete os seguintes passos:

1. Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

1. Inscreva-se em Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Poderá ter `Exit` de sair da sessão atual do PowerShell depois de executar este comando para o próximo passo.

1. Instale a versão `Az.ManagedServiceIdentity` pré-lançamento do módulo para executar as operações de identidade geridas atribuídas pelo utilizador neste artigo:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Ativar

Para um cenário que se baseie numa identidade atribuída ao utilizador, é necessário executar os seguintes passos:

- Criar uma identidade
- Atribuir a identidade recém-criada

### <a name="create-identity"></a>Criar identidade

Esta secção mostra como criar uma identidade atribuída ao utilizador. Uma identidade atribuída pelo utilizador é criada como um recurso autónomo do Azure. Utilizando a [Nova Entidade AzUserAssignedId,](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)a Azure cria uma identidade no seu inquilino AZure AD que pode ser atribuído a uma ou mais instâncias de serviço Azure.

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

### <a name="assign-identity"></a>Atribuir identidade

Esta secção mostra como atribuir a identidade atribuída ao utilizador a um VM do Windows. Uma identidade atribuída pelo utilizador pode ser utilizada pelos clientes em vários recursos do Azure. Utilize os comandos seguintes para atribuir a identidade atribuída pelo utilizador a uma única VM. Utilize a propriedade `Id` devolvida no passo anterior para o parâmetro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Conceder acesso 

Esta secção mostra como conceder o acesso de identidade atribuído ao seu utilizador a um Grupo de Recursos em Azure Resource Manager. As identidades geridas dos recursos do Azure disponibilizam identidades que podem ser utilizadas pelo seu código para pedir os tokens de acesso para autenticar para API de recurso que suportem a autenticação do Azure AD. Neste tutorial, o seu código irá aceder à API do Azure Resource Manager. 

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

## <a name="access-data"></a>Aceder a dados

### <a name="get-an-access-token"></a>Obter um token de acesso 

No resto do tutorial, vai trabalhar a partir da VM que criámos anteriormente.

1. Inscreva-se no portal Azure em [https://portal.azure.com](https://portal.azure.com)

2. No portal, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em **Descrição Geral**, clique em **Ligar**.

3. Introduza o **Nome de Utilizador** e a **Palavra-passe** que utilizou quando criou a VM do Windows.

4. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual, abra o **PowerShell** na sessão remota.

5. Através de `Invoke-WebRequest` do PowerShell, envie um pedido às identidades geridas locais para o ponto final dos recursos do Azure obter um token de acesso para o Azure Resource Manager.  O `client_id` valor é o valor devolvido quando criou a identidade gerida atribuída ao utilizador.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Ler propriedades

Utilize o token de acesso obtido no passo anterior para aceder ao Azure Resource Manager e ler as propriedades do Grupo de Recursos ao qual concedeu o seu acesso de identidade atribuída pelo utilizador. Substitua `<SUBSCRIPTION ID>` pelo ID da subscrição do seu ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A resposta contém as informações específicas do Grupo de Recursos, semelhantes ao seguinte exemplo:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma identidade atribuída ao utilizador e a anexá-la a uma Máquina Virtual Azure para aceder à API do Gestor de Recursos Azure.  Para saber mais sobre o Azure Resource Manager, veja:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
