---
title: Arquivos do SharePoint - QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicione origens de dados protegidas do Sharepoint à sua base de dados de conhecimento para enriquecer a base de dados de conhecimento com perguntas e respostas que podem ser protegidas com o Active Directory.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 3e5aa1cc78efeb6e8158155b5e0676c8a63cf6e6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447556"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Adicionar uma origem de dados protegida do Sharepoint à sua base de dados de conhecimento

Adicione origens de dados protegidas do Sharepoint à sua base de dados de conhecimento para enriquecer a base de dados de conhecimento com perguntas e respostas que podem ser protegidas com o Active Directory. 

Quando adiciona um documento protegido do Sharepoint à sua base de dados de conhecimento, como o Gestor do QnA Maker, tem de solicitar permissão do Active Directory para o QnA Maker. Depois desta permissão é concedida do Gestor de Active Directory a ferramenta QnA Maker para acesso ao Sharepoint, não tem de ser dada novamente. Cada adição de documento subsequentes para a base de dados de conhecimento não terá autorização, se está a ser o mesmo recurso do Sharepoint. 

Se o Gestor de base de dados de conhecimento do QnA Maker não é o gerente do Active Directory, terá de se comunicar com o Gestor do Active Directory para concluir este processo.

## <a name="add-supported-file-types-to-knowledge-base"></a>Adicionar tipos de ficheiro suportados para a base de dados de conhecimento

Pode adicionar tudo suportado do QnA Maker [tipos de ficheiro](../Concepts/data-sources-supported.md) de um servidor Sharepoint para a sua base de dados de conhecimento. Poderá ter de conceder [permissões](#permissions) se o recurso do ficheiro está protegido.

1. A partir do servidor do Sharepoint, selecione o menu de reticências do ficheiro, `...`.
1. Copie o URL do ficheiro.

    ![Obter o URL de ficheiros do Sharepoint ao selecionar o menu de reticências do ficheiro, em seguida, copiar o URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. No portal do QnA Maker, sobre o **definições** página, [adicionar o URL](edit-knowledge-base.md#add-datasource) para a base de dados de conhecimento. 

### <a name="images-with-sharepoint-files"></a>Imagens com arquivos do Sharepoint

Se os ficheiros incluem imagens, aqueles não são extraídos. Pode adicionar a imagem, no portal do QnA Maker, depois do ficheiro é extraído em pares de QnA.

Adicione a imagem com a seguinte sintaxe de markdown: 

```markdown
![Explanation or description of image](URL of public image)
```

O texto exibido entre colchetes, `[]`, explica a imagem. O URL em parênteses, `()`, é a ligação direta para a imagem. 

Quando testa o par de QnA no painel de teste interativa, no portal do QnA Maker, é apresentada na imagem, em vez do texto de markdown. Isso valida a imagem pode ser obtido ao público da sua aplicação de cliente.

## <a name="permissions"></a>Permissões

Conceder permissões acontece quando um ficheiro protegido do servidor do Sharepoint é adicionado a uma base de dados de conhecimento. Dependendo de como o Sharepoint é definido de segurança e as permissões da pessoa a adicionar o arquivo, isso poderia exigir:

* Não existem passos adicionais - a pessoa a adicionar o ficheiro tem todas as permissões necessárias.
* passos por ambos [Gestor de base de dados de conhecimento](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) e [Gestor de diretório Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Veja os passos listados abaixo. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gestor de base de dados de conhecimento: Adicionar origem de dados do Sharepoint no portal do QnA Maker

Quando o **manager do QnA Maker** adiciona um documento protegido do Sharepoint para uma base de dados de conhecimento, a inicia do Gestor de base de dados de conhecimento um pedido de permissão que o Gestor de diretório Active Directory tem de concluir.

A solicitação começa com um pop-up para se autenticar numa conta do Active Directory. 

![Autenticar a conta de utilizador](../media/add-sharepoint-datasources/authenticate-user-account.png)

Depois do Gestor do QnA Maker seleciona a conta, o administrador do Active Directory irá receber um aviso a indicar que têm de permitir que o QnA Maker acesso da aplicação (e não o Gestor do QnA Maker) para o recurso do Sharepoint. O Gestor de diretório Active Directory tem de efetuar este procedimento para todos os recursos do Sharepoint, mas nem todos os documentos esse recurso. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>O Gestor de diretório Active Directory: conceder acesso de leitura do ficheiro para o QnA Maker

O Gestor do Active Directory (não o Gestor do QnA Maker) tem de conceder acesso ao QnA Maker para aceder ao recurso de Sharepoint selecionando [esta ligação](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) para autorizar a aplicação de enterprise do Sharepoint do Portal do QnA Maker para que o arquivo de leitura permissões. 

![O Gestor do Active Directory do Azure concede permissão de forma interativa](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Conceder acesso a partir do Centro de administração do Azure Active Directory

1. O Gestor de diretório Active Directory inicia sessão no portal do Azure e abre  **[aplicações empresariais](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** . 

1. Procure `QnAMakerPortalSharepoint` select a aplicação do QnA Maker. 

    [![Procure QnAMakerPortalSharepoint na lista de aplicações da empresa](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Sob **Security**, aceda à **permissões**. Selecione **conceder autorização de administrador para a organização**. 

    [![Selecione o utilizador autenticado para o administrador do Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecione uma conta de início de sessão com permissões para conceder permissões para o Active Directory. 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

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

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Colaborar na sua base de dados de conhecimento](collaborate-knowledge-base.md)
