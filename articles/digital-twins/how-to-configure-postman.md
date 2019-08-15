---
title: Como configurar o postmaster para o Azure digital gêmeos | Microsoft Docs
description: Como configurar o postmaster para o Azure digital gêmeos.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.openlocfilehash: 7ceb36d818c84642461372f0df70c8088908550c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965819"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Como configurar o postmaster para o Azure digital gêmeos

Este artigo descreve como configurar o cliente REST do postmaster para interagir com e testar as APIs de gerenciamento de gêmeos digital do Azure. Especificamente, ele descreve:

* Como configurar um aplicativo de Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2,0.
* Como usar o cliente REST do postmaster para fazer solicitações HTTP de passagem de token para suas APIs de gerenciamento.
* Como usar o postmaster para fazer solicitações de POSTAgem diversas em suas APIs de gerenciamento.

## <a name="postman-summary"></a>Resumo do postmaster

Introdução ao gêmeos digital do Azure usando uma ferramenta de cliente REST, como o [postmaster](https://www.getpostman.com/) , para preparar seu ambiente de teste local. O cliente do postmaster ajuda a criar rapidamente solicitações HTTP complexas. Baixe a versão da área de trabalho do cliente do postmaster acessando [www.getpostman.com/apps](https://www.getpostman.com/apps).

O [postmaster](https://www.getpostman.com/) é uma ferramenta de teste REST que localiza funcionalidades de solicitação HTTP de chave em uma GUI útil baseada em desktops e em plug-in.

Por meio do cliente do postmaster, as soluções que os desenvolvedores podem especificar o tipo de solicitação HTTP (*post*, *Get*, *Update*, *patch*e *delete*), o ponto de extremidade da API para chamar e o uso de SSL. O postmaster também dá suporte à adição de cabeçalhos de solicitação HTTP, parâmetros, dados de formulário e corpos.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurar Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2,0

Configure seu aplicativo Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2,0.

1. Siga as etapas neste guia de [início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) para criar um aplicativo do Azure AD do tipo nativo. Ou você pode reutilizar um registro de aplicativo nativo existente.

1. Em **permissões necessárias**, selecione **Adicionar** e insira **Azure digital gêmeos** em **Adicionar acesso à API**. Se a pesquisa não localizar a API, procure **Azure Smart Spaces** como alternativa. Em seguida, selecione **conceder permissões > permissões delegadas** e **concluídas**.

    [![Azure Active Directory de registros do aplicativo Adicionar API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Selecione **manifesto** para abrir o manifesto do aplicativo para seu aplicativo. Defina *oauth2AllowImplicitFlow* como `true`.

    [![Azure Active Directory fluxo implícito](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Configure uma **URL** de resposta `https://www.getpostman.com/oauth2/callback`para.

    [![Azure Active Directory URL de resposta](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Copie e mantenha a **ID do aplicativo** de seu Azure Active Directory aplicativo. Ele é usado nas etapas a seguir.

## <a name="obtain-an-oauth-20-token"></a>Obter um token 2,0 do OAuth

Em seguida, configure e configure o postmaster para obter um token de Azure Active Directory. Posteriormente, faça uma solicitação HTTP autenticada para o Azure digital gêmeos usando o token adquirido:

1. Acesse [www.getpostman.com](https://www.getpostman.com/) para baixar o aplicativo.
1. Verifique se a **URL de autorização** está correta. Ele deve ter o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nome  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu locatário ou organização | `microsoft` |

1. Selecione a guia **autorização** , selecione **OAuth 2,0**e, em seguida, selecione **obter novo token de acesso**.

    | Campo  | Value |
    |---------|---------|
    | Tipo de Concessão | `Implicit` |
    | Url de chamada de retorno | `https://www.getpostman.com/oauth2/callback` |
    | URL de autenticação | Use a **URL de autorização** da etapa 2 |
    | ID de Cliente | Use a **ID do aplicativo** para o Azure Active Directory aplicativo que foi criado ou realocado da seção anterior |
    | Scope | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação de Cliente | `Send as Basic Auth header` |

1. O cliente agora deve aparecer como:

    [![Exemplo de cliente do postmaster](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Selecione o **token de solicitação**.

    >[!TIP]
    >Se você receber a mensagem de erro "OAuth 2 não pôde ser concluído", tente o seguinte:
    > * Feche o postmaster e reabra-o e tente novamente.
  
1. Role para baixo e selecione **usar token**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Fazer uma solicitação de POSTAgem de várias partes

Depois de concluir as etapas anteriores, configure o postmaster para fazer uma solicitação de POSTAgem HTTP de várias partes autenticada:

1. Na guia **cabeçalho** , adicione um **tipo de conteúdo** de chave de cabeçalho de solicitação HTTP `multipart/mixed`com valor.

   [![Tipo de conteúdo com várias partes/misto](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serialize dados que não são de texto em arquivos. Os dados JSON seriam salvos como um arquivo JSON.
1. Na guia **corpo** , adicione cada arquivo atribuindo um nome de **chave** , selecionando `file` ou `text`.
1. Em seguida, selecione cada arquivo no botão **escolher arquivo** .

   [![Exemplo de cliente do postmaster](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * O cliente do postmaster não exige que partes com várias partes tenham um tipo de **conteúdo** ou disposição de **conteúdo**atribuído manualmente.
   > * Você não precisa especificar esses cabeçalhos para cada parte.
   > * Você deve selecionar `multipart/mixed` ou outro **tipo de conteúdo** apropriado para a solicitação inteira.

1. Por fim, selecione **Enviar** para enviar sua solicitação HTTP post de várias partes.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as APIs de gerenciamento de gêmeos digital e como usá-las, leia [como usar as APIs de gerenciamento do Azure digital gêmeos](how-to-navigate-apis.md).

- Use solicitações com várias partes para [Adicionar BLOBs às entidades do Azure digital gêmeos](./how-to-add-blobs.md).

- Para saber mais sobre como autenticar com as APIs de gerenciamento, leia [autenticar com APIs](./security-authenticating-apis.md).