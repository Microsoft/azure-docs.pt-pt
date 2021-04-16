---
title: Sincronizar atributos ao Azure Ative Directory para mapeamento
description: Ao configurar o fornecimento de utilizadores com aplicações Azure Ative Directory e SaaS, utilize a funcionalidade de extensão de diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388215"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Sincronização de atributos de extensão para provisionamento de aplicações

O Azure Ative Directory (Azure AD) deve conter todos os dados (atributos) necessários para criar um perfil de utilizador ao aprovisionar as contas dos utilizadores do Azure AD para uma [aplicação SaaS](../saas-apps/tutorial-list.md). Ao personalizar mapeamentos de atributos para o fornecimento do utilizador, poderá encontrar o atributo que pretende mapear não consta na lista **de atributos Source.** Este artigo mostra-lhe como adicionar o atributo em falta.

Para utilizadores apenas em Azure AD, pode [criar extensões de esquema utilizando PowerShell ou Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

Para os utilizadores no Ative Directory, deve sincronizar os utilizadores com a Azure AD. Pode sincronizar utilizadores e atributos usando [o Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). O Azure AD Connect sincroniza automaticamente determinados atributos ao AZure AD, mas nem todos os atributos. Além disso, alguns atributos (como o SAMAccountName) que são sincronizados por padrão podem não ser expostos usando a AZure AD Graph API. Nestes casos, pode [utilizar a função de extensão de diretório AD Connect Azure para sincronizar o atributo a Azure AD](#create-an-extension-attribute-using-azure-ad-connect). Desta forma, o atributo será visível para o Azure AD Graph API e o serviço de fornecimento de AD Azure.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Crie um atributo de extensão num único utilizador de nuvem
Pode utilizar o Microsoft Graph e o PowerShell para estender o esquema do utilizador aos utilizadores em Azure AD. Estes atributos de extensão são automaticamente descobertos na maioria dos casos.

Quando tiver mais de 1000 diretores de serviço, poderá encontrar extensões em falta na lista de atributos de origem. Se um atributo que criou não aparecer automaticamente, verifique se o atributo foi criado e adicione-o manualmente ao seu esquema. Para verificar se foi criado, utilize o Microsoft Graph e [o Graph Explorer](/graph/graph-explorer/graph-explorer-overview). Para adicioná-lo manualmente ao seu esquema, consulte [editar a lista de atributos suportados](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Crie um atributo de extensão numa nuvem apenas utilizador usando o Microsoft Graph
Pode estender o esquema dos utilizadores AZure AD utilizando [o Microsoft Graph](/graph/overview). 

Primeiro, enuseça as aplicações no seu inquilino para obter a identificação da app em que está a trabalhar. Para saber mais, consulte [extensões de listasProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Em seguida, crie o atributo de extensão. Substitua a propriedade **ID** abaixo pelo **ID** recuperado no passo anterior. Terá de usar o atributo **"ID"** e não o "appId". Para saber mais, consulte [Criar extensãoProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&abas=http&preserve-view=true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

O pedido anterior criou um atributo de extensão com o formato `extension_appID_extensionName` . Agora pode atualizar um utilizador com este atributo de extensão. Para saber mais, consulte [o utilizador Update](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Finalmente, verifique o atributo para o utilizador. Para saber mais, consulte [um utilizador.](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Crie um atributo de extensão numa nuvem apenas utilizador usando PowerShell
Crie uma extensão personalizada utilizando o PowerShell e atribua um valor a um utilizador. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Criar um atributo de extensão usando Azure AD Connect

1. Abra o assistente Azure AD Connect, escolha Tarefas e, em seguida, escolha **Opções de sincronização personalizadas**.

   ![Página de tarefas adicionais do Azure Ative Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Inscreva-se como administrador global da Azure AD. 

3. Na página **Funcionalidades Opcionais,** selecione **sincronização de atributos de extensão do diretório**.
 
   ![Página de funcionalidades opcionais do assistente de ligação ativa do Azure](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione os atributos(s) que pretende estender para Azure AD.
   > [!NOTE]
   > A pesquisa em **Atributos Disponíveis** é sensível a casos.

   ![Screenshot que mostra a página de seleção "Extensões de Diretório"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Termine o assistente Azure AD Connect e permita que um ciclo completo de sincronização possa ser executado. Quando o ciclo estiver concluído, o esquema é estendido e os novos valores são sincronizados entre o AD no local e o AD AZure.
 
6. No portal Azure, enquanto [está a editar mapeamentos de atributos](customize-application-attributes.md)do utilizador, a lista de **atributos Fonte** irá agora conter o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)` . Selecione o atributo e mape-o para a aplicação-alvo para provisionamento.

   ![Página de seleção de extensões de extensões de diretório de assistente azure Ative Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> A capacidade de provisão de atributos de referência de AD no local, como **gerido por ou** **DN/DistinguishedName,** não é suportada hoje em dia. Pode solicitar esta funcionalidade no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 


## <a name="next-steps"></a>Passos seguintes

* [Definir quem está no âmbito do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
