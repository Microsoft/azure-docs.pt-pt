---
title: Proteja uma API utilizando o OAuth 2.0 com AAD e API Management
titleSuffix: Azure API Management
description: Aprenda a proteger um backend web da API com o Azure Ative Directory e a API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 300f44daeeea5e8a774575dabcb00686906bb5de
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804372"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Protect an API by using OAuth 2.0 with Azure Active Directory and API Management (Proteger uma API com o OAuth 2.0 com o Azure Active Directory e a Gestão de API)

Este guia mostra-lhe como configurar a sua instância de Gestão API Azure para proteger uma API, utilizando o protocolo OAuth 2.0 com o Azure Ative Directory (Azure AD). 

> [!NOTE]
> Esta funcionalidade está disponível nos níveis **Developer,** **Standard** e **Premium** da API Management.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir os passos deste artigo, deve ter:
* Uma instância de Gestão API
* API está a ser publicada que utiliza a instância de Gestão API
* Um inquilino da AD Azure

## <a name="overview"></a>Descrição geral

Aqui está uma visão geral rápida dos passos:

1. Registe uma aplicação (backend-app) em Azure AD para representar a API.
2. Registe outra aplicação (cliente-app) na Azure AD para representar uma aplicação de cliente que precisa de chamar a API.
3. Em Azure AD, conceda permissões para permitir que a aplicação de clientes ligue para a aplicação backend.
4. Configure a Consola de Desenvolvimento para ligar para a API utilizando a autorização do utilizador OAuth 2.0.
5. Adicione a política **validada-jwt** para validar o token OAuth para cada pedido de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registe uma candidatura na AD Azure para representar a API

Para proteger uma API com AD Azure, o primeiro passo é registar uma aplicação em Azure AD que represente a API. 

