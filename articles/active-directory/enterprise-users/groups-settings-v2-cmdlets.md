---
title: Exemplos powerShell V2 para gestão de grupos - Azure AD / Microsoft Docs
description: Esta página fornece exemplos powerShell para ajudá-lo a gerir os seus grupos no Azure Ative Directory
keywords: Azure AD, Diretório Ativo Azure, PowerShell, Grupos, Gestão de Grupos
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0cab755156cf7220b2303d90c43ba8e78df7c24
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548125"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Ative Directory versão 2 cmdlets para gestão de grupos

> [!div class="op_single_selector"]
> - [Portal do Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

Este artigo contém exemplos de como usar o PowerShell para gerir os seus grupos no Azure Ative Directory (Azure AD).  Também lhe diz como se configurar com o módulo Azure AD PowerShell. Em primeiro lugar, tem de [descarregar o módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instale o módulo Azure AD PowerShell

Para instalar o módulo Azure AD PowerShell, utilize os seguintes comandos:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Para verificar se o módulo está pronto a ser utilizado, utilize o seguinte comando:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo AD Azure, consulte a documentação de referência on-line para a versão 2 do [Azure Ative Directory PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

> [!NOTE]
> Os cmdlets Azure AD PowerShell não funcionam com o novo Powershell 7, uma vez que se baseia em .net Core. Estamos cientes e isso está em vias de ser atualizado. A partir de agora, sugerimos a utilização do Módulo Windows PowerShell 5.x para ser utilizado para operações de powershell AD Azure. 


## <a name="connect-to-the-directory"></a>Ligue-se ao diretório

Antes de começar a gerir grupos utilizando cmdlets Azure AD PowerShell, tem de ligar a sua sessão PowerShell ao diretório que pretende gerir. Utilize o seguinte comando:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

O cmdlet solicita-lhe as credenciais que pretende usar para aceder ao seu diretório. Neste exemplo, estamos a usar karen@drumkit.onmicrosoft.com para aceder ao diretório de demonstração. O cmdlet devolve uma confirmação para mostrar que a sessão foi conectada com sucesso ao seu diretório:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Agora pode começar a usar os cmdlets AzureAD para gerir grupos no seu diretório.

## <a name="retrieve-groups"></a>Grupos de recuperação

Para recuperar os grupos existentes do seu diretório, utilize o Get-AzureADGroups cmdlet. 

Para recuperar todos os grupos do diretório, utilize o cmdlet sem parâmetros:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

O cmdlet devolve todos os grupos no diretório ligado.

Pode utilizar o parâmetro -objectID para recuperar um grupo específico para o qual especifique o objectID do grupo:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

O cmdlet devolve agora o grupo cujo objectID corresponde ao valor do parâmetro introduzido:

```powershell
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
```

Pode procurar um grupo específico utilizando o parâmetro do filtro. Este parâmetro requer uma cláusula de filtro ODATA e devolve todos os grupos que correspondem ao filtro, como no exemplo seguinte:

```powershell
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
```

> [!NOTE]
> Os cmdlets Azure AD PowerShell implementam a norma de consulta OData. Para obter mais informações, consulte **$filter** nas [opções de consulta do sistema OData utilizando o ponto final OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter).

## <a name="create-groups"></a>Criar grupos

Para criar um novo grupo no seu diretório, use o New-AzureADGroup cmdlet. Este cmdlet cria um novo grupo de segurança chamado "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Grupos de atualização

Para atualizar um grupo existente, utilize o Set-AzureADGroup cmdlet. Neste exemplo, estamos a mudar a propriedade DisplayName do grupo "Administradores Intune". Primeiro, estamos a encontrar o grupo usando o cmdlet Get-AzureADGroup e o filtro usando o atributo DisplayName:

```powershell
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
```

Em seguida, estamos alterando a propriedade Descri para o novo valor "Administradores de dispositivos intune":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Agora, se encontrarmos o grupo novamente, vemos que a propriedade Description é atualizada para refletir o novo valor:

```powershell
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
```

## <a name="delete-groups"></a>Delete groups (Eliminar grupos)

Para eliminar grupos do seu diretório, utilize o cmdlet Remove-AzureADGroup da seguinte forma:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Gerir associação ao grupo

### <a name="add-members"></a>Adicionar membros

Para adicionar novos membros a um grupo, utilize o Add-AzureADGroupMember cmdlet. Este comando adiciona um membro ao grupo de Administradores Intune que usamos no exemplo anterior:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

O parâmetro ObjectId é o ObjectID do grupo ao qual queremos adicionar um membro, e o -RefObjectId é o ObjectID do utilizador que queremos adicionar como membro ao grupo.

### <a name="get-members"></a>Obter membros

Para obter os membros existentes de um grupo, use o Get-AzureADGroupMember cmdlet, como neste exemplo:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Remover membros

Para remover o membro que anteriormente adicionámos ao grupo, use o Remove-AzureADGroupMember cmdlet, como é mostrado aqui:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Verificar membros

Para verificar os membros do grupo de um utilizador, utilize o Select-AzureADGroupIdsUserIsMemberOf cmdlet. Este cmdlet toma como parâmetros o ObjectId do utilizador para o qual verificar os membros do grupo, e uma lista de grupos para os quais verificar os membros. A lista de grupos deve ser fornecida sob a forma de uma variável complexa do tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", pelo que primeiro temos de criar uma variável com esse tipo:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Em seguida, fornecemos valores para que os groupIds verifiquem no atributo "GroupIds" desta variável complexa:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Agora, se quisermos verificar as filiações em grupo de um utilizador com ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea contra os grupos em $g, devemos usar:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

O valor devolvido é uma lista de grupos dos quais este utilizador é membro. Também pode aplicar este método para verificar a adesão de Contactos, Grupos ou Principais de Serviço para uma determinada lista de grupos, utilizando Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf ou Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Desativar a criação de grupo pelos seus utilizadores

Pode evitar que os utilizadores não administrativos criem grupos de segurança. O comportamento padrão nos Serviços de Diretório Online da Microsoft (MSODS) é permitir que os utilizadores não administrativos criem grupos, quer a gestão do grupo de self-service (SSGM) também esteja ativada. A definição SSGM controla o comportamento apenas no painel de acesso my Apps.

Para desativar a criação de grupos para utilizadores não administrativos:

1. Verifique se os utilizadores não administrativos podem criar grupos:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Se retornar, `UsersPermissionToCreateGroupsEnabled : True` os utilizadores não administrativos podem criar grupos. Para desativar esta funcionalidade:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Gerir proprietários de grupos

Para adicionar os proprietários a um grupo, utilize o Add-AzureADGroupOwner cmdlet:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

O parâmetro ObjectId é o ObjectID do grupo ao qual queremos adicionar um proprietário, e o -RefObjectId é o ObjectID do utilizador ou principal de serviço que queremos adicionar como proprietário do grupo.

Para recuperar os proprietários de um grupo, utilize o Get-AzureADGroupOwner cmdlet:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

O cmdlet devolve a lista de proprietários (utilizadores e principais serviços) para o grupo especificado:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Se quiser retirar um proprietário de um grupo, utilize o Remove-AzureADGroupOwner cmdlet:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Pseudónimos reservados

Quando um grupo é criado, certos pontos finais permitem ao utilizador final especificar um nome ou pseudónimo por correio Para ser usado como parte do endereço de e-mail do grupo. Grupos com os seguintes pseudónimos de e-mail altamente privilegiados só podem ser criados por um administrador global Azure AD. 
  
* abuso
* administração
* administrador
* hostmaster
* majordomo
* chefe dos correios
* raiz
* seguro
* security
* ssl-administrador
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Recuo do grupo para as instalações (pré-visualização)

Hoje, muitos grupos ainda são geridos no Ative Directory. Para responder a pedidos de sincronização de grupos de nuvem de volta ao local, a funcionalidade de writeback de grupos microsoft 365 para Azure AD já está disponível para pré-visualização.

Os grupos Microsoft 365 são criados e geridos na nuvem. A capacidade de writeback permite-lhe escrever de volta os grupos Microsoft 365 como grupos de distribuição para uma floresta de Diretório Ativo com o Exchange instalado. Os utilizadores com caixas de correio no local podem então enviar e receber e-mails destes grupos. A funcionalidade de writeback do grupo não suporta grupos de segurança Azure AD ou grupos de distribuição.

Para mais detalhes, consulte a documentação para o [serviço de sincronização Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

A writeback do grupo Microsoft 365 é uma funcionalidade de pré-visualização pública do Azure Ative Directory (Azure AD) e está disponível com qualquer plano de licença AZure AD pago. Para obter algumas informações legais sobre pré-visualizações, consulte [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Passos seguintes

Pode encontrar mais documentação do Azure Ative Directory PowerShell na [Azure Ative Directory Cmdlets](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)