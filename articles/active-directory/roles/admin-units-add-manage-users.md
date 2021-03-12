---
title: Adicione, remova e liste os utilizadores numa unidade administrativa - Azure Ative Directory | Microsoft Docs
description: Gerir os utilizadores e as suas permissões de função numa unidade administrativa no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a9d80344a31023d174935e7f785e36102e99eba
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011560"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerir utilizadores numa unidade administrativa no Azure Ative Directory

No Azure Ative Directory (Azure AD), pode adicionar utilizadores a uma unidade administrativa para um âmbito administrativo de controlo mais granular.

Para preparar a utilização do PowerShell e do Microsoft Graph para a gestão da unidade administrativa, consulte [Começar](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Adicionar utilizadores a uma unidade administrativa

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Pode atribuir os utilizadores a unidades administrativas individualmente ou como uma operação a granel.

- Atribuir utilizadores individuais a partir de um perfil de utilizador:

   1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com) com permissões de Administrador de Função Privilegiada.

   1. Selecione **Utilizadores** e, em seguida, para abrir o perfil do utilizador, selecione o utilizador a atribuir a uma unidade administrativa.
   
   1. Selecione **unidades administrativas**. 
   
   1. Para atribuir o utilizador a uma ou mais unidades administrativas, selecione **Atribua à unidade administrativa** e, em seguida, no painel direito, selecione as unidades administrativas às quais pretende atribuir o utilizador.

       ![Screenshot do painel "Unidades Administrativas" para atribuir um utilizador a uma unidade administrativa.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Atribuir utilizadores individuais a partir de uma unidade administrativa:

   1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com) com permissões de Administrador de Função Privilegiada.
   1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa onde o utilizador deve ser designado.
   1. Selecione **Todos os utilizadores**, selecione **Adicionar membro** e, em seguida, no painel **de membros Adicionar,** selecione um ou mais utilizadores que pretende atribuir à unidade administrativa.

        ![Screenshot do painel da unidade administrativa "Utilizadores" para atribuir um utilizador a uma unidade administrativa.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Atribuir os utilizadores como uma operação a granel:

   1. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com) com permissões de Administrador de Função Privilegiada.

   1. Selecione **unidades administrativas**.

   1. Selecione a unidade administrativa à qual pretende adicionar utilizadores.

   1. Selecione **Utilizadores**  >  **A granel** adicionar  >  **membros**. Em seguida, pode descarregar o modelo de valores separados por vírgula (CSV) e editar o ficheiro. O formato é simples e precisa de um único nome principal de utilizador para ser adicionado em cada linha. Depois de o ficheiro estar pronto, guarde-o para um local apropriado e, em seguida, carrefique-o como parte deste passo.

      ![Screenshot do painel "Utilizadores" para atribuir utilizadores a uma unidade administrativa como uma operação a granel.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

No PowerShell, utilize o `Add-AzureADAdministrativeUnitMember` cmdlet no exemplo seguinte para adicionar o utilizador à unidade administrativa. O ID do objeto da unidade administrativa à qual pretende adicionar o utilizador e o ID do objeto do utilizador que pretende adicionar são tomados como argumentos. Altere a secção realçada conforme necessário para o seu ambiente específico.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.ObjectId -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Substitua o espaço reservado por informações de teste e execute o seguinte comando:

Pedir

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Corpo

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Exemplo

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Ver uma lista de unidades administrativas para um utilizador

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

No portal Azure, pode abrir o perfil de um utilizador fazendo o seguinte:

1. Vá ao **Azure AD** e, em seguida, selecione **Utilizadores**.

1. Selecione o utilizador cujo perfil pretende visualizar.

1. Selecione **unidades administrativas** para apresentar a lista de unidades administrativas às quais o utilizador foi designado.

   ![Screenshot das unidades administrativas às quais foi atribuído um utilizador.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

Execute o seguinte comando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Por defeito, `Get-AzureADAdministrativeUnitMember` devolve apenas 100 membros de uma unidade administrativa. Para recuperar mais membros, pode adicionar `"-All $true"` .

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Substitua o espaço reservado por informações de teste e execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Remover um único utilizador de uma unidade administrativa

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Pode remover um utilizador de uma unidade administrativa de duas formas: 

* No portal Azure, vá ao **Azure AD** e, em seguida, selecione **Utilizadores**. 
  1. Selecione o utilizador para abrir o perfil do utilizador. 
  1. Selecione a unidade administrativa de onde pretende remover o utilizador e, em seguida, selecione **Remover da unidade administrativa**.

     ![Screenshot mostrando como remover um utilizador de uma unidade administrativa do painel de perfil do utilizador.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* No portal Azure, vá ao **Azure AD,** e depois selecione **unidades administrativas**.
  1. Selecione a unidade administrativa de onde pretende remover o utilizador. 
  1. Selecione o utilizador e, em seguida, **selecione Remover o membro**.
  
     ![Screenshot mostrando como remover um utilizador ao nível da unidade administrativa.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

Execute o seguinte comando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Substitua os espaços reservados por informações de teste e execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Remova vários utilizadores como uma operação a granel

Para remover vários utilizadores de uma unidade administrativa, faça o seguinte:

1. No portal Azure, vá ao **Azure AD.**

1. Selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa daí retirada dos utilizadores. 

1. **Selecione a granel remover membros** e, em seguida, descarregue o modelo CSV que utilizará para listar os utilizadores que pretende remover.

   ![Screenshot mostrando a ligação "A granel remove membros" no painel "Utilizadores".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Edite o modelo CSV descarregado com as entradas de utilizador relevantes. Não remova as duas primeiras linhas do modelo. Adicione um nome principal do utilizador (UPN) em cada linha.

   ![Screenshot de um ficheiro CSV editado para remover utilizadores de uma unidade administrativa a granel.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Guarde as alterações, faça o upload do ficheiro e, em seguida, **selecione Enviar por isso**.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a uma unidade administrativa](admin-units-assign-roles.md)
- [Adicionar grupos a uma unidade administrativa](admin-units-add-manage-groups.md)
