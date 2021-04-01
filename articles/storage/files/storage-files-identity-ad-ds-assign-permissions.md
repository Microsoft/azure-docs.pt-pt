---
title: Controle o acesso às ações de ficheiros Azure - autenticação AD DS no local
description: Saiba como atribuir permissões a uma identidade de Serviços de Domínio de Diretório Ativo que represente a sua conta de armazenamento. Isto permite-lhe controlar o acesso com autenticação baseada na identidade.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91716042"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Parte dois: atribuir permissões de nível de partilha a uma identidade

Antes de iniciar este artigo, certifique-se de ter completado o artigo anterior, [Ativar a autenticação DS AD para a sua conta](storage-files-identity-ad-ds-enable.md).

Uma vez ativada a autenticação dos Serviços de Domínio do Diretório Ativo (DS AD) na sua conta de armazenamento, tem de configurar permissões de nível de partilha para ter acesso às suas ações de ficheiros. A identidade que pretende aceder aos recursos de partilha de ficheiros Azure deve ser uma identidade híbrida que existe tanto na AD DS como na Azure AD. Por exemplo, digamos que tem um utilizador no seu DS AD que é user1@onprem.contoso.com e que sincronizou com a Azure AD como user1@contoso.com usando a sincronização Azure AD Connect. Para permitir que este utilizador aceda a Ficheiros Azure, tem de atribuir as permissões de nível de partilha a user1@contoso.com . O mesmo conceito aplica-se a grupos ou princípios de serviço. Por isso, deve sincronizar os utilizadores e grupos do seu DS AD AD a Azure AD utilizando a sincronização Azure AD Connect. 

As permissões de nível de partilha devem ser atribuídas à identidade Azure AD que representa o mesmo utilizador ou grupo no seu DS AD para suportar a autenticação AD DS na sua partilha de ficheiros Azure. A autenticação e autorização contra identidades que só existem em Azure AD, como as Identidades Geridas Azure (MSIs), não são suportadas com a autenticação de DS AD. Este artigo demonstra como atribuir permissões de nível de partilha para uma partilha de ficheiros a uma identidade.


## <a name="share-level-permissions"></a>Permissões de nível de partilha

Geralmente, recomendamos a utilização de permissões de nível de partilha para uma gestão de acesso de alto nível a um grupo AD Azure que representa um grupo de utilizadores e identidades, aproveitando depois os ACLs do Windows para o controlo de acesso granular ao nível do diretório/ficheiro. 

Existem três funções integradas da Azure para a concessão de permissões de nível de partilha aos utilizadores:

- **O Storage File Data SMB Share Reader** permite o acesso de leitura em ações de ficheiros de armazenamento Azure sobre SMB.
- **O Storage File Data SMB Share Contributor** permite ler, escrever e eliminar o acesso em ações de ficheiros de armazenamento Azure em todo o SMB.
- **Dados de ficheiros de armazenamento SMB Share Contribuinte Elevado** permite ler, escrever, excluir e modificar ACLs do Windows em ações de ficheiros de armazenamento Azure em todo o SMB.

> [!IMPORTANT]
> O controlo administrativo total de uma parte de ficheiro, incluindo a capacidade de apropriar-se de um ficheiro, requer a utilização da chave da conta de armazenamento. O controlo administrativo não é suportado com credenciais Azure AD.

Pode utilizar o portal Azure, Azure PowerShell ou Azure CLI para atribuir as funções incorporadas à identidade AD AZure de um utilizador para a concessão de permissões de nível de partilha.

## <a name="assign-an-azure-role"></a>Atribuir um papel Azure

### <a name="azure-portal"></a>Portal do Azure

Para atribuir um papel de Azure a uma identidade AD Azure, utilizando o [portal Azure,](https://portal.azure.com)siga estes passos:

1. No portal Azure, vá à sua partilha de ficheiros ou [crie uma partilha de ficheiros](storage-how-to-create-file-share.md).
1. Selecione **Controlo de Acesso (IAM)**.
1. **Selecione Adicionar uma atribuição de função**
1. Na lâmina de atribuição de **funções Add,** selecione a função incorporada adequada (Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor) da lista **Role.** Deixar **Atribuir acesso à** definição predefinida: **utilizador, grupo ou principal de serviço Azure**. Selecione a identidade AD Azure alvo pelo nome ou endereço de e-mail. **A identidade AD AD selecionada deve ser uma identidade híbrida e não pode ser apenas uma identidade em nuvem.** Isto significa que a mesma identidade também está representada na DS AD.
1. **Selecione Guardar** para completar a operação de atribuição de funções.

### <a name="powershell"></a>PowerShell

A seguinte amostra powerShell mostra como atribuir um papel Azure a uma identidade AD Azure, com base no nome de inscrição. Para obter mais informações sobre a atribuição de funções Azure com PowerShell, consulte [adicionar ou remover atribuições de funções Azure utilizando o módulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Antes de executar o seguinte script de amostra, substitua os valores do espaço reservado, incluindo os parênteses, pelos seus valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
O seguinte comando CLI 2.0 atribui um papel Azure a uma identidade AD Azure, com base no nome de inscrição. Para obter mais informações sobre a atribuição de funções Azure com Azure CLI, consulte [adicionar ou remover atribuições de funções Azure utilizando o Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Antes de executar o seguinte script de amostra, lembre-se de substituir os valores do espaço reservado, incluindo os suportes, pelos seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Passos seguintes

Agora que atribuiu permissões de nível de partilha, tem de configurar o diretório e as permissões de nível de ficheiro. Continue para o próximo artigo.

[Parte três: configurar o diretório e as permissões de nível de ficheiro sobre o SMB](storage-files-identity-ad-ds-configure-permissions.md)
