---
title: Adicione, remova e liste os utilizadores numa unidade administrativa - Azure Ative Directory / Microsoft Docs
description: Gerir os utilizadores e as suas permissões de função numa unidade administrativa no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a68295594b0153a7d062ae3dac34e6dcba5b04f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377964"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerir utilizadores numa unidade administrativa no Azure Ative Directory

No Azure Ative Directory (Azure AD), pode adicionar utilizadores a uma unidade administrativa (AU) para um âmbito administrativo de controlo mais granular.

Para obter os passos que se preparem para utilizar o PowerShell e o Microsoft Graph para a gestão da unidade administrativa, consulte [Get start](admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Adicionar utilizadores a uma AU

### <a name="azure-portal"></a>Portal do Azure

Pode atribuir os utilizadores a unidades administrativas individualmente ou numa operação a granel.

- Atribuição individual a partir de um perfil de utilizador

   1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com) com permissões de Administrador de Função Privilegiada.
   1. Selecione **Utilizadores** e selecione o utilizador a atribuir a uma unidade administrativa para abrir o perfil do utilizador.
   1. Selecione **unidades administrativas**. O utilizador pode ser atribuído a uma ou mais unidades administrativas selecionando **Atribuir à unidade administrativa** e selecionar as unidades administrativas onde o utilizador deve ser atribuído.

       ![selecione Adicionar e, em seguida, inserir um nome para a unidade administrativa](./media/admin-units-add-manage-users/assign-users-individually.png)

- Atribuição individual de uma unidade administrativa

   1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com) com permissões de Administrador de Função Privilegiada.
   1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa onde os utilizadores devem ser designados.
   1. Selecione **Todos os utilizadores** e, em seguida, selecione **Adicionar membro** para selecionar um ou mais utilizadores a atribuir à unidade administrativa a partir do painel de **membros Add.**

        ![selecione uma unidade administrativa e, em seguida, selecione Adicionar membro](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Atribuição a granel

   1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com) com permissões de Administrador de Função Privilegiada.
   1. Selecione **unidades administrativas**.
   1. Selecione a unidade administrativa onde os utilizadores devem ser adicionados.
   1. Abrir **Todos os utilizadores**  >  **Adicione membros do ficheiro .csv**. Em seguida, pode descarregar o modelo de valores separados por vírgula (CSV) e editar o ficheiro. O formato é simples e precisa de um único Nome Principal do Utilizador para ser adicionado em cada linha. Assim que o ficheiro estiver pronto, guarde-o num local apropriado e, em seguida, carrefique-o como parte deste passo.

    ![granel atribuir utilizadores a uma unidade administrativa](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

No exemplo acima, o cmdlet Add-AzureADAdministrativeUnitMember é utilizado para adicionar o utilizador à unidade administrativa. A identificação do objeto da unidade administrativa onde o utilizador deve ser adicionado e a identificação do objeto do utilizador a ser adicionado são tomadas como argumento. A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Exemplo:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Listar unidades administrativas para um utilizador

### <a name="azure-portal"></a>Portal do Azure

No portal Azure pode abrir o perfil de um utilizador:

1. Abertura de utilizadores **de anúncios Azure**  >  **Users**.

1. Selecione o utilizador para abrir o perfil do utilizador.

1. Selecione **unidades administrativas** para ver a lista de unidades administrativas onde o utilizador foi designado.

   ![Listar as unidades administrativas para um utilizador](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
Nota: Por predefinição, Get-AzureADAdministrativeUnitMember apenas devolve 100 membros, pode adicionar "-Todos $true" para recuperar mais membros.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Remova um único utilizador de uma AU

### <a name="azure-portal"></a>Portal do Azure

Há duas formas de remover um utilizador de uma unidade administrativa. No portal Azure pode abrir o perfil de um utilizador indo para Utilizadores **AD Azure**  >  **Users**. Selecione o utilizador para abrir o perfil do utilizador. Selecione a unidade administrativa de que pretende que o utilizador seja removido e selecione **Remover da unidade administrativa**.

![Remova um utilizador de uma unidade administrativa do perfil do utilizador](./media/admin-units-add-manage-users/user-remove-admin-units.png)

Também pode remover um utilizador nas unidades administrativas **Azure AD**  >  **Administrative units** selecionando a unidade administrativa de onde pretende remover os utilizadores. Selecione o utilizador e selecione **Remover o membro**.
  
![Remova um utilizador ao nível da unidade administrativa](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>A granel remove mais de um utilizador

Pode ir ao Azure AD > Unidades Administrativas e selecionar a unidade administrativa de onde pretende remover os utilizadores. Clique no membro de remoção a granel. Descarregue o modelo CSV para fornecer a lista de utilizadores a remover.

Edite o modelo CSV descarregado com as entradas de utilizador relevantes. Não remova as duas primeiras linhas do modelo. Adicione um utilizador UPN em cada linha.

![Editar o ficheiro CSV para remoção de utilizadores a granel das unidades administrativas](./media/admin-units-add-manage-users/bulk-user-entries.png)

Depois de ter guardado as entradas no ficheiro, faça o upload do ficheiro, **selecione Enviar por isso**.

![Envie o ficheiro de upload em massa](./media/admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a uma unidade administrativa](admin-units-assign-roles.md)
- [Adicionar grupos a uma unidade administrativa](admin-units-add-manage-groups.md)
