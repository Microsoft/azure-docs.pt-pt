---
title: Autorizar contas de desenvolvedor usando o OAuth 2,0 no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como autorizar usuários usando o OAuth 2,0 no gerenciamento de API.
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
ms.openlocfilehash: 99a49aa4627dc23d5f7531ac961d63e3e75ccff9
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176624"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o OAuth 2,0 no gerenciamento de API do Azure

Muitas APIs dão suporte ao [OAuth 2,0](https://oauth.net/2/) para proteger a API e garantir que somente usuários válidos tenham acesso e possam acessar apenas os recursos aos quais eles estão qualificados. Para usar o console de desenvolvedor interativo do gerenciamento de API do Azure com essas APIs, o serviço permite que você configure sua instância de serviço para trabalhar com sua API habilitada para OAuth 2,0.

> [!IMPORTANT]
> A autorização do OAuth 2,0 ainda não está disponível no console interativo do novo portal do desenvolvedor.

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância do serviço de gerenciamento de API para usar a autorização OAuth 2,0 para contas de desenvolvedor, mas não mostra como configurar um provedor OAuth 2,0. A configuração para cada provedor OAuth 2,0 é diferente, embora as etapas sejam semelhantes, e as informações necessárias usadas na configuração do OAuth 2,0 na instância do serviço de gerenciamento de API são as mesmas. Este tópico mostra exemplos usando Azure Active Directory como um provedor OAuth 2,0.

> [!NOTE]
> Para obter mais informações sobre como configurar o OAuth 2,0 usando Azure Active Directory, consulte o exemplo [webapp-GraphAPI-dotnet][WebApp-GraphAPI-DotNet] .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Configurar um servidor de autorização OAuth 2,0 no gerenciamento de API

> [!NOTE]
> Se você ainda não criou uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][Create an API Management service instance].

1. Clique na guia OAuth 2,0 no menu à esquerda e clique em **+ Adicionar**.

    ![Menu OAuth 2,0](./media/api-management-howto-oauth2/oauth-01.png)

2. Insira um nome e uma descrição opcional nos campos **nome** e **Descrição** .

    > [!NOTE]
    > Esses campos são usados para identificar o servidor de autorização OAuth 2,0 na instância atual do serviço de gerenciamento de API e seus valores não são provenientes do servidor OAuth 2,0.

3. Insira a **URL da página de registro do cliente**. Esta página é onde os usuários podem criar e gerenciar suas contas e varia dependendo do provedor OAuth 2,0 usado. A **URL da página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2,0 que dão suporte ao gerenciamento de contas do usuário. Algumas organizações não configuram nem usam essa funcionalidade, mesmo se o provedor OAuth 2,0 oferecer suporte a ela. Se o seu provedor OAuth 2,0 não tiver o gerenciamento de contas configurado pelo usuário, insira uma URL de espaço reservado aqui, como a URL da sua empresa, ou uma URL como `https://placeholder.contoso.com`.

    ![Novo servidor OAuth 2,0](./media/api-management-howto-oauth2/oauth-02.png)

4. A próxima seção do formulário contém os **tipos de concessão de autorização**, a URL do ponto de extremidade de **autorização**e as configurações do método de **solicitação de autorização** .

    Especifique os **tipos de concessão de autorização** verificando os tipos desejados. O **código de autorização** é especificado por padrão.

    Insira a **URL do ponto de extremidade de autorização**. Por Azure Active Directory, essa URL será semelhante à URL a seguir, em que `<tenant_id>` é substituído pela ID do seu locatário do Azure AD.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    O **método de solicitação de autorização** especifica como a solicitação de autorização é enviada para o servidor OAuth 2,0. Por padrão, **Get** está selecionado.

