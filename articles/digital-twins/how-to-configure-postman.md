---
title: Como configurar o postmaster – Azure digital gêmeos | Microsoft Docs
description: Saiba como configurar e usar o postmaster para testar as APIs do gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023315"
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

1. Siga os passos [no Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) para criar e configurar uma aplicação azure Ative Directory. Como alternativa, você pode reutilizar um registro de aplicativo existente.

    [![Configurar um novo Carteiro Redirecionar URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Agora, adicione um **Uri Redirecionamento** para `https://www.getpostman.com/oauth2/callback`.

1. Selecione a caixa de verificação de **fichas** de acesso > Acesso **para** permitir a sua utilizada caixa de verificação implícita o OAuth 2.0. **Selecione Configurar**e, em seguida, **guardar**.

1. Copie a **identificação** do cliente da sua aplicação Azure Ative Directory.

## <a name="obtain-an-oauth-20-token"></a>Obter um token 2,0 do OAuth

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Configure e configure o postmaster para obter um token de Azure Active Directory. Posteriormente, faça uma solicitação HTTP autenticada para o Azure digital gêmeos usando o token adquirido:

1. Verifique se a **URL de autorização** está correta. Ele deve ter o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nome  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu inquilino ou organização. Use o nome amigo do homem em vez do ID alfanumérico do **seu** registo de aplicações azure Ative Directory. | `microsoft` |

1. Acesse [www.getpostman.com](https://www.getpostman.com/) para baixar o aplicativo.

1. Queremos fazer um pedido get. Selecione o separador **Autorização,** selecione OAuth 2.0 e, em seguida, selecione **Obter novo acesso token**.

    | Campo  | Valor |
    |---------|---------|
    | Tipo de concessão | `Implicit` |
    | Callback URL | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL | Utilize o URL de **Autorização** a partir do **passo 1** |
    | ID de Cliente | Use a **ID do aplicativo** para o Azure Active Directory aplicativo que foi criado ou reutilizado da seção anterior |
    | Âmbito | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação do Cliente | `Send as Basic Auth header` |

1. O cliente agora deve aparecer como:

    [![exemplo simbólico do cliente carteiro](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecione **Request Token**.
  
1. Role para baixo e selecione **usar token**.

## <a name="make-a-multipart-post-request"></a>Fazer uma solicitação de POSTAgem de várias partes

Depois de concluir as etapas anteriores, configure o postmaster para fazer uma solicitação de POSTAgem HTTP de várias partes autenticada:

1. Na guia **cabeçalhos** , adicione um **tipo de conteúdo de chave de** cabeçalho de solicitação HTTP com valor `multipart/mixed`.

   [![Especificar o tipo de conteúdo multiparte/misto](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialize dados que não são de texto em arquivos. Os dados JSON seriam salvos como um arquivo JSON.
1. Na guia **corpo** , selecione `form-data`. 
1. Adicione cada arquivo atribuindo um nome de **chave** , selecionando `File`.
1. Em seguida, selecione cada arquivo no botão **escolher arquivo** .

   [![cliente carteiro forma exemplo de corpo](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * O cliente do postmaster não exige que partes com várias partes tenham um tipo de **conteúdo** ou disposição de **conteúdo**atribuído manualmente.
   > * Você não precisa especificar esses cabeçalhos para cada parte.
   > * Você deve selecionar `multipart/mixed` ou outro **tipo de conteúdo** apropriado para a solicitação inteira.

1. Por fim, selecione **Enviar** para enviar sua solicitação HTTP post de várias partes. Um código de status de `200` ou `201` indica uma solicitação bem-sucedida. A mensagem de resposta apropriada aparecerá na interface do cliente.

1. Valide seus dados de solicitação HTTP POST chamando o ponto de extremidade da API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as APIs de gerenciamento de gêmeos digital e como usá-las, leia [como usar as APIs de gerenciamento do Azure digital gêmeos](how-to-navigate-apis.md).

- Use solicitações com várias partes para [Adicionar BLOBs às entidades do Azure digital gêmeos](./how-to-add-blobs.md).

- Para saber mais sobre como autenticar com as APIs de gerenciamento, leia [autenticar com APIs](./security-authenticating-apis.md).
