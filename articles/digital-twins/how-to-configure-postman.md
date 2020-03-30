---
title: Como configurar o Carteiro - Azure Digital Twins [ Azure Digital Twins] Microsoft Docs
description: Aprenda a configurar e usar o Carteiro para testar as APIs de Gémeos Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297159"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Como configurar o Carteiro para Gémeos Digitais Azure

Este artigo descreve como configurar o cliente do Postman REST para interagir e testar as APIs de Gestão de Gémeos Digitais Azure. Especificamente, descreve:

* Como configurar uma aplicação azure Ative Diretório para utilizar o fluxo de subvenção implícita OAuth 2.0.
* Como utilizar o cliente Postman REST para fazer pedidos http de token-bearing para as suas APIs de Gestão.
* Como utilizar o Carteiro para fazer pedidos postais multipart às suas APIs de Gestão.

## <a name="postman-summary"></a>Resumo do carteiro

Inicie a Azure Digital Twins utilizando uma ferramenta de cliente REST, como [o Carteiro,](https://www.getpostman.com/) para preparar o seu ambiente de testes local. O cliente Carteiro ajuda a criar rapidamente pedidos complexos de HTTP. Descarregue a versão do ambiente de trabalho do cliente do Carteiro [indo](https://www.getpostman.com/apps)para www.getpostman.com/apps .

[O Carteiro](https://www.getpostman.com/) é uma ferramenta de teste REST que localiza as funcionalidades de pedido http chave num ambiente de trabalho útil e GUI baseado em plugin.

Através do cliente Postman, os desenvolvedores de soluções podem especificar o tipo de pedido http (*POST,* *GET,* *UPDATE,* *PATCH*, e *DELETE),* Ponto final da API para ligar e usar o TLS. O carteiro também suporta a adição de cabeçalhos de pedido HTTP, parâmetros, dados de formulários e corpos.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configure Azure Ative Diretório para utilizar o fluxo de subvenção implícita OAuth 2.0

1. Siga os passos [no Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) para criar e configurar uma aplicação azure Ative Directory. Em alternativa, pode reutilizar o registo de aplicações existente.

    [![Configure um novo Carteiro Redirecione URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Agora, adicione um Uri `https://www.getpostman.com/oauth2/callback` **Redirecionamento** para .

1. Selecione a caixa de verificação de**fichas** de acesso **implícita** > para permitir a utilizada do fluxo de subvenção implícito OAuth 2.0. **Selecione Configurar**e, em seguida, **guardar**.

1. Copie a **identificação** do cliente da sua aplicação Azure Ative Directory.

## <a name="obtain-an-oauth-20-token"></a>Obtenha um token OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Configure e configure o Carteiro para obter um token azure Ative Directory. Posteriormente, faça um pedido http autenticado à Azure Digital Twins utilizando o símbolo adquirido:

1. Verifique se o url de **autorização** está correto. Deve tomar o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nome  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu inquilino ou organização. Use o nome amigo do homem em vez do ID alfanumérico do **seu** registo de aplicações azure Ative Directory. | `microsoft` |

1. Vá a [www.getpostman.com](https://www.getpostman.com/) para descarregar a aplicação.

1. Queremos fazer um pedido get. Selecione o separador **Autorização,** selecione OAuth 2.0 e, em seguida, selecione **Obter novo acesso token**.

    | Campo  | Valor |
    |---------|---------|
    | Tipo de Concessão | `Implicit` |
    | URL de chamada de retorno | `https://www.getpostman.com/oauth2/callback` |
    | URL Auth | Utilize o URL de **Autorização** a partir do **passo 1** |
    | ID de Cliente | Utilize o ID de **aplicação** para a app Azure Ative Directory que foi criada ou reutilizada a partir da secção anterior |
    | Âmbito | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação de Cliente | `Send as Basic Auth header` |

1. O cliente deve agora aparecer como:

    [![Exemplo simbólico do cliente carteiro](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecione **Pedir Token**.
  
1. Desloque-se para baixo e selecione **Use Token**.

## <a name="make-a-multipart-post-request"></a>Faça um pedido de POST multipart

Depois de completar os passos anteriores, configure o Carteiro para fazer um pedido de POST multipart http autenticado:

1. Sob o separador **Headers,** adicione uma **chave** `multipart/mixed`de erro http com valor .

   [![Especificar o tipo de conteúdo multiparte/misto](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialize dados não-texto em ficheiros. Os dados da JSON seriam guardados como um ficheiro JSON.
1. Sob o separador `form-data` **Body,** selecione . 
1. Adicione cada ficheiro atribuindo um **nome-chave,** selecionando `File`.
1. Em seguida, selecione cada ficheiro através do botão **Escolher** Ficheiro.

   [![Exemplo de corpo de cliente carteiro](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * O cliente carteiro não exige que os pedaços multipartes tenham um **Tipo de Conteúdo** ou **Disposição**de Conteúdo manualmente atribuídos .
   > * Não é necessário especificar os cabeçalhos para cada peça.
   > * Deve selecionar `multipart/mixed` ou outro **Conteúdo-Tipo** apropriado para todo o pedido.

1. Por último, selecione **Enviar** para submeter o seu pedido http POST multipart. Um código `200` de `201` estado ou indica um pedido bem sucedido. A mensagem de resposta adequada aparecerá na interface do cliente.

1. Valide os dados do pedido HTTP POST, ligando para o ponto final da API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Passos seguintes

- Para conhecer as APIs de gestão de Gémeos Digitais e como usá-las, leia Como utilizar apis de gestão de [Gémeos Digitais Azure.](how-to-navigate-apis.md)

- Utilize pedidos multipart para [adicionar bolhas às entidades da Azure Digital Twins.](./how-to-add-blobs.md)

- Para aprender sobre autenticação com as APIs de Gestão, leia [Authenticato com APIs](./security-authenticating-apis.md).
