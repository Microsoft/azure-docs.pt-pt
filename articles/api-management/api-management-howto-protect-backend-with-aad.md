---
title: Proteja o backend da API na Gestão da API utilizando o OAuth 2.0 e o AD Azure
titleSuffix: Azure API Management
description: Saiba como garantir o acesso a um backend web API na Azure API Management com autorização de utilizador OAuth 2.0 e Diretório Ativo Azure
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: article
ms.date: 09/23/2020
ms.author: apimpm
ms.custom: contperf-fy21q1
ms.openlocfilehash: face4beab450e92be76b2bb90e45625e025de6ee
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027922"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-by-using-oauth-20-authorization-with-azure-ad"></a>Proteja um backend web API na Azure API Management utilizando a autorização OAuth 2.0 com Azure AD 

Este guia mostra-lhe como configurar o seu exemplo [de Gestão API Azure](api-management-key-concepts.md) para proteger uma API, utilizando o [protocolo OAuth 2.0 com o Azure Ative Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md). 

> [!NOTE]
> Esta funcionalidade está disponível nos níveis **de Developer,** **Basic,** **Standard** e **Premium** da API Management.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, você deve ter:

- Um caso de Gestão da API
- Uma API publicada que utiliza a instância de Gestão da API
- Um inquilino da AD AZure

## <a name="overview"></a>Descrição geral

Segue-se uma visão geral rápida dos passos:

1. Registe uma aplicação (backend-app) em Azure AD para representar a API.
1. Registe outra aplicação (cliente-app) em Azure AD para representar uma aplicação do cliente que precisa de ligar para a API.
1. Em Azure AD, conceda permissões para permitir que o cliente-app ligue para a app backend.
1. Configure a Consola de Desenvolvedores para ligar para a API utilizando a autorização do utilizador OAuth 2.0.
1. Adicione a política **validada-jwt** para validar o token OAuth para cada pedido de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registar um pedido na AZure AD para representar a API

Para proteger uma API com AD Azure, primeiro registe uma aplicação na AZure AD que representa a API. Os passos seguintes utilizam o portal Azure para registar a aplicação. Para obter mais informações sobre o registo de aplicações, consulte [Quickstart: Configure uma aplicação para expor uma API web](../active-directory/develop/quickstart-configure-app-expose-web-apis.md).

