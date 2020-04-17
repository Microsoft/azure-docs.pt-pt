---
title: Adicionar, remover e listar os utilizadores numa unidade administrativa (pré-visualização) - Diretório Ativo Azure [ Diretório Ativo Azure ] Microsoft Docs
description: Gerir os utilizadores e as suas permissões de funções numa unidade administrativa em Diretório Ativo Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b2b901f652564c47ca35cb0f75a69f26fa2fa71
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533229"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerir utilizadores numa unidade administrativa no Diretório Ativo do Azure

No Azure Ative Directory (Azure AD), pode adicionar utilizadores a uma unidade administrativa (UA) para um âmbito de controlo administrativo mais granular.

Para obter medidas para se preparar para utilizar powerShell e Microsoft Graph para gestão de unidades administrativas, consulte [Iniciar -](roles-admin-units-manage.md#getting-started)

## <a name="add-users-to-an-au"></a>Adicione utilizadores a uma UA

### <a name="azure-portal"></a>Portal do Azure

Pode atribuir utilizadores a unidades administrativas de duas formas.

1. Atribuição individual

    1. Pode ir ao Azure AD no portal e selecionar utilizadores e selecionar o utilizador para ser designado para uma unidade administrativa. Em seguida, pode selecionar unidades administrativas no painel esquerdo. O utilizador pode ser atribuído a uma ou mais unidades administrativas clicando em Atribuir à unidade administrativa e selecionando as unidades administrativas onde o utilizador deve ser atribuído.

       ![selecione Adicionar e, em seguida, introduzir um nome para a unidade administrativa](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Pode ir ao Azure AD no portal e selecionar unidades administrativas no painel esquerdo e, em seguida, selecionar a unidade administrativa onde os utilizadores serão atribuídos. Selecione Todos os utilizadores no painel esquerdo e, em seguida, selecione Adicionar membro. Em seguida, pode ir em frente e selecionar um ou mais utilizadores a serem atribuídos à unidade administrativa a partir do painel certo.

        ![selecione uma unidade administrativa e, em seguida, selecione Adicionar membro](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Atribuição a granel

    Vá ao Azure AD no portal e selecione Unidades Administrativas. Selecione a unidade administrativa onde os utilizadores devem ser adicionados. Proceda clicando em Todos os utilizadores -> Adicionar membros do ficheiro .csv. Em seguida, pode baixar o modelo CSV e editar o ficheiro. O formato é simples e precisa de uma única UPN para ser adicionada em cada linha. Uma vez que o ficheiro esteja pronto, guarde-o num local apropriado e, em seguida, carregue-o no passo 3, conforme realçado no instantâneo.

    ![atribuem a granel os utilizadores a uma unidade administrativa](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

No exemplo acima, o cmdlet Add-AzureADAdministrativeUnitMember é utilizado para adicionar o utilizador à unidade administrativa. O ID do objeto da unidade administrativa onde o utilizador deve ser adicionado e o ID do objeto que deve ser adicionado são tomados como argumento. A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Exemplo:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Lista ruminar unidades administrativas para um utilizador

### <a name="azure-portal"></a>Portal do Azure

No portal Azure pode abrir o perfil de um utilizador indo para o Azure AD > Utilizadores. Clique no utilizador para abrir o perfil do utilizador.

![Abra o perfil de um utilizador no Diretório Ativo Azure](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Selecione **unidades administrativas** no painel esquerdo para ver a lista das unidades administrativas onde o utilizador foi atribuído.

![Enumerar as unidades administrativas para um utilizador](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Remova um único utilizador de uma UA

### <a name="azure-portal"></a>Portal do Azure

Existem duas formas de remover um utilizador de uma unidade administrativa. No portal Azure pode abrir o perfil de um utilizador indo para > **utilizadores**de **Anúncios Azure**. Selecione o utilizador para abrir o perfil do utilizador. Selecione a unidade administrativa de que deseja que o utilizador seja removido e **selecione Remover da unidade administrativa**.

![Remova um utilizador de uma unidade administrativa do perfil do utilizador](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Também pode remover um utilizador nas > **unidades administrativas** **da AD Azure**selecionando a unidade administrativa de que pretende remover os utilizadores. Selecione o utilizador e selecione **Remover membro**.
  
![Remova um utilizador ao nível da unidade administrativa](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Remoção a granel mais do que um utilizador

Pode ir ao Azure AD > Unidades Administrativas e selecionar a unidade administrativa de que pretende remover os utilizadores. Clique em Granel remover membro. Descarregue o modelo CSV para fornecer a lista de utilizadores a remover.

Editar o modelo CSV descarregado com as entradas de utilizador relevantes. Não retire as duas primeiras linhas do modelo. Adicione um utilizador UPN em cada linha.

![Editar o ficheiro CSV para remoção de utilizadores a granel das unidades administrativas](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Depois de ter guardado as entradas no ficheiro, faça o upload do ficheiro, selecione **Enviar**.

![Enviar o ficheiro de upload a granel](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a uma unidade administrativa](roles-admin-units-assign-roles.md)
- [Adicionar grupos a uma unidade administrativa](roles-admin-units-add-manage-groups.md)