1. Vá ao [portal Azure](https://portal.azure.com) para registar a sua candidatura. Procure e selecione **registos DE APP**.

1. Selecione **Novo registo**. 

1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura: 
    - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da app, como a *aplicação backend.* 
    - Na secção de tipos de **conta suportada,** selecione uma opção que se adapte ao seu cenário. 

1. Deixe a secção **URI redirecionamento** vazia.

1. Selecione **Registar** para criar a aplicação. 

1. Na página **'Overview'** da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde.

1. Selecione **Expor uma API** e definir o **ID DE Aplicação URI** com o valor predefinido. Grave este valor para mais tarde.

1. Selecione o botão **Adicionar um botão** de âmbito para visualizar a página Adicionar uma página de **âmbito.** Em seguida, crie um novo âmbito que seja `Files.Read`suportado pela API (por exemplo, ). Por fim, selecione o botão **de âmbito Adicionar** para criar o âmbito. Repita este passo para adicionar todos os âmbitos suportados pela sua API.

1. Quando os âmbitos forem criados, tome nota dos mesmos para utilização num passo posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registe mais uma aplicação na AD Azure para representar uma aplicação de cliente

Todas as aplicações de clientes que liguem para a API também têm de ser registadas como aplicação em Azure AD. Neste exemplo, a aplicação do cliente é a Consola de Desenvolvimento no portal de desenvolvimento de Gestão aPI. Aqui está como registar outra aplicação em Azure AD para representar a Consola de Desenvolvimento.

1. Vá ao [portal Azure](https://portal.azure.com) para registar a sua candidatura. Procure e selecione **registos DE APP**.

1. Selecione **Novo registo**.

1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura: 
    - Na secção **Nome,** introduza um nome de aplicação significativo que será exibido aos utilizadores da app, como a *aplicação cliente.'* 
    - Na secção de tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional (Qualquer diretório AD Azure - Multitenant)**. 

1. Na secção **Redirecionamento URI,** selecione `Web` e introduza o URL `https://contoso5.portal.azure-api.net/signin`.

1. Selecione **Registar** para criar a aplicação. 

1. Na página **'Overview'** da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde.

Agora, crie um segredo de cliente para que esta aplicação seja usada num passo subsequente.

1. A partir da lista de páginas para a sua aplicação cliente, selecione **Certificados & segredos**, e selecione novo segredo de **cliente.**

1. Em **Adicionar um segredo de cliente,** forneça uma **Descrição**. Escolha quando a chave deve expirar e selecione **Adicionar**.

Quando o segredo for criado, note o valor-chave para uso num passo posterior. 

## <a name="grant-permissions-in-azure-ad"></a>Concessão de permissões em Azure AD

Agora que registou duas aplicações para representar a API e a Consola de Desenvolvimento, precisa de conceder permissões para permitir que a aplicação do cliente ligue para a aplicação de backend.  

1. Vá ao [portal Azure](https://portal.azure.com) para conceder permissões à sua aplicação de cliente. Procure e selecione **registos DE APP**.

1. Escolha a sua aplicação de cliente. Em seguida, na lista de páginas para a aplicação, selecione **permissões API**.

1. Selecione **Adicionar uma Permissão**.

1. Em **selecionar um API,** selecione **My APIs**e, em seguida, encontre e selecione a sua aplicação de backend.

1. Sob **permissões delegadas,** selecione as permissões adequadas para a sua aplicação de backend e, em seguida, **selecione Adicionar permissões**.

1. Opcionalmente, na página de **permissões da API,** selecione o consentimento do **administrador grant \<para o seu nome de inquilino>** para conceder o consentimento em nome de todos os utilizadores deste diretório. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Ativar a autorização de utilizador da OAuth 2.0 na consola de desenvolvimento

Neste momento, criou as suas aplicações em Azure AD e concedeu permissões adequadas para permitir que a aplicação de clientes ligue para a aplicação de backend. 

Neste exemplo, a Consola de Desenvolvimento é a aplicação cliente-app. Os seguintes passos descrevem como ativar a autorização de utilizador OAuth 2.0 na Consola de Desenvolvimento. 

1. No portal Azure, navegue até à sua instância de Gestão API.

1. Selecione **OAuth 2.0** > **Adicionar**.

1. Forneça um nome e **descrição**de **exibição.**

1. Para o URL da página de registo do `http://localhost` **Cliente,** insira um valor de espaço reservado, como . O URL da página de **registo do Cliente** aponta para uma página que os utilizadores podem usar para criar e configurar as suas próprias contas para os fornecedores OAuth 2.0 que suportam esta. Neste exemplo, os utilizadores não criam e configuram as suas próprias contas, pelo que utilizam um espaço reservado.

1. Para tipos de **concessão de autorização,** selecione **código de autorização**.

1. Especifique o **URL do ponto final** da autorização e o URL final do ponto final do **token**. Recupere estes valores da página **Endpoints** no seu inquilino Azure AD. Volte a procurar na página de **registos** da App e selecione **Pontos Finais**.


1. Copie o ponto final de **autorização OAuth 2.0**e cole-o na caixa de texto URL do ponto final da **Autorização.** Selecione **POST** no método de pedido de autorização.

1. Copie o **Ponto final OAuth 2.0 Token**, e cole-o na caixa de texto url do **ponto final token.** 

    >[!IMPORTANT]
    > Pode utilizar pontos finais **v1** ou **v2.** No entanto, dependendo da versão que escolher, o passo abaixo será diferente. Recomendamos a utilização de pontos finais v2. 

1. Se utilizar pontos finais **v1,** adicione um parâmetro corporal chamado **recurso**. Para o valor deste parâmetro, utilize o ID da **aplicação** de aplicação de back-end. 

1. Se utilizar pontos finais **v2,** utilize o âmbito que criou para a aplicação de backend no campo **de âmbito Predefinido.** Além disso, certifique-se [`accessTokenAcceptedVersion`](/azure/active-directory/develop/reference-app-manifest#accesstokenacceptedversion-attribute) de `2` definir o valor para o imóvel no seu manifesto de [candidatura](/azure/active-directory/develop/reference-app-manifest).

1. Em seguida, especifique as credenciais do cliente. Estas são as credenciais para a aplicação cliente-app.

1. Para ID do **cliente,** utilize o ID de **aplicação** da aplicação cliente.

1. Para o segredo do **Cliente,** utilize a chave que criou para a aplicação de clientes mais cedo. 

1. Imediatamente a seguir ao segredo do cliente está a **redirect_url** para o tipo de concessão de código de autorização. Tome nota deste URL.

1. Selecione **Criar**.

1. Volte para a sua aplicação de cliente e **selecione Autenticação**.

1. Em **Redirecione os URIs**, selecione o tipo como **Web**, repasse o **redirect_url** em **Redirect URI**, e, em seguida, guarde.

Agora que configuraum servidor de autorização OAuth 2.0, a Consola de Desenvolvimento pode obter fichas de acesso a partir de Azure AD. 

O próximo passo é ativar a autorização de utilizador da OAuth 2.0 para a sua API. Isto permite que a Consola de Desenvolvimento saiba que precisa de obter um sinal de acesso em nome do utilizador, antes de fazer chamadas para a sua API.

1. Navegue na sua instância de Gestão API e vá para **APIs**.

2. Selecione a API que pretende proteger. Por exemplo, pode `Echo API`utilizar o .

3. Ir para **Definições**.

4. Em **Segurança,** escolha **OAuth 2.0**e selecione o servidor OAuth 2.0 que configurau anteriormente. 

5. Selecione **Guardar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Ligue com sucesso para a API do portal de desenvolvimento

> [!NOTE]
> Esta secção não se aplica ao nível **de Consumo,** que não suporta o portal de desenvolvimento.

Agora que a autorização de utilizador OAuth 2.0 está ativada no seu API, a Consola de Desenvolvimento obterá um sinal de acesso em nome do utilizador, antes de ligar para a API.

1. Navegue em qualquer operação sob a API no portal do desenvolvedor e selecione **Experimente- a**. Isto leva-te à Consola de Desenvolvimento.

2. Note um novo item na secção **de Autorização,** correspondente ao servidor de autorização que acabou de adicionar.

3. Selecione **código de autorização** da lista de autorização e é solicitado que inscreva-se no inquilino da AD Azure. Se já assinou a conta, pode não ser solicitado.

4. Após o sucesso da `Authorization` entrada, um cabeçalho é adicionado ao pedido, com um sinal de acesso da Azure AD. Segue-se uma amostra de símbolo (Base64 codificada):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selecione **Enviar**e pode ligar para a API com sucesso.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configure uma política de validação JWT para pré-autorizar pedidos

Neste momento, quando um utilizador tenta fazer uma chamada a partir da Consola de Desenvolvimento, o utilizador é solicitado a iniciar sessão. A Consola de Desenvolvimento obtém um sinal de acesso em nome do utilizador, e inclui o símbolo no pedido feito à API.

No entanto, e se alguém ligar para a sua API sem um símbolo ou com um token inválido? Por exemplo, tente ligar para `Authorization` a API sem o cabeceamento, a chamada ainda vai passar. A razão é que a API Management não valida o sinal de acesso neste momento. Simplesmente passa `Authorization` o cabeceamento para a API traseira.

Pode utilizar a política [De Validação JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar pedidos na Gestão API, validando os tokens de acesso de cada pedido de entrada. Se um pedido não tiver um token válido, a API Management bloqueia-o. Por exemplo, adicione a `<inbound>` seguinte política `Echo API`à secção política do . Verifica a reclamação do público num sinal de acesso e devolve uma mensagem de erro se o token não for válido. Para obter informações sobre como configurar políticas, consulte [definir ou editar políticas.](set-edit-policies.md)

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
> Este `openid-config` URL corresponde ao ponto final v1. Para o `openid-config`ponto final `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`v2, utilize .

## <a name="build-an-application-to-call-the-api"></a>Construa uma aplicação para chamar a API

Neste guia, utilizou a Consola de Desenvolvimento em Gestão API como aplicação do cliente da amostra para chamar o `Echo API` protegido pela OAuth 2.0. Para saber mais sobre como construir uma aplicação e implementar o OAuth 2.0, consulte as amostras de código do [Diretório Ativo azure.](../active-directory/develop/sample-v2-code.md)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [o Azure Ative Directory e o OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a Gestão da API.
* Para outras formas de garantir o seu serviço de back-end, consulte a [autenticação do Certificado Mútuo](api-management-howto-mutual-certificates.md).

* Criar uma instância de [serviço de Gestão API.](get-started-create-service-instance.md)

* [Gerencie a sua primeira API.](import-and-publish.md)
