---
title: Ficheiros SharePoint - QnA Maker
description: Adicione fontes de dados seguras do SharePoint à sua base de conhecimentos para enriquecer a base de conhecimentos com perguntas e respostas que possam ser protegidas com O Diretório Ativo.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80294883"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Adicione uma fonte de dados segura do SharePoint à sua base de conhecimentos

Adicione fontes de dados seguras baseadas na nuvem do SharePoint à sua base de conhecimentos para enriquecer a base de conhecimentos com perguntas e respostas que possam ser protegidas com O Diretório Ativo.

Quando adicionar um documento SharePoint seguro à sua base de conhecimentos, como gestor do QnA Maker, deve solicitar permissão de Diretório Ativo para o Fabricante qnA. Uma vez que esta permissão é dada do gestor de Diretório Ativo ao QnA Maker para acesso ao SharePoint, não precisa de ser dada novamente. Cada adição de documento subsequente à base de conhecimentos não necessitará de autorização se estiver no mesmo recurso SharePoint.

Se o gestor de base de conhecimento do QnA Maker não for o gestor de Diretório ativo, terá de comunicar com o gestor de Directórioativo Ativo para concluir este processo.

## <a name="prerequisites"></a>Pré-requisitos

* SharePoint baseado em nuvem - QnA Maker usa o Microsoft Graph para permissões. Se o seu SharePoint estiver no local, não poderá extrair do SharePoint porque o Microsoft Graph não será capaz de determinar permissões.
* Formato URL - QnA Maker apenas suporta urls SharePoint que são gerados para partilha e são de formato`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Adicione tipos de ficheiros suportados à base de conhecimentos

Pode adicionar todos os tipos de [ficheiros](../Concepts/content-types.md) suportados pelo QnA Maker a partir de um site do SharePoint à sua base de conhecimentos. Poderá ter de conceder [permissões](#permissions) se o recurso do ficheiro estiver seguro.

1. A partir da biblioteca com o site SharePoint, selecione o menu de elipse do ficheiro, `...`.
1. Copie a URL do ficheiro.

   ![Obtenha o URL do ficheiro SharePoint selecionando o menu de elipse do ficheiro e, em seguida, copiando o URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. No portal QnA Maker, na página **Definições,** [adicione o URL](manage-knowledge-bases.md#edit-knowledge-base) à base de conhecimentos.

### <a name="images-with-sharepoint-files"></a>Imagens com ficheiros SharePoint

Se os ficheiros incluirem imagens, estas não são extraídas. Pode adicionar a imagem, a partir do portal QnA Maker, depois de o ficheiro ser extraído em pares QnA.

Adicione a imagem com a seguinte sintaxe de marcação:

```markdown
![Explanation or description of image](URL of public image)
```

O texto nos suportes `[]`quadrados, explica a imagem. O URL nos parênteses, `()`é o link direto para a imagem.

Quando se testa o par QnA no painel de teste interativo, no portal QnA Maker, a imagem é exibida, em vez do texto de marcação. Isto valida que a imagem pode ser recuperada publicamente a partir da sua aplicação de cliente.

## <a name="permissions"></a>Permissões

A concessão de permissões ocorre quando um ficheiro seguro de um servidor SharePoint é adicionado a uma base de conhecimento. Dependendo da forma como o SharePoint é configurado e das permissões da pessoa que adiciona o ficheiro, isso pode exigir:

* sem passos adicionais - a pessoa que adiciona o ficheiro tem todas as permissões necessárias.
* passos tanto pelo [gestor da base](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) de conhecimento como pelo gestor de [Directórioactivo Ativo.](#active-directory-manager-grant-file-read-access-to-qna-maker)

Veja os passos listados abaixo.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gestor de base de conhecimento: adicione fonte de dados do SharePoint no portal QnA Maker

Quando o gestor do **QnA Maker** adiciona um documento SharePoint seguro a uma base de conhecimentos, o gestor da base de conhecimentos inicia um pedido de permissão que o gestor de Diretório Ativo precisa de concluir.

O pedido começa com um pop-up para autenticar uma conta de Diretório Ativo.

![Autenticar conta de utilizador](../media/add-sharepoint-datasources/authenticate-user-account.png)

Assim que o gestor do QnA Maker selecionar a conta, o administrador do Diretório Ativo Azure receberá um aviso de que precisa de permitir o acesso da aplicação QnA Maker (não ao gestor do QnA Maker) ao recurso SharePoint. O gestor de Diretórios Ativos do Azure terá de o fazer por cada recurso do SharePoint, mas não por todos os documentos desse recurso.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Gestor de diretório ativo: ficheiro de subvenção lê acesso ao QnA Maker

O gestor de Diretório Ativo (não o gestor do QnA Maker) precisa de conceder acesso ao QnA Maker para aceder ao recurso SharePoint, selecionando [este link](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) para autorizar a aplicação empresarial QnA Maker Portal SharePoint para ter permissões de leitura de ficheiros.

![Gestor de Diretório Ativo Azure concede permissão interativamente](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Acesso de concessão do centro de administração do Azure Ative Directory

1. O gestor de Diretório Ativo inscreve-se no portal Azure e abre **[aplicações da Enterprise.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**

1. Procure `QnAMakerPortalSharePoint` a aplicação QnA Maker.

    [![Pesquisa por QnAMakerPortalSharePoint na lista de aplicações da Enterprise](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Sob **Segurança,** vá a **Permissões.** Selecione O consentimento do **administrador grant para a Organização**.

    [![Selecione utilizador autenticado para Administrador de Diretório Ativo](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecione uma conta De Acesso com permissões para conceder permissões para o Diretório Ativo.



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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Colabore na sua base de conhecimento](collaborate-knowledge-base.md)
