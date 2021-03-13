---
title: Sincronizar atributos ao AZure AD para mapeamento
description: Aprenda a sincronizar atributos do seu Ative Directory para Azure AD. Ao configurar o fornecimento de utilizadores para aplicações SaaS, utilize a funcionalidade de extensão do diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.author: kenwith
ms.openlocfilehash: 0f8369c80a7a219b159f31aacb7d10a0dd009d00
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418679"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sync um atributo do seu Ative Directy no local para Azure AD para provisionamento a uma aplicação

Ao personalizar mapeamentos de atributos para o fornecimento do utilizador, poderá descobrir que o atributo que pretende mapear não aparece na lista **de atributos Source.** Este artigo mostra-lhe como adicionar o atributo em falta sincronizando-o do seu Ative Directory (AD) no local ao Azure Ative Directory (Azure AD).

O Azure AD deve conter todos os dados necessários para criar um perfil de utilizador ao aprovisionar as contas dos utilizadores do Azure AD para uma aplicação SaaS. Em alguns casos, para disponibilizar os dados poderá necessitar de sincronização de atributos do seu AD no local para Azure AD. O Azure AD Connect sincroniza automaticamente determinados atributos ao AZure AD, mas nem todos os atributos. Além disso, alguns atributos (como o SAMAccountName) que são sincronizados por padrão podem não ser expostos usando a API do Microsoft Graph. Nestes casos, pode utilizar a função de extensão de diretório AD Connect Azure para sincronizar o atributo a Azure AD. Desta forma, o atributo será visível para a API do Microsoft Graph e para o serviço de fornecimento de AD Azure.

Se os dados necessários para o provisionamento estiverem no Ative Directory mas não estiverem disponíveis para provisões devido às razões acima descritas, pode utilizar o Azure AD Connect ou o PowerShell para criar atributos de extensão. 
 
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

## <a name="create-an-extension-attribute-using-powershell"></a>Criar um atributo de extensão usando PowerShell
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
