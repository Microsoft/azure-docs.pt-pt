---
title: Arquivos do SharePoint-QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicione fontes de dados do SharePoint protegidas à sua base de conhecimento para enriquecer a base de conhecimento com perguntas e respostas que podem ser protegidas com Active Directory.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: da73f42c17a3688e7f1f464ec4a3bbe77cbc9229
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955196"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Adicionar uma fonte de dados protegida do SharePoint à sua base de conhecimento

Adicione fontes de dados do SharePoint protegidas à sua base de conhecimento para enriquecer a base de conhecimento com perguntas e respostas que podem ser protegidas com Active Directory. 

Ao adicionar um documento protegido do SharePoint à sua base de dados de conhecimento, como o Gerenciador de QnA Maker, você deve solicitar a permissão Active Directory para QnA Maker. Depois que essa permissão for fornecida do Active Directory Manager para QnA Maker para acesso ao SharePoint, ela não precisará ser fornecida novamente. Cada adição de documento subsequente à base de dados de conhecimento não precisará de autorização se estiver no mesmo recurso do SharePoint. 

Se o QnA Maker Gerenciador da base de dados de conhecimento não for o Active Directory Manager, você precisará se comunicar com o Gerenciador de Active Directory para concluir esse processo.

## <a name="add-supported-file-types-to-knowledge-base"></a>Adicionar tipos de arquivo com suporte à base de dados de conhecimento

Você pode adicionar todos os tipos de [arquivo](../Concepts/data-sources-supported.md) com suporte QnA Maker de um site do SharePoint à sua base de dados de conhecimento. Talvez você precise conceder [permissões](#permissions) se o recurso de arquivo estiver protegido.

1. Na biblioteca com o site do SharePoint, selecione o menu de reticências do `...`arquivo,.
1. Copie a URL do arquivo.

   ![Obtenha a URL do arquivo do SharePoint selecionando o menu de reticências do arquivo e, em seguida, copiando a URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. No portal de QnA Maker, na página **configurações** , [adicione a URL](edit-knowledge-base.md#add-datasource) à base de dados de conhecimento. 

### <a name="images-with-sharepoint-files"></a>Imagens com arquivos do SharePoint

Se os arquivos incluírem imagens, elas não serão extraídas. Você pode adicionar a imagem, no portal de QnA Maker, depois que o arquivo é extraído em pares de QnA.

Adicione a imagem com a seguinte sintaxe de redução: 

```markdown
![Explanation or description of image](URL of public image)
```

O texto entre colchetes, `[]`, explica a imagem. A URL entre parênteses, `()`, é o link direto para a imagem. 

Quando você testa o par QnA no painel de teste interativo, no portal de QnA Maker, a imagem é exibida, em vez do texto de redução. Isso valida que a imagem pode ser recuperada publicamente do aplicativo cliente.

## <a name="permissions"></a>Permissões

A concessão de permissões ocorre quando um arquivo protegido de um servidor do SharePoint é adicionado a uma base de dados de conhecimento. Dependendo de como o SharePoint é configurado e as permissões da pessoa que adiciona o arquivo, isso pode exigir:

* nenhuma etapa adicional-a pessoa que está adicionando o arquivo tem todas as permissões necessárias.
* etapas tanto pelo [Gerenciador de base de conhecimento](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) quanto pelo [Gerenciador de Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Consulte as etapas listadas abaixo. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gerenciador da base de dados de conhecimento: Adicionar fonte de dados do SharePoint no portal QnA Maker

Quando o **QnA Maker Manager** adiciona um documento do SharePoint protegido a uma base de dados de conhecimento, o Gerenciador da base de dados de conhecimento inicia uma solicitação de permissão que o gerenciador de Active Directory precisa concluir.

A solicitação começa com um pop-up para autenticar em uma conta de Active Directory. 

![Autenticar conta de usuário](../media/add-sharepoint-datasources/authenticate-user-account.png)

Depois que o Gerenciador de QnA Maker selecionar a conta, o administrador do Azure Active Directory receberá um aviso de que precisa permitir que o aplicativo QnA Maker (não o QnA Maker Manager) acesse o recurso do SharePoint. O Gerenciador de Azure Active Directory precisará fazer isso para todos os recursos do SharePoint, mas nem todos os documentos desse recurso. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Gerenciador do Active Directory: conceder acesso de leitura de arquivo para QnA Maker

O Gerenciador de Active Directory (não o QnA Maker Manager) precisa conceder acesso ao QnA Maker para acessar o recurso do SharePoint, selecionando [este link](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) para autorizar o aplicativo do Portal do QnA Maker SharePoint Enterprise a ter permissões de leitura de arquivo. 

![Azure Active Directory Manager concede permissão interativamente](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Conceder acesso do centro de administração Azure Active Directory

1. O Active Directory Manager entra na portal do Azure e abre **[aplicativos empresariais](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** . 

1. Pesquise por `QnAMakerPortalSharePoint` selecionar o aplicativo QnA Maker. 

    [![Pesquisar QnAMakerPortalSharePoint na lista de aplicativos empresariais](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Em **segurança**, vá para **permissões**. Selecione **conceder consentimento de administrador para a organização**. 

    [![Selecionar usuário autenticado para Active Directory admin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecione uma conta de logon com permissões para conceder permissões para o Active Directory. 


  
<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker. 

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Colabore em sua base de dados de conhecimento](collaborate-knowledge-base.md)
