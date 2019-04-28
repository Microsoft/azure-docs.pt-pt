---
title: Como configurar o Postman duplos Digital do Azure | Documentos da Microsoft
description: Como configurar o Postman duplos Digital do Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: adgera
ms.openlocfilehash: 49b073952b0923b940204b19680dcc9a1ffa44b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926203"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Como configurar o Postman duplos Digital do Azure

Este artigo descreve como configurar o cliente de REST do Postman para interagir com e testar as APIs de gestão de duplos Digital do Azure. Especificamente, ele descreve:

* como configurar uma aplicação do Azure Active Directory para utilizar o fluxo de concessão implícita OAuth 2.0.
* Como utilizar o cliente REST do Postman para fazer pedidos HTTP do efeito de token para as suas APIs de gestão.
* Como utilizar o Postman para fazer pedidos POST com várias partes às suas APIs de gestão.

## <a name="postman-summary"></a>Resumo do postman

Começar a duplos Digital do Azure, utilizando uma ferramenta de cliente REST como [Postman](https://www.getpostman.com/) para preparar o ambiente de teste local. O cliente do Postman ajuda a criar rapidamente complexos pedidos HTTP. Baixe a versão de área de trabalho do cliente do Postman ao aceder [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) é uma REST ferramenta de teste que localiza as principais funcionalidades de pedido HTTP num ambiente de trabalho útil e baseada em Plug-in GUI. 

Por meio do cliente do Postman, os desenvolvedores de soluções podem especificar o tipo de pedido HTTP (*POST*, *obter*, *ATUALIZAÇÃO*, *PATCH*e o  *ELIMINAR*), ponto final de API para chamar e a utilização do SSL. Postman também oferece suporte a adicionar cabeçalhos de pedido HTTP, parâmetros, dados do formulário e órgãos de desenvolvimento.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurar o Azure Active Directory para utilizar o fluxo de concessão implícita OAuth 2.0

Configure a sua aplicação do Azure Active Directory para utilizar o fluxo de concessão implícita OAuth 2.0.

1. Siga os passos em [este guia de introdução](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) para criar uma aplicação do Azure AD do tipo nativo. Ou pode reutilizar um registo de aplicação nativa existente.

1. Sob **permissões obrigatórias**, selecione **Add** e introduza **duplos Digital do Azure** sob **adicionar acesso à API**. Se a pesquisa não localizar a API, procure **Azure Smart Spaces** como alternativa. Em seguida, selecione **conceder permissões > permissões delegadas** e **feito**.

    ![Api de adicionar registos de aplicações do Azure Active Directory](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Clique em **manifesto** para abrir o manifesto do aplicativo para a sua aplicação. Definir *oauth2AllowImplicitFlow* para `true`.

      ![Fluxo implícito de Active Directory do Azure][1]

1. Configurar uma **URL de resposta** para `https://www.getpostman.com/oauth2/callback`.

      ![Azure Active Directory Reply URL][2]

1. Copie o e mantenha os **ID da aplicação** da sua aplicação do Azure Active Directory. É utilizado nos passos que se seguem.

## <a name="obtain-an-oauth-20-token"></a>Obter um token de OAuth 2.0

Em seguida, definir e configurar o Postman para obter um token do Azure Active Directory. Em seguida, efetue um pedido HTTP autenticado no duplos Digital do Azure com o token obtido:

1. Aceda a [www.getpostman.com](https://www.getpostman.com/) para transferir a aplicação.
1. Certifique-se de que sua **URL de autorização** está correto. Deve demorar o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu inquilino ou a organização | `microsoft` |

1. Selecione o **autorização** separador, selecione **OAuth 2.0**e, em seguida, selecione **obter novo Token de acesso**.

    | Campo  | Value |
    |---------|---------|
    | Tipo de Concessão | `Implicit` |
    | Url de chamada de retorno | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL | Utilize o **URL de autorização** do passo 2 |
    | ID de Cliente | Utilize o **ID da aplicação** para a aplicação do Azure Active Directory que foi criada ou reobjetivada da seção anterior |
    | Âmbito | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação de Cliente | `Send as Basic Auth header` |

1. O cliente deverá ser exibida como:

   ![Exemplo de cliente do postman][3]

1. Selecione **pedir o Token**.

    >[!TIP]
    >Se receber a mensagem de erro "Não foi possível concluir o OAuth 2", experimente o seguinte:
    > * Feche o Postman e abri-lo novamente e tente novamente.
  
1. Role para baixo e selecione **utilização Token**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Fazer um pedido de mensagem com várias partes

Depois de concluir os passos anteriores, configure o Postman para fazer uma solicitação autenticada para o HTTP com várias partes POST:

1. Sob o **cabeçalho** separador, adicione uma chave de cabeçalho de pedido HTTP **Content-Type** com o valor `multipart/mixed`.

   ![Tipo de conteúdo multipart/mixed][4]

1. Serialize dados que não sejam de texto em ficheiros. Dados JSON seriam guardados como um ficheiro JSON.
1. Sob o **corpo** separador, adicione cada ficheiro ao atribuir um **chave** nome, selecionando `file` ou `text`.
1. Em seguida, selecione cada ficheiro através do **Escolher ficheiro** botão.

   ![Exemplo de cliente do postman][5]

   >[!NOTE]
   > * O cliente do Postman não requer que os segmentos com várias partes têm um atribuídos manualmente **Content-Type** ou **Content-Disposition**.
   > * Não é necessário especificar esses cabeçalhos para cada parte.
   > * Tem de selecionar `multipart/mixed` ou de outra apropriado **Content-Type** para inteiramente o pedido.

1. Por último, clique em **enviar** para submeter o pedido de HTTP POST com várias partes.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as APIs de gestão de duplos digitais e como utilizá-los, leia [como utilizar as APIs de gestão de duplos Digital do Azure](how-to-navigate-apis.md).

- Utilizar a pedidos de várias partes [adicionar blobs para entidades dos Azure Digital duplos](./how-to-add-blobs.md).

- Para saber mais sobre a autenticação com as APIs de gestão, leia [autenticar com as APIs](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
[4]: media/how-to-configure-postman/content-type.png
[5]: media/how-to-configure-postman/form-body.png