---
title: Exemplos do PowerShell para gerir grupos - Azure Active Directory | Documentos da Microsoft
description: Esta página fornece exemplos do PowerShell para o ajudar a gerir os seus grupos no Azure Active Directory
keywords: O Azure AD, Azure Active Directory, PowerShell, gestão de grupos, grupo
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7287616dbad1aa77a6e4aaa110ade39dcea4f195
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082624"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Cmdlets da versão 2 do Azure Active Directory para gestão de grupos

> [!div class="op_single_selector"]
> * [Portal do Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> * [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Este artigo contém exemplos de como utilizar o PowerShell para gerir os seus grupos no Azure Active Directory (Azure AD).  Ele também informa como mãos à obra com o módulo do PowerShell do Azure AD. Em primeiro lugar, deve [transferir o módulo do Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do PowerShell do Azure AD
Para instalar o módulo Azure AD PowerShell, utilize os seguintes comandos:

    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread

Para verificar se o módulo está pronto a utilizar, utilize o seguinte comando:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo do Azure AD, consulte a documentação de referência online [do Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Ligar o diretório
Antes de começar a gerir grupos com os cmdlets do PowerShell do Azure AD, tem de ligar a sessão do PowerShell para o diretório que pretende gerir. Utilize o seguinte comando:

    PS C:\Windows\system32> Connect-AzureAD

O cmdlet pede-lhe as credenciais que pretende utilizar para aceder ao seu diretório. Neste exemplo, estamos a utilizar karen@drumkit.onmicrosoft.com para aceder ao diretório de demonstração. O cmdlet devolve uma mensagem de confirmação para mostrar que a sessão foi ligada com êxito para o seu diretório:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Agora pode começar a utilizar os cmdlets do AzureAD para gerir grupos no seu diretório.

## <a name="retrieve-groups"></a>Obter os grupos
Para obter os grupos existentes do diretório, utilize o cmdlet Get-AzureADGroups. 

Para obter todos os grupos no diretório, utilize o cmdlet sem parâmetros:

    PS C:\Windows\system32> get-azureadgroup

O cmdlet devolve todos os grupos no diretório conectado.

Pode utilizar o parâmetro - objectID para recuperar um grupo específico para o qual especifique o objectID do grupo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Agora, o cmdlet retorna o grupo cujo objectID corresponde ao valor do parâmetro que introduziu:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Pode pesquisar por um grupo específico utilizando o parâmetro - filter. Este parâmetro assume uma cláusula de filtro ODATA e devolve todos os grupos que correspondem ao filtro, como no exemplo seguinte:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Cmdlets do Azure AD PowerShell implementam o padrão de consulta de OData. Para obter mais informações, consulte **$filter** na [opções de consulta de sistema de OData com o ponto final OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Criar grupos
Para criar um novo grupo no seu diretório, utilize o cmdlet New-AzureADGroup. Este cmdlet cria um novo grupo de segurança denominado "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Grupos de atualização
Para atualizar um grupo existente, utilize o cmdlet Set-AzureADGroup. Neste exemplo, estamos a alterar a propriedade DisplayName do grupo "Administradores do Intune." Em primeiro lugar, vamos está encontrando o grupo utilizando o cmdlet Get-AzureADGroup e filtrar usando o atributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Em seguida, estamos a alterar a propriedade de descrição para o novo valor "Administradores de dispositivos do Intune":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Agora, se encontrarmos o grupo novamente, podemos ver que a propriedade Description é atualizada para refletir o novo valor:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="delete-groups"></a>Eliminar grupos
Para eliminar grupos do seu diretório, utilize o cmdlet Remove-AzureADGroup da seguinte forma:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Gerir associação a um grupo 
### <a name="add-members"></a>Adicionar membros
Para adicionar novos membros a um grupo, utilize o cmdlet Add-AzureADGroupMember. Este comando adiciona um membro ao grupo administradores do Intune que utilizámos no exemplo anterior:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ObjectId é o ObjectID do grupo a que queremos adicionar um membro e - RefObjectId é o ObjectID do utilizador que queremos adicionar como membro ao grupo.

### <a name="get-members"></a>Obter membros
Para obter os membros existentes de um grupo, utilize o cmdlet Get-AzureADGroupMember, tal como neste exemplo:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Remover membros
Para remover o membro que adicionamos anteriormente ao grupo, utilize o cmdlet Remove-AzureADGroupMember, conforme é mostrado aqui:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Verifique se os membros
Para verificar as associações de grupo de um utilizador, utilize o cmdlet Select-AzureADGroupIdsUserIsMemberOf. Este cmdlet utiliza como seus parâmetros o ObjectId do utilizador para o qual pretende verificar as associações de grupo e uma lista de grupos para o qual pretende verificar as associações. A lista de grupos têm de ser fornecidos na forma de uma variável de complexo de tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", então, vamos primeiro tem de criar uma variável com esse tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Em seguida, podemos fornecer valores para o groupIds verificar o atributo "GroupIds" da variável complexos:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Agora, se quisermos verificar as associações de grupo de um utilizador com ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea contra os grupos no $g, devemos usar:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


O valor devolvido é uma lista de grupos dos quais este utilizador é membro. Também pode aplicar este método para verificar a associação de contatos, grupos ou principais de serviço para uma determinada lista de grupos, usando Select-AzureADGroupIdsContactIsMemberOf, selecione AzureADGroupIdsGroupIsMemberOf ou Selecione AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Desativar a criação do grupo pelos seus utilizadores
Pode impedir que usuários não-administradores criar grupos de segurança. O comportamento padrão no Microsoft Online Directory Services (MSODS) é permitir que usuários não-administradores criar grupos, independentemente da gestão de grupos self-service (SSGM) também está ativada. A definição de SSGM controla o comportamento apenas no painel de acesso das minhas aplicações. 

Para desativar a criação do grupo de usuários não-administradores:

1. Certifique-se de que os usuários não-administradores têm permissão para criar grupos:
   
   ```
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Se ele retorna `UsersPermissionToCreateGroupsEnabled : True`, em seguida, os usuários não-administradores podem criar grupos. Para desativar esta funcionalidade:
  
   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Gerir proprietários de grupos
Para adicionar proprietários a um grupo, utilize o cmdlet Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ObjectId é o ObjectID do grupo a que queremos adicionar um proprietário e - RefObjectId é o ObjectID do utilizador que pretende adicionar como proprietário do grupo.

Para obter os proprietários de um grupo, utilize o cmdlet Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

O cmdlet devolve a lista de proprietários para o grupo especificado:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Se pretender remover um proprietário de um grupo, utilize o cmdlet Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Aliases reservados 
Quando é criado um grupo, determinado pontos de extremidade permitem que o utilizador final especificar uma mailNickname ou um alias para ser utilizado como parte do endereço de e-mail do grupo. Só é possível criar grupos com os seguintes aliases de e-mail com privilégios elevados por um administrador global do Azure AD. 
  
* abuso 
* admin 
* administrador 
* hostmaster 
* majordomo 
* postmaster 
* raiz 
* proteger 
* security 
* SSL-administrador 
* webmaster 

## <a name="next-steps"></a>Passos Seguintes
Pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
