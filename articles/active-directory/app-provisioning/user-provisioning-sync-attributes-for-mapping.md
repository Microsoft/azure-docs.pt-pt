---
title: Sincronizar atributos ao AZure AD para mapeamento
description: Ao configurar o fornecimento de utilizadores para aplicações SaaS, utilize a funcionalidade de extensão do diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579438"
---
# <a name="syncing-extension-attributes-attributes"></a>Atributos de extensão sincronizado

Ao personalizar mapeamentos de atributos para o fornecimento do utilizador, poderá descobrir que o atributo que pretende mapear não aparece na lista **de atributos Source.** Este artigo mostra-lhe como adicionar o atributo em falta sincronizando-o do seu Ative Directory (AD) no local ao Azure Ative Directory (Azure AD) ou criando os atributos de extensão em Ad Azure para um único utilizador em nuvem. 

O Azure AD deve conter todos os dados necessários para criar um perfil de utilizador ao aprovisionar as contas dos utilizadores do Azure AD para uma aplicação SaaS. Em alguns casos, para disponibilizar os dados poderá necessitar de sincronização de atributos do seu AD no local para Azure AD. O Azure AD Connect sincroniza automaticamente determinados atributos ao AZure AD, mas nem todos os atributos. Além disso, alguns atributos (como o SAMAccountName) que são sincronizados por padrão podem não ser expostos usando a AZure AD Graph API. Nestes casos, pode utilizar a função de extensão de diretório AD Connect Azure para sincronizar o atributo a Azure AD. Desta forma, o atributo será visível para o Azure AD Graph API e o serviço de fornecimento de AD Azure. Se os dados necessários para o provisionamento estiverem no Ative Directory mas não estiverem disponíveis para provisões devido às razões acima descritas, pode utilizar o Azure AD Connect para criar atributos de extensão. 

Embora a maioria dos utilizadores sejam utilizadores híbridos que são sincronizados a partir do Ative Directory, também pode criar extensões em utilizadores apenas na nuvem sem utilizar o Azure AD Connect. Utilizando o PowerShell ou o Microsoft Graph pode estender o esquema de um único utilizador em nuvem. 

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

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Crie um atributo de extensão num único utilizador de nuvem
Os clientes podem utilizar o Microsoft Graph e o PowerShell para estender o esquema do utilizador. Estes atributos de extensão são automaticamente descobertos na maioria dos casos, mas os clientes com mais de 1000 diretores de serviço podem encontrar extensões em falta na lista de atributos de origem. Se um atributo que criar utilizando os passos abaixo não aparecer automaticamente na lista de atributos de origem, verifique utilizando o gráfico de que o atributo de extensão foi criado com sucesso e, em seguida, adicione-o ao seu esquema [manualmente](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes). Ao fazer os pedidos de gráfico abaixo, clique em saber mais para verificar as permissões necessárias para escamar os pedidos. Pode utilizar [o explorador de gráficos](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) para fazer os pedidos. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Crie um atributo de extensão numa nuvem apenas utilizador usando o Microsoft Graph
Terá de utilizar uma aplicação para alargar o esquema dos seus utilizadores. Enumere as aplicações no seu inquilino para identificar o id da aplicação que gostaria de usar para estender o esquema do utilizador. [Saiba mais.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Crie o atributo de extensão. Substitua a propriedade **de id** abaixo pelo **id** recuperado no passo anterior. Terá de usar o atributo **"id"** e não o "appId". [Saiba mais.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
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

O pedido anterior criou um atributo de extensão com o formato "extension_appID_extensionName". Atualize um utilizador com o atributo de extensão. [Saiba mais.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Verifique o utilizador para se certificar de que o atributo foi atualizado com sucesso. [Saiba mais.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

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

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Passos seguintes

* [Definir quem está no âmbito do provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