5. Em seguida **, URL do ponto de extremidade do token**, métodos de autenticação de **cliente**, método de envio de **token de acesso** e **escopo padrão** precisam ser especificados.

    ![Novo servidor OAuth 2,0](./media/api-management-howto-oauth2/oauth-03.png)

    Para um Azure Active Directory servidor OAuth 2,0, a **URL do ponto de extremidade do token** terá o seguinte formato, em que `<TenantID>` tem o formato de `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    A configuração padrão para **métodos de autenticação de cliente** é **básica**e o método de envio de **token de acesso** é o cabeçalho de **autorização**. Esses valores são configurados nesta seção do formulário, juntamente com o **escopo padrão**.

6. A seção **credenciais do cliente** contém a ID do **cliente** e o segredo do **cliente**, que são obtidos durante o processo de criação e configuração do seu servidor OAuth 2,0. Depois que **a ID do cliente** e o segredo do **cliente** são especificados, o **redirect_uri** para o **código de autorização** é gerado. Esse URI é usado para configurar a URL de resposta na configuração do servidor OAuth 2,0.

    ![Novo servidor OAuth 2,0](./media/api-management-howto-oauth2/oauth-04.png)

    Se os **tipos de concessão de autorização** estiverem definidos como senha de proprietário do **recurso**, a seção credenciais de senha do **proprietário do recurso** será usada para especificar essas credenciais; caso contrário, você pode deixá-lo em branco.

    Quando o formulário for concluído, clique em **criar** para salvar a configuração do servidor de autorização OAuth 2,0 de gerenciamento de API. Depois que a configuração do servidor for salva, você poderá configurar APIs para usar essa configuração, conforme mostrado na próxima seção.

## <a name="step2"> </a>Configurar uma API para usar a autorização de usuário OAuth 2,0

1. Clique em **APIs** no menu **Gerenciamento de API** à esquerda.

    ![APIs do OAuth 2,0](./media/api-management-howto-oauth2/oauth-05.png)

2. Clique no nome da API desejada e clique em **configurações**. Role até a seção **segurança** e marque a caixa para **OAuth 2,0**.

    ![Configurações do OAuth 2,0](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecione o **servidor de autorização** desejado na lista suspensa e clique em **salvar**.

    ![Configurações do OAuth 2,0](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Portal do desenvolvedor herdado-testar a autorização do usuário do OAuth 2,0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Depois de configurar seu servidor de autorização OAuth 2,0 e configurar sua API para usar esse servidor, você pode testá-lo acessando o portal do desenvolvedor e chamando uma API. Clique em **portal do desenvolvedor (Herdado)** no menu superior da página **visão geral** da instância do gerenciamento de API do Azure.

Clique em **APIs** no menu superior e selecione **API de eco**.

![API Eco][api-management-apis-echo-api]

> [!NOTE]
> Se tiver apenas uma API configurada ou visível para a sua conta, clicar em APIs leva-o diretamente para as operações dessa API.

Selecione a operação **obter recurso** , clique em **Abrir console**e, em seguida, selecione **código de autorização** na lista suspensa.

![Abrir consola][api-management-open-console]

Quando o **código de autorização** é selecionado, uma janela pop-up é exibida com a forma de entrada do provedor OAuth 2,0. Neste exemplo, o formulário de entrada é fornecido por Azure Active Directory.

> [!NOTE]
> Se você tiver pop-ups desabilitados, será solicitado a habilitá-los pelo navegador. Depois de habilitá-los, selecione **código de autorização** novamente e o formulário de entrada será exibido.

![Iniciar sessão][api-management-oauth2-signin]

Depois de entrar, os cabeçalhos de **solicitação** são populados com um cabeçalho de `Authorization : Bearer` que autoriza a solicitação.

![Token de cabeçalho de solicitação][api-management-request-header-token]

Neste ponto, você pode configurar os valores desejados para os parâmetros restantes e enviar a solicitação.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como usar o OAuth 2,0 e o gerenciamento de API, consulte o vídeo a seguir e o [artigo](api-management-howto-protect-backend-with-aad.md)que o acompanha.

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

