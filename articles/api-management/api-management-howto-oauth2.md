---
title: Autorizar contas de programadores utilizando OAuth 2.0 em Gestão de API
titleSuffix: Azure API Management
description: Saiba como autorizar os utilizadores a utilizarem o OAuth 2.0 em Gestão API. A OAuth 2.0 assegura a API para que os utilizadores só possam aceder aos recursos a que têm direito.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077256"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de programadores utilizando o OAuth 2.0 na Azure API Management

Muitas APIs suportam [OAuth 2.0](https://oauth.net/2/) para garantir a API e garantir que apenas utilizadores válidos têm acesso, e só podem aceder aos recursos a que têm direito. Para utilizar a consola interativa de desenvolvimento da Azure API Management com tais APIs, o serviço permite-lhe configurar a sua instância de serviço para trabalhar com a sua API ativada pela OAuth 2.0.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Pré-requisitos

Este guia mostra-lhe como configurar a sua instância de serviço de Gestão API para utilizar a autorização OAuth 2.0 para contas de programadores, mas não mostra como configurar um provedor OAuth 2.0. A configuração para cada provedor OAuth 2.0 é diferente, embora os passos sejam semelhantes, e as peças de informação necessárias utilizadas na configuração da OAuth 2.0 na sua instância de serviço de API Management são as mesmas. Este tópico mostra exemplos usando o Azure Ative Directory como fornecedor OAuth 2.0.

> [!NOTE]
> Para obter mais informações sobre a configuração do OAuth 2.0 utilizando o Azure Ative Directory, consulte a amostra [WebApp-GraphAPI-DotNet.][WebApp-GraphAPI-DotNet]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Configurar um servidor de autorização OAuth 2.0 na Gestão da API

> [!NOTE]
> Se ainda não criou uma instância de serviço de Gestão API, consulte [criar uma instância de serviço de Gestão API.][Create an API Management service instance]

1. Clique no separador OAuth 2.0 no menu à esquerda e clique em **+Adicionar** .

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Introduza um nome e uma descrição opcional nos campos **Nome** e **Descrição.**

    > [!NOTE]
    > Estes campos são utilizados para identificar o servidor de autorização OAuth 2.0 dentro da atual instância de serviço de Gestão da API e os seus valores não provêm do servidor OAuth 2.0.

3. Insira o URL da **página de registo do Cliente.** Esta página é onde os utilizadores podem criar e gerir as suas contas, e varia consoante o provedor OAuth 2.0 utilizado. O **URL da página de registo** do Cliente aponta para a página que os utilizadores podem usar para criar e configurar as suas próprias contas para os fornecedores OAuth 2.0 que suportam a gestão de contas dos utilizadores - por exemplo, `https://contoso.com/login` . Algumas organizações não configuram ou utilizam esta funcionalidade mesmo que o provedor OAuth 2.0 a suporte. Se o seu fornecedor OAuth 2.0 não tiver a gestão do utilizador das contas configuradas, insira aqui um URL de espaços reservado, como o URL da sua empresa, ou um URL como `https://placeholder.contoso.com` .

    ![OAuth 2.0 novo servidor](./media/api-management-howto-oauth2/oauth-02.png)

4. A secção seguinte do formulário contém os tipos de **concessão** de autorização, **URL de ponto final de autorização** e definições do método de pedido de **autorização.**

    Especifique os **tipos de concessão de autorização** verificando os tipos pretendidos. **O código de autorização** é especificado por predefinição.

    Introduza o **URL do ponto final de autorização** . Para o Azure Ative Directory, este URL será semelhante ao seguinte URL, onde `<tenant_id>` é substituído pelo ID do seu inquilino AZure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    O **método de pedido de autorização** especifica como o pedido de autorização é enviado para o servidor OAuth 2.0. Por predefinição, **o GET** é selecionado.

5. Em seguida, **URL de ponto final token,** **métodos de autenticação do cliente,** **método de envio de token** de acesso e **âmbito padrão** devem ser especificados.

    ![Screenshot que mostra o ecrã de serviço Add OAuth2.](./media/api-management-howto-oauth2/oauth-03.png)

    Para um servidor OAuth 2.0 do Azure Ative Directory, o URL do **ponto final token** terá o seguinte formato, onde `<TenantID>`  tem o formato de `yourapp.onmicrosoft.com` .

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    A definição predefinição dos **métodos de autenticação do Cliente** é **Básica,** e  **o método de envio de ficha de acesso** é **cabeçalho de autorização** . Estes valores estão configurados nesta secção do formulário, juntamente com o **âmbito Padrão** .

6. A secção **de credenciais do Cliente** contém o **ID** do Cliente e o **segredo do Cliente,** que são obtidos durante o processo de criação e configuração do seu servidor OAuth 2.0. Uma vez especificado o **ID do Cliente** e **o segredo do Cliente,** o **redirect_uri** para o código **de autorização** é gerado. Este URI é utilizado para configurar o URL de resposta na configuração do servidor OAuth 2.0.

    No novo portal de desenvolvimento, o sufixo URI é de forma:

    - `/signin-oauth/code/callback/{authServerName}` para o fluxo de concessão de código de autorização
    - `/signin-oauth/implicit/callback` para o fluxo de subvenção implícita

    ![Screenshot que mostra onde adicionar as credenciais do cliente para o novo serviço OAuth2.](./media/api-management-howto-oauth2/oauth-04.png)

    Se **os tipos de concessão de autorização** forem definidos como **palavra-passe do proprietário do recurso,** a secção **de credenciais de senha do proprietário de recursos** é utilizada para especificar essas credenciais; caso contrário, pode deixá-lo em branco.

    Assim que o formulário estiver completo, clique em **Criar** para guardar a configuração do servidor de autorização API Management OAuth 2.0. Uma vez guardada a configuração do servidor, pode configurar APIs para utilizar esta configuração, como mostrado na secção seguinte.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Configurar uma API para utilizar a autorização do utilizador OAuth 2.0

1. Clique **em APIs** a partir do menu **API Management** à esquerda.

    ![OAuth 2.0 APIs](./media/api-management-howto-oauth2/oauth-05.png)

2. Clique no nome da API desejada e clique em **Definições** . Percorra a secção **de Segurança** e, em seguida, verifique a caixa para **OAuth 2.0** .

    ![Definições OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecione o **servidor de Autorização** pretendido da lista de drop-down e clique em **Guardar** .

    ![Screenshot que realça o servidor de autorização selecionado e o botão Guardar.](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Legacy developer portal - teste a autorização do utilizador OAuth 2.0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Uma vez configurado o seu servidor de autorização OAuth 2.0 e configurado a sua API para usar esse servidor, pode testá-lo indo para o Portal do Desenvolvedor e chamando uma API. Clique no **portal developer (legado)** no menu superior a partir da página de visão geral da sua página **de visão geral** da azure API Management.

Clique em **APIs** no menu superior e selecione **Echo API** .

![API Eco][api-management-apis-echo-api]

> [!NOTE]
> Se tiver apenas uma API configurada ou visível para a sua conta, clicar em APIs leva-o diretamente para as operações dessa API.

Selecione a operação **de recurso GET,** clique em **Abrir Consola** e, em seguida, selecione código de **autorização** a partir do drop-down.

![Abrir consola][api-management-open-console]

Quando o **código de autorização** é selecionado, é apresentada uma janela pop-up com a forma de inscrição do provedor OAuth 2.0. Neste exemplo, o formulário de inscrição é fornecido pela Azure Ative Directory.

> [!NOTE]
> Se tiver pop-ups desativados, será solicitado para os ativar pelo navegador. Depois de os ativar, selecione novamente o **código de autorização** e o formulário de inscrição será apresentado.

![Iniciar sessão][api-management-oauth2-signin]

Uma vez que tenha assinado, os **cabeçalhos do Pedido** são preenchidos com um `Authorization : Bearer` cabeçalho que autoriza o pedido.

![Pedido de sinal de cabeçalho][api-management-request-header-token]

Neste ponto pode configurar os valores desejados para os parâmetros restantes, e submeter o pedido.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização da OAuth 2.0 e da API Management, consulte o seguinte vídeo e [artigo](api-management-howto-protect-backend-with-aad.md)de acompanhamento .

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