1. Vá ao [portal Azure](https://portal.azure.com) para registar a sua inscrição. Procure e selecione **registos de Aplicações.**

1. Selecione **Novo registo**. 

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:

   - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, como a *aplicação backend.* 
   - Na secção **tipos de conta suportada,** selecione uma opção que se adapte ao seu cenário. 

1. Deixe a secção **URI de redirecionamento** vazia.

1. Selecione **Registar** para criar a aplicação. 

1. Na página **de Visão Geral** da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde.

1. Selecione **Expor uma API** e definir o **ID URI de aplicação** com o valor predefinido. Grave este valor para mais tarde.

1. Selecione o botão **Adicionar um botão de âmbito** para visualizar a página Adicionar um **âmbito.** Em seguida, crie um novo âmbito que seja suportado pela API (por exemplo, `Files.Read` ).

1. Selecione o botão **de âmbito adicionar** para criar o âmbito. Repita este passo para adicionar todos os âmbitos suportados pela sua API.

1. Quando os âmbitos forem criados, tome nota deles para serem utilizados num passo posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registe outra candidatura na AD Azure para representar uma aplicação ao cliente

Todas as aplicações de clientes que liguem para a API devem ser registadas como um pedido na Azure AD. Neste exemplo, a aplicação do cliente é a **Consola de Desenvolvimento** no portal de desenvolvimento da API Management. 

Para registar outra aplicação no AD Azure para representar a Consola de Desenvolvimento:

1. Vá ao [portal Azure](https://portal.azure.com) para registar a sua inscrição.

1. Procure e selecione **registos de Aplicações.**

1. Selecione **Novo registo**.

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:

   - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, como a *aplicação cliente.* 
   - Na secção **tipos de conta suportada,** selecione **Contas em qualquer diretório organizacional (Qualquer diretório AD Azure - Multitenant)**. 

1. Na secção **URI de redirecionamento,** selecione `Web` e deixe o campo URL vazio por enquanto.

1. Selecione **Registar** para criar a aplicação. 

1. Na página **de Visão Geral** da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde.

1. Crie um segredo de cliente para que esta aplicação seja usada num passo posterior.

   1. A partir da lista de páginas para a sua aplicação de cliente, selecione **Certificados & segredos,** e selecione **Novo segredo de cliente.**

   1. Em **Adicionar um segredo de cliente,** forneça uma **descrição**. Escolha quando a chave deve expirar e **selecione Adicionar**.

Quando o segredo for criado, note o valor chave para utilização num passo posterior. 

## <a name="grant-permissions-in-azure-ad"></a>Permissões de concessão em Azure AD

Agora que registou duas aplicações para representar a API e a Consola de Desenvolvimento, conceda permissões para permitir que a aplicação cliente-app ligue para a app backend.  

1. Vá ao [portal Azure](https://portal.azure.com) para conceder permissões à sua aplicação ao cliente. Procure e selecione **registos de Aplicações.**

1. Escolha a aplicação do seu cliente. Em seguida, na lista de páginas para a aplicação, selecione **permissões API**.

1. **Selecione Adicionar uma Permissão**.

1. Em **Seleção de uma API,** selecione **as minhas APIs** e, em seguida, encontre e selecione a sua aplicação backend.

1. Sob **Permissões Delegadas**, selecione as permissões apropriadas para a sua aplicação de backend e, em seguida, selecione **Permissões de adicionar**.

1. Opcionalmente, na página de permissões da **API,** selecione **o consentimento da administração Grant para \<your-tenant-name>** conceder o consentimento em nome de todos os utilizadores deste diretório. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Ativar a autorização do utilizador OAuth 2.0 na Consola de Desenvolvimento

Neste momento, criou as suas aplicações em Azure AD, e concedeu permissões adequadas para permitir que a aplicação do cliente ligue para a app backend. 

Neste exemplo, a Consola de Desenvolvimento é a aplicação cliente. Os seguintes passos descrevem como ativar a autorização do utilizador OAuth 2.0 na Consola do Desenvolvedor. 

1. No portal Azure, consulte a sua instância de Gestão de API.

1. **Selecione OAuth 2.0**  >  **Add**.

1. Forneça um **nome de exibição** e **descrição**.

1. Para o URL da página de registo do **Cliente,** insira um valor reservado, como `http://localhost` . O **URL da página de registo do Cliente** aponta para uma página que os utilizadores podem usar para criar e configurar as suas próprias contas para os fornecedores OAuth 2.0 que o suportam. Neste exemplo, os utilizadores não criam e configuram as suas próprias contas, pelo que utiliza um espaço reservado.

1. Para **tipos de concessão de autorização,** selecione **código de autorização.**

1. Especifique o URL do **ponto final de autorização** e o URL do ponto final **token**. Recupere estes valores da página **Endpoints** no seu inquilino AD Azure. Volte a navegar na página de **registos** da App e selecione **Pontos de Final**.


1. Copie o ponto final de **autorização OAuth 2.0** e cole-o na caixa de texto **URL do ponto final de autorização.** Selecione **POST** sob o método de pedido de autorização.

1. Copie o **Ponto Final de Token OAuth 2.0** e cole-o na caixa de texto **DE URL do ponto final token.** 

   >[!IMPORTANT]
   > Utilize pontos finais **v1** ou **v2.** No entanto, dependendo da versão que escolher, o passo abaixo será diferente. Recomendamos a utilização de pontos finais V2. 

1. Se utilizar pontos finais **v1,** adicione um parâmetro do corpo denominado **recurso**. Para o valor deste parâmetro, utilize o ID da **aplicação** da aplicação back-end. 

1. Se utilizar pontos finais **V2,** utilize o âmbito que criou para a aplicação backend no campo **de âmbito Padrão.** Além disso, certifique-se de definir o valor do [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) imóvel para `2` o seu manifesto [de aplicação.](../active-directory/develop/reference-app-manifest.md)

1. Em seguida, especifique as credenciais do cliente. Estas são as credenciais para a aplicação do cliente.

1. Para **identificação do cliente,** utilize o ID de **aplicação** da aplicação cliente.

1. Para **o segredo do Cliente,** utilize a chave que criou para a aplicação do cliente anteriormente. 

1. Imediatamente a seguir ao segredo do cliente está o **redirect_url** para o tipo de concessão de código de autorização. Tome nota desta URL.

1. Selecione **Criar**.

1. Volte ao registo da sua aplicação cliente no Azure Ative Directory e selecione **Autenticação.**

1. Nas **configurações da Plataforma** clique em Adicionar uma **plataforma**, e selecione o tipo como **Web**, cole o **redirect_url** em **URI de redirecionamento** e, em seguida, clique no botão **Configurar** para guardar.

Agora que configuraste um servidor de autorização OAuth 2.0, a Consola de Desenvolvimento pode obter fichas de acesso a partir do Azure AD. 

O próximo passo é ativar a autorização do utilizador OAuth 2.0 para a sua API. Isto permite que a Consola de Desenvolvedores saiba que precisa de obter um token de acesso em nome do utilizador, antes de então fazer chamadas para a sua API.

1. Navegue na sua instância de Gestão de API e vá a **APIs**.

1. Selecione a API que pretende proteger. Por exemplo, `Echo API`.

1. Ir para **Definições**.

1. Em **Segurança**, escolha **OAuth 2.0** e selecione o servidor OAuth 2.0 que configuraste anteriormente. 

1. Selecione **Guardar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Ligue com sucesso para a API a partir do portal do desenvolvedor

> [!NOTE]
> Esta secção não se aplica ao nível **de Consumo,** que não suporta o portal do desenvolvedor.

Agora que a autorização do utilizador OAuth 2.0 está ativada na sua API, a Consola de Desenvolvedores obterá um token de acesso em nome do utilizador, antes de ligar para a API.

1. Navegue por qualquer operação sob a API no portal do desenvolvedor e **selecione Experimente-a**. Isto leva-o à Consola de Desenvolvimento.

1. Note um novo item na secção **Autorização,** correspondente ao servidor de autorização que acaba de adicionar.

1. Selecione o código de **autorização** da lista de depósito de autorização, e é solicitado a iniciar sação no inquilino da AD Azure. Se já assinou com a conta, pode não ser solicitado.

1. Após o sucesso do sinstrução, `Authorization` um cabeçalho é adicionado ao pedido, com um token de acesso da Azure AD. Segue-se um token de amostra (Base64 codificado):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Selecione **Enviar** para ligar para a API com sucesso.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configure uma política de validação da JWT para pré-autorizar pedidos

Neste momento, quando um utilizador tenta fazer uma chamada a partir da Consola de Desenvolvimento, o utilizador é solicitado a iniciar sedu. A Consola de Desenvolvedores obtém um token de acesso em nome do utilizador, e inclui o token no pedido feito à API.

No entanto, e se alguém ligar para a sua API sem um símbolo ou com um token inválido? Por exemplo, tente ligar para a API sem o `Authorization` cabeçalho, a chamada ainda vai passar. A razão é que a API Management não valida o token de acesso neste momento. Simplesmente passa o `Authorization` cabeçalho para a API de fundo.

Utilize a política [validada do JWT](./api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar pedidos na Gestão da API, validando os tokens de acesso de cada pedido de entrada. Se um pedido não tiver um token válido, a API Management bloqueia-o. Por exemplo, adicione a seguinte política à `<inbound>` secção política do `Echo API` . Verifica a reclamação do público num token de acesso e devolve uma mensagem de erro se o sinal não for válido. Para obter informações sobre como configurar políticas, consulte [políticas definidas ou editar](./set-edit-policies.md).


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> Este `openid-config` URL corresponde ao ponto final v1. Para o `openid-config` ponto final v2, utilize o seguinte URL:
>
> `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Construir uma aplicação para chamar a API

Neste guia, utilizou a Consola de Desenvolvimento em Gestão API como a aplicação do cliente da amostra para ligar para o `Echo API` protegido pela OAuth 2.0. Para saber mais sobre como construir uma aplicação e implementar o OAuth 2.0, consulte [as amostras de código do Azure Ative Directory](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Ative Directory e o OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
- Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a API Management.
- Para obter outras formas de garantir o seu serviço back-end, consulte a [autenticação do Certificado Mútuo.](./api-management-howto-mutual-certificates.md)
- [Criar uma instância de serviço de Gestão API.](./get-started-create-service-instance.md)
- [Gerencie a sua primeira API.](./import-and-publish.md)
