---
title: Autorizar contas de programador com o OAuth 2.0 na gestão de API do Azure | Documentos da Microsoft
description: Saiba como autorizar os utilizadores com o OAuth 2.0 na gestão de API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: b195271edeea6cd5ea527454ad1615ac85a32138
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658688"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de programador na gestão de API do Azure com o OAuth 2.0

Suportam a várias APIs [OAuth 2.0](https://oauth.net/2/) para proteger a API e certifique-se de que apenas os utilizadores válidos têm acesso, e apenas podem aceder a recursos aos quais estão qualificados. Para utilizar a consola de programador interativo da gestão de API do Azure com essas APIs, o serviço permite-lhe configurar a sua instância de serviço para trabalhar com o OAuth 2.0 API ativada.

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar a sua instância do serviço de gestão de API para utilizar a autorização de OAuth 2.0 para contas de desenvolvedor, mas não mostra como configurar um fornecedor de OAuth 2.0. A configuração para cada fornecedor de OAuth 2.0 é diferente, embora os passos são semelhantes, e as partes necessárias de informações utilizadas na configuração do OAuth 2.0 na sua instância do serviço de gestão de API são os mesmos. Este tópico mostra exemplos que utilizam o Azure Active Directory como um provedor de OAuth 2.0.

> [!NOTE]
> Para obter mais informações sobre como configurar o OAuth 2.0 com o Azure Active Directory, consulte a [WebApp-Graph-DotNet] [ WebApp-GraphAPI-DotNet] exemplo.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Configurar um servidor de autorização de OAuth 2.0 na gestão de API

> [!NOTE]
> Se ainda não criou uma instância de serviço de gestão de API, consulte [criar uma instância de serviço de gestão de API][Create an API Management service instance].

1. Clique no separador de OAuth 2.0, no menu à esquerda e clique em **+ adicionar**.

    ![Menu de OAuth 2.0](./media/api-management-howto-oauth2/oauth-01.png)

2. Introduza um nome e uma descrição opcional na **Name** e **Descrição** campos.

    > [!NOTE]
    > Estes campos são utilizados para identificar o servidor de autorização de OAuth 2.0 dentro da instância de serviço de gestão de API atual e o servidor de OAuth 2.0 não pertencem à seus valores.

3. Introduza o **URL de página de registo de cliente**. Esta página é onde os utilizadores podem criar e gerir as suas contas e varia consoante o fornecedor de OAuth 2.0 utilizado. O **URL de página de registo de cliente** aponta para a página que os utilizadores podem utilizar para criar e configurar suas próprias contas para fornecedores de OAuth 2.0 que suportam a gestão de utilizadores de contas. Algumas organizações não configurar nem utilizar esta funcionalidade, mesmo que o fornecedor de OAuth 2.0 oferece suporte a ele. Se o seu fornecedor de OAuth 2.0 não tem a gestão de utilizadores de contas configuradas, introduza um URL de marcador de posição aqui, como o URL da sua empresa ou um URL como `https://placeholder.contoso.com`.

    ![Novo servidor do OAuth 2.0](./media/api-management-howto-oauth2/oauth-02.png)

4. A secção seguinte do formulário contém os **tipos de concessão de autorização**, **URL de ponto final de autorização**, e **método de pedido de autorização** definições.

    Especifique a **tipos de concessão de autorização** verificando os tipos de pretendido. **Código de autorização** é especificado por predefinição.

    Introduza o **URL de ponto final de autorização**. Para o Azure Active Directory, este URL será semelhante ao URL seguinte, onde `<client_id>` é substituído com o id de cliente que identifica a sua aplicação para o servidor de OAuth 2.0.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    O **método de pedido de autorização** Especifica como o pedido de autorização é enviado para o servidor de OAuth 2.0. Por predefinição **obter** está selecionada.

5. Em seguida, **URL de ponto final do Token**, **métodos de autenticação de cliente**, **token de acesso de método de envio** e **predefinido âmbito** têm de ser especificado.

    ![Novo servidor do OAuth 2.0](./media/api-management-howto-oauth2/oauth-03.png)

    Para um servidor do Azure Active Directory OAuth 2.0, o **URL de ponto final do Token** tem de ter o formato seguinte, onde `<TenantID>` tem o formato de `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    A definição de predefinida **métodos de autenticação de cliente** é **básica**, e **token de acesso de método de envio** é **cabeçalho de autorização**. Estes valores são configurados nesta secção do formulário, juntamente com o **predefinição âmbito**.

6. O **credenciais de cliente** secção contém os **ID de cliente** e **segredo do cliente**, que é obtido durante o processo de criação e configuração do seu servidor de OAuth 2.0 . Uma vez a **ID de cliente** e **segredo do cliente** forem especificados, o **redirect_uri** para o **código de autorização** é gerado. Este URI é utilizado para configurar o URL de resposta na sua configuração de servidor de OAuth 2.0.

    ![Novo servidor do OAuth 2.0](./media/api-management-howto-oauth2/oauth-04.png)

    Se **tipos de concessão de autorização** está definida como **palavra-passe de proprietário do recurso**, o **credenciais de palavra-passe de proprietário do recurso** secção é usado para especificar essas credenciais; caso contrário, Pode deixar em branco.

    Quando o formulário estiver concluído, clique em **criar** para guardar a configuração de servidor de autorização de API Management OAuth 2.0. Depois da configuração do servidor é guardada, pode configurar as APIs para utilizar esta configuração, conforme mostrado na próxima seção.

## <a name="step2"> </a>Configurar uma API para utilizar a autorização de utilizador do OAuth 2.0

1. Clique em **APIs** partir do **gestão de API** menu à esquerda.

    ![APIs de OAuth 2.0](./media/api-management-howto-oauth2/oauth-05.png)

2. Clique no nome da API do pretendido e clique em **definições**. Desloque-se para o **Security** secção e, em seguida, selecione a caixa para **OAuth 2.0**.

    ![Definições de OAuth 2.0](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecione o desejado **servidor de autorização** da lista pendente e clique em **guardar**.

    ![Definições de OAuth 2.0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Testar a autorização de utilizador do OAuth 2.0 no Portal do Programador

Depois de ter configurado o seu servidor de autorização de OAuth 2.0 e configurado a sua API para utilizar esse servidor, pode testá-lo ao aceder ao Portal do programador e chamar uma API.  Clique em **portal do programador** no menu superior da sua instância de gestão de API do Azure **descrição geral** página.

![Portal do programador][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API eco**.

![API Eco][api-management-apis-echo-api]

> [!NOTE]
> Se tiver apenas uma API configurada ou visível para a sua conta, clicar em APIs leva-o diretamente para as operações dessa API.

Selecione o **recurso GET** operação, clique em **abra a consola do**e, em seguida, selecione **código de autorização** na lista suspensa.

![Abrir consola][api-management-open-console]

Quando **código de autorização** é selecionado, é apresentada uma janela de pop-up com o formulário de início de sessão do fornecedor OAuth 2.0. Neste exemplo, o formulário de início de sessão é fornecido pelo Azure Active Directory.

> [!NOTE]
> Se tiver desativados de pop-ups será solicitado para ativá-las pelo navegador. Depois de ativá-los, selecione **código de autorização** novamente e será apresentado o formulário de início de sessão.

![Iniciar sessão][api-management-oauth2-signin]

Assim que tiver iniciado sessão, o **cabeçalhos de pedido** são preenchidos com um `Authorization : Bearer` cabeçalho que autoriza o pedido.

![Token do cabeçalho do pedido][api-management-request-header-token]

Neste momento pode configurar os valores pretendidos para os parâmetros restantes e submeter o pedido.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar o OAuth 2.0 e a gestão de API, consulte o seguinte vídeo e que acompanha este artigo [artigo](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
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

