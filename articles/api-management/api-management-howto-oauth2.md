---
title: Autorizar contas de desenvolvimento usando OAuth 2.0 na Gestão API
titleSuffix: Azure API Management
description: Saiba como autorizar utilizadores que utilizem o OAuth 2.0 na Gestão API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 90c890925378c30ce5688d2713990b4b2cdd20c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430681"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de desenvolvimento utilizando o OAuth 2.0 na Azure API Management

Muitas APIs suportam o [OAuth 2.0](https://oauth.net/2/) para garantir a API e garantir que apenas os utilizadores válidos têm acesso, e só podem aceder a recursos a que têm direito. Para utilizar a consola interativa de desenvolvimento da Azure API Management com tais APIs, o serviço permite configurar a sua instância de serviço para trabalhar com a sua API Ativada OAuth 2.0.

> [!IMPORTANT]
> A autorização OAuth 2.0 ainda não está disponível na consola interativa do novo portal de desenvolvimento.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Pré-requisitos

Este guia mostra-lhe como configurar a sua instância de serviço de Gestão API para utilizar a autorização OAuth 2.0 para contas de desenvolvedor, mas não lhe mostra como configurar um fornecedor OAuth 2.0. A configuração para cada fornecedor OAuth 2.0 é diferente, embora os passos sejam semelhantes, e as peças de informação necessárias utilizadas na configuração do OAuth 2.0 na sua instância de serviço de Gestão API são as mesmas. Este tópico apresenta exemplos usando o Azure Ative Directory como fornecedor oAuth 2.0.

> [!NOTE]
> Para obter mais informações sobre a configuração do OAuth 2.0 utilizando o Diretório Ativo Do Azure, consulte a amostra [WebApp-GraphAPI-DotNet.][WebApp-GraphAPI-DotNet]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Configure um servidor de autorização OAuth 2.0 na Gestão API

> [!NOTE]
> Se ainda não criou uma instância de serviço de Gestão API, consulte Criar uma instância de serviço de [Gestão API][Create an API Management service instance].

1. Clique no separador OAuth 2.0 no menu à esquerda e clique em **+Add**.

    ![Menu OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Introduza um nome e uma descrição opcional nos campos **Nome** e **Descrição.**

    > [!NOTE]
    > Estes campos são utilizados para identificar o servidor de autorização OAuth 2.0 dentro da atual instância de serviço de Gestão API e os seus valores não provêm do servidor OAuth 2.0.

3. Insira o URL da página de **registo do Cliente**. Esta página é onde os utilizadores podem criar e gerir as suas contas, e varia consoante o provedor OAuth 2.0 utilizado. O URL da página de **registo do Cliente** aponta para a página que os utilizadores podem usar para criar e configurar as suas próprias contas para os fornecedores OAuth 2.0 que suportam a gestão de contas do utilizador. Algumas organizações não configuram ou utilizam esta funcionalidade mesmo que o fornecedor OAuth 2.0 a suporte. Se o seu fornecedor OAuth 2.0 não tiver a gestão do utilizador das contas configuradas, introduza `https://placeholder.contoso.com`aqui um URL de espaço reservado, como o URL da sua empresa, ou um URL como .

    ![OAuth 2.0 novo servidor](./media/api-management-howto-oauth2/oauth-02.png)

4. A secção seguinte do formulário contém os tipos de **subvenção de autorização,** URL de ponto final de **autorização,** e definições de método de pedido de **autorização.**

    Especifique os tipos de **subvenção** de autorização verificando os tipos pretendidos. **O código** de autorização é especificado por defeito.

    Introduza o URL do **ponto final**da Autorização . Para o Azure Ative Directory, este URL será `<tenant_id>` semelhante ao seguinte URL, onde é substituído pela identificação do seu inquilino Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    O método de pedido de **autorização** especifica como o pedido de autorização é enviado para o servidor OAuth 2.0. Por predefinição é selecionado **O GET.**

5. Em seguida, **TOKen endpoint URL,** métodos de **autenticação do cliente,** Método de **envio de fichas** de acesso e **âmbito padrão** devem ser especificados.

    ![OAuth 2.0 novo servidor](./media/api-management-howto-oauth2/oauth-03.png)

    Para um servidor Azure Ative Directory OAuth 2.0, o URL de `<TenantID>` ponto final `yourapp.onmicrosoft.com` **token** terá o seguinte formato, onde tem o formato de .

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    A definição padrão para métodos de **autenticação do Cliente** é **Básica,** e o método de envio de **fichas** de acesso é **cabeçalho de autorização**. Estes valores estão configurados nesta secção do formulário, juntamente com o **âmbito Predefinido**.

6. A secção **de credenciais do Cliente** contém o ID do **Cliente** e o segredo do **Cliente,** que são obtidos durante o processo de criação e configuração do seu servidor OAuth 2.0. Uma vez especificado o ID do **Cliente** e o segredo do **Cliente,** gera-se a **redirect_uri** para o código de **autorização.** Este URI é utilizado para configurar o URL de resposta na configuração do servidor OAuth 2.0.

    ![OAuth 2.0 novo servidor](./media/api-management-howto-oauth2/oauth-04.png)

    Se os tipos de **concessão** de autorização forem definidos para a **palavra-passe do proprietário do Recurso,** a secção de **credenciais** de senha do proprietário do Recurso é usada para especificar essas credenciais; caso contrário, pode deixá-lo em branco.

    Assim que o formulário estiver completo, clique em **Criar** para salvar a configuração do servidor de autorização API Management OAuth 2.0. Uma vez que a configuração do servidor é guardada, pode configurar APIs para utilizar esta configuração, como mostrado na secção seguinte.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Configure uma API para utilizar a autorização de utilizador oAuth 2.0

1. Clique em **APIs** no menu **de Gestão API** à esquerda.

    ![OAuth 2.0 APIs](./media/api-management-howto-oauth2/oauth-05.png)

2. Clique no nome da API desejada e clique em **Definições**. Percorra a secção de **Segurança** e, em seguida, verifique a caixa para **OAuth 2.0**.

    ![Definições OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecione o servidor de **Autorização** desejado a partir da lista de lançamentos e clique em **Guardar**.

    ![Definições OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Portal de desenvolvimento legado - teste a autorização de utilizador OAuth 2.0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Depois de configurar o seu servidor de autorização OAuth 2.0 e configurar a Sua API para utilizar esse servidor, pode testá-lo indo ao Portal do Desenvolvimento e chamando uma API. Clique no **portal developer (legado)** no menu superior da sua página de **visão geral** da instância de Gestão API Azure.

Clique em **APIs** no menu superior e selecione **Echo API**.

![API Eco][api-management-apis-echo-api]

> [!NOTE]
> Se tiver apenas uma API configurada ou visível para a sua conta, clicar em APIs leva-o diretamente para as operações dessa API.

Selecione a operação **GET Resource,** clique em **Open Console**e, em seguida, selecione código **de autorização** a partir da queda.

![Abrir consola][api-management-open-console]

Quando o código de **autorização** é selecionado, é apresentada uma janela pop-up com a forma de inscrição do fornecedor OAuth 2.0. Neste exemplo, o formulário de entrada é fornecido pelo Azure Ative Directory.

> [!NOTE]
> Se tiver pop-ups desativados, será solicitado a os ativar pelo navegador. Depois de os ativar, selecione novamente **o código de autorização** e será apresentado o formulário de inscrição.

![Iniciar sessão][api-management-oauth2-signin]

Uma vez assinado, os **cabeçalhos do** `Authorization : Bearer` Pedido são preenchidos com um cabeçalho que autoriza o pedido.

![Peça ficha cabeçalho de pedido][api-management-request-header-token]

Neste ponto pode configurar os valores desejados para os restantes parâmetros e submeter o pedido.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a utilização da OAuth 2.0 e da API Management, consulte o seguinte vídeo e [artigo](api-management-howto-protect-backend-with-aad.md)de acompanhamento.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

