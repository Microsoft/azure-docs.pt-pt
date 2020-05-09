---
title: PowerShell V2 exemplos para gestão de grupos - Azure AD [ Microsoft Docs
description: Esta página fornece exemplos powerShell para ajudá-lo a gerir os seus grupos no Diretório Ativo Azure
keywords: Azure AD, Azure Ative Directory, PowerShell, Grupos, Gestão do Grupo
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc12e571ca955a0ddc47e1c1dd73c2717161df4b
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739318"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Ative Directory versão 2 cmdlets para gestão de grupo

> [!div class="op_single_selector"]
> - [Portal do Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Este artigo contém exemplos de como usar o PowerShell para gerir os seus grupos no Azure Ative Directory (Azure AD).  Também lhe diz como se configurar com o módulo PowerShell Azure AD. Primeiro, tem de descarregar o módulo PowerShell Da [AD Azure](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instale o módulo PowerShell Da AD Azure

Para instalar o módulo PowerShell Azure AD, utilize os seguintes comandos:

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

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo AD Azure, consulte a documentação de referência on-line para a Versão 2 do [Diretório Ativo Azure PowerShell 2](/powershell/azure/install-adv2?view=azureadps-2.0).

> [!NOTE]
> Os cmdlets Azure AD PowerShell não funcionam com o novo Powershell 7, uma vez que se baseia em .net Core. Estamos cientes e isso está em vias de ser atualizado. A partir de agora, sugerimos utilizar o Módulo Powershell 5.x Windows powershell para ser utilizado para operações de powershell Azure AD. 


## <a name="connect-to-the-directory"></a>Ligue-se ao diretório

Antes de poder começar a gerir grupos utilizando cmdlets Azure AD PowerShell, tem de ligar a sua sessão PowerShell ao diretório que pretende gerir. Utilize o seguinte comando:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

O cmdlet solicita-lhe as credenciais que pretende utilizar para aceder ao seu diretório. Neste exemplo, estamos karen@drumkit.onmicrosoft.com a utilizar para aceder ao diretório de demonstração. O cmdlet devolve uma confirmação para mostrar que a sessão foi ligada com sucesso ao seu diretório:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Agora pode começar a usar os cmdlets AzureAD para gerir grupos no seu diretório.

## <a name="retrieve-groups"></a>Recuperar grupos

Para recuperar os grupos existentes do seu diretório, utilize o cmdlet Get-AzureADGroups. 

Para recuperar todos os grupos do diretório, utilize o cmdlet sem parâmetros:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

O cmdlet devolve todos os grupos no diretório conectado.

Pode utilizar o parâmetro -objectID para recuperar um grupo específico para o qual especifica o objectID do grupo:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

O cmdlet devolve agora o grupo cujo objectid corresponde ao valor do parâmetro em que entrou:

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

Pode procurar um grupo específico utilizando o parâmetro do filtro. Este parâmetro pega numa cláusula de filtro ODATA e devolve todos os grupos que correspondem ao filtro, como no exemplo seguinte:

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
> Os cmdlets De PowerShell da AD Azure implementam a norma de consulta OData. Para mais informações, consulte **$filter** opções de consulta do [sistema OData utilizando o ponto final do OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Criar grupos

Para criar um novo grupo no seu diretório, utilize o cmdlet New-AzureADGroup. Este cmdlet cria um novo grupo de segurança chamado "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Atualizar grupos

Para atualizar um grupo existente, utilize o cmdlet Set-AzureADGroup. Neste exemplo, estamos a mudar a propriedade displayName do grupo "Administradores Intune". Primeiro, estamos a encontrar o grupo usando o cmdlet get-AzureADGroup e o filtro usando o atributo DisplayName:

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

Em seguida, estamos a mudar a propriedade Descrição para o novo valor "Intune Device Administrators":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Agora, se encontrarmos o grupo novamente, vemos que a propriedade Descrição é atualizada para refletir o novo valor:

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

Para adicionar novos membros a um grupo, utilize o cmdlet Add-AzureADGroupMember. Este comando adiciona um membro ao grupo intune Administrators que usamos no exemplo anterior:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

O parâmetro -ObjectId é o ObjectID do grupo ao qual queremos adicionar um membro, e o -RefObjectId é o ObjectID do utilizador que queremos adicionar como membro ao grupo.

### <a name="get-members"></a>Obter membros

Para obter os membros existentes de um grupo, utilize o Cmdlet Get-AzureADGroupMember, como neste exemplo:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Remover membros

Para remover o membro que adicionámos anteriormente ao grupo, utilize o cmdlet Remove-AzureADGroupMember, como é mostrado aqui:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Verificar membros

Para verificar os membros do grupo de um utilizador, utilize o Select-AzureADGroupIdsUserIsMemberOf cmdlet. Este cmdlet toma como parâmetros o ObjectId do utilizador para saber os membros do grupo, e uma lista de grupos para os quais verificar os membros. A lista de grupos deve ser fornecida sob a forma de uma variável complexa de tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", pelo que primeiro devemos criar uma variável com esse tipo:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Em seguida, fornecemos valores para que os groupIds verifiquem o atributo "GroupIds" desta variável complexa:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Agora, se quisermos verificar os membros do grupo de um utilizador com objectid 72cd4bbd-2594-40a2-935c-016f3cfeeeea contra os grupos em $g, devemos usar:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

O valor devolvido é uma lista de grupos dos quais este utilizador é membro. Também pode aplicar este método para verificar a adesão de Contactos, Grupos ou Principados de Serviços para uma determinada lista de grupos, utilizando Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf ou Select-AzureADGroupIdsServicePrincipaIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Desativar a criação de grupo pelos seus utilizadores

Pode impedir que os utilizadores não administradores criem grupos de segurança. O comportamento padrão nos Serviços de Diretórios Online da Microsoft (MSODS) é permitir que utilizadores não administradores criem grupos, quer a gestão de grupos de autosserviço (SSGM) também esteja ativada. A definição de SSGM controla o comportamento apenas no painel de acesso my apps.

Para desativar a criação de grupo para utilizadores não administradores:

1. Verifique se os utilizadores não administradores estão autorizados a criar grupos:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Se regressar, `UsersPermissionToCreateGroupsEnabled : True`os utilizadores não administradores podem criar grupos. Para desativar esta funcionalidade:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Gerir proprietários de grupos

Para adicionar os proprietários a um grupo, utilize o cmdlet Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

O parâmetro -ObjectId é o ObjectID do grupo ao qual queremos adicionar um proprietário, e o -RefObjectId é o ObjectID do utilizador ou diretor de serviço que queremos adicionar como proprietário do grupo.

Para recuperar os proprietários de um grupo, utilize o Cmdlet Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

O cmdlet devolve a lista de proprietários (utilizadores e diretores de serviço) para o grupo especificado:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Se pretender remover um proprietário de um grupo, utilize o cmdlet Remove-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Pseudónimos reservados

Quando um grupo é criado, certos pontos finais permitem ao utilizador final especificar um e-mailApelido ou pseudónimo para ser usado como parte do endereço de e-mail do grupo.Grupos com os seguintes pseudónimos de e-mail altamente privilegiados só podem ser criados por um administrador global da AD Azure. 
  
* abuso
* admin
* administrador
* hostmaster
* majordomo
* chefe dos correios
* raiz
* seguro
* security
* ssl-admin
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Redação do grupo para as instalações (pré-visualização)

Hoje em dia, muitos grupos ainda são geridos no local Ative Directory. Para responder aos pedidos de sincronização de grupos de nuvem de volta ao local, o Office 365 groups writeback feature for Azure AD está agora disponível para pré-visualização.

O Office 365 grupos é criado e gerido na nuvem. A capacidade de redação permite-lhe reescrever os grupos Office 365 como grupos de distribuição para uma floresta de Diretório Ativo com O Exchange instalado. Os utilizadores com caixas de correio no local Podem então enviar e receber e-mails destes grupos. A funcionalidade de redação de grupo não suporta grupos de segurança da AD Azure ou grupos de distribuição.

Para mais detalhes, consulte a documentação para o serviço de [sincronização Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

O Office 365 group writeback é uma funcionalidade de pré-visualização pública do Azure Ative Directory (Azure AD) e está disponível com qualquer plano de licença Azure AD pago. Para obter algumas informações legais sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="next-steps"></a>Passos seguintes

Pode encontrar mais documentação do Diretório Ativo Azure PowerShell na [Azure Ative Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
