---
title: Acesso no local apis com procuração de aplicação ad azure
description: A Aplicação Proxy do Azure Ative Directory permite que as aplicações nativas acedam de forma segura a APIs e à lógica empresarial que acolhe no local ou em VMs em nuvem.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: has-adal-ref
ms.openlocfilehash: 74c6951a718d15a9ca7b84e92662272ba1bfd182
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610297"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Acesso seguro a APIs no local com procuração de aplicação ad-a.azure

Você pode ter a lógica de negócio apis funcionando no local, ou hospedado em máquinas virtuais na nuvem. As aplicações nativas do Android, iOS, Mac ou Windows precisam de interagir com os pontos finais da API para utilizar dados ou fornecer interação ao utilizador. A Procuração de Aplicações Azure AD e as Bibliotecas de [Autenticação de Diretórios Ativos Azure (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) permitem que as suas aplicações nativas acedam de forma segura às suas APIs no local. A Procuração de Aplicações de Diretório Ativo Azure é uma solução mais rápida e segura do que abrir portas de firewall e controlar a autenticação e autorização na camada da aplicação.

Este artigo acompanha-o através da criação de uma solução de Proxy de Aplicação AD Azure para hospedar um serviço Web API a que as aplicações nativas podem aceder.

## <a name="overview"></a>Descrição geral

O diagrama seguinte mostra uma forma tradicional de publicar APIs no local. Esta abordagem requer a abertura dos portos 80 e 443.

![Acesso tradicional da API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

O diagrama seguinte mostra como pode utilizar o Proxy de Aplicação AD Azure para publicar apis de forma segura sem abrir quaisquer portas de entrada:

![Acesso à APi proxy proxy da aplicação Azure AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

O Procurador de Aplicação Azure AD forma a espinha dorsal da solução, funcionando como ponto final público para o acesso à API, e fornecendo autenticação e autorização. Pode aceder às suas APIs a partir de um vasto conjunto de plataformas utilizando as bibliotecas [ADAL.](/azure/active-directory/develop/active-directory-authentication-libraries)

Uma vez que a autenticação e autorização da Aplicação AD Azure proxy são construídas em cima do Azure AD, pode utilizar o Acesso Condicional Azure AD para garantir que apenas dispositivos fidedignos podem aceder a APIs publicados através do Application Proxy. Utilize azure AD Join ou Azure AD Hybrid Unidos para desktops e Intune Managed para dispositivos. Também pode usufruir de funcionalidades Azure Ative Directory Premium como a Autenticação Multi-Factor Azure, e a segurança apoiada por machine learning da [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este passeio, precisa de:

- Acesso a administrador a um diretório Azure, com uma conta que pode criar e registar apps
- A amostra web API e aplicativos de clientes nativos de[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Publicar a API através de Procuração de Aplicações

Para publicar uma API fora da sua intranet através do Application Proxy, segue o mesmo padrão que a publicação de aplicações web. Para mais informações, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através do Proxy de Aplicação no Diretório Ativo Azure](application-proxy-add-on-premises-application.md).

Para publicar a Web API secretAPI através do Proxy de Aplicação:

1. Construa e publique o projeto SecretAPI como uma ASP.NET aplicação web no seu computador ou intranet local. Certifique-se de que pode aceder à aplicação web localmente.

1. No [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory**. Em seguida, selecione **aplicações Enterprise**.

1. No topo das **aplicações da Enterprise - Todas as aplicações,** selecione **Nova aplicação**.

1. Na página Adicionar uma página de **aplicação,** selecione **aplicações No local**. Aparece a sua própria página **de candidatura no local.**

1. Se não tiver instalado um Conector proxy de aplicação, será solicitado a instalá-lo. Selecione **Descarregue o Conector proxy da aplicação** para descarregar e instalar o conector.

1. Uma vez instalado o Conector proxy de aplicação, na página de aplicação Adicionar a sua própria página **de aplicação no local:**

   1. Ao lado do **Nome,** insira *SecretAPI.*

   1. Ao lado do **Url Interno,** introduza o URL utilizado para aceder à API a partir da sua intranet.

   1. Certifique-se de que a **pré-autenticação** está definida para o **Diretório Ativo Azure**.

   1. Selecione **Adicionar** no topo da página e esperar pela criação da aplicação.

   ![Adicionar app API](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Na página de **aplicações da Enterprise - Todas as aplicações,** selecione a aplicação **SecretAPI.**

1. Na página **SecretAPI - Resumo,** selecione **Propriedades** na navegação à esquerda.

1. Não pretende que as APIs estejam disponíveis para os utilizadores finais no painel **MyApps,** por isso, detete **te visível para os utilizadores** para **Não** na parte inferior da página **Properties** e, em seguida, selecione **Guardar**.

   ![Não é visível para os utilizadores](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Publicou a sua Web API através do Procurador de Aplicação AD Azure. Agora, adicione utilizadores que possam aceder à aplicação.

1. Na página **SecretAPI - Resumo,** selecione **Utilizadores e grupos** na navegação à esquerda.

1. Na página **Utilizadores e grupos,** selecione **Adicionar utilizador**.

1. Na página de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

1. Na página **utilizadores e grupos,** procure e selecione utilizadores que possam aceder à aplicação, incluindo pelo menos a si mesmo. Depois de selecionar todos os utilizadores, selecione **Selecione**.

   ![Selecione e atribua o utilizador](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. De volta à página **de Atribuição de Adicionar,** selecione **Atribuir**.

> [!NOTE]
> APIs que utilizam autenticação integrada do Windows podem requerer [passos adicionais](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registe a app nativa e conceda acesso à API

Aplicações nativas são programas desenvolvidos para usar numa determinada plataforma ou dispositivo. Antes que a sua aplicação nativa possa ligar e aceder a uma API, deve registá-la em Azure AD. Os seguintes passos mostram como registar uma aplicação nativa e dar-lhe acesso à API web que publicou através do Application Proxy.

Para registar a aplicação nativa AppProxyNativeAppSample:

1. Na página de **Visão Geral** do Diretório Ativo Azure, selecione registos de **Aplicações,** e no topo do painel de inscrições da **App,** selecione **Nova inscrição**.

1. No Registo uma página **de inscrição:**

   1. Em **nome**, *introduza AppProxyNativeAppSample*.

   1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .

   1. Em **Redirecionamento URL,** desça e selecione **cliente público (mobile & desktop)** e, em seguida, *introduza https:\//appproxynativeapp*.

   1. Selecione **Register**e aguarde que a aplicação seja registada com sucesso.

      ![Novo registo de aplicação](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Já registou a aplicação AppProxyNativeAppSample no Diretório Ativo Azure. Para dar acesso à sua aplicação nativa à API web SecretAPI:

1. Na página de Registos de**Aplicações** de **Visão** > Geral do Diretório Ativo do Azure, selecione a aplicação **AppProxyNativeAppSample.**

1. Na página **AppProxyNativeAppSample,** selecione **permissões API** na navegação à esquerda.

1. Na página de **permissões da API,** selecione **Adicionar uma permissão**.

1. Na primeira página de **permissões Request API,** selecione as **APIs** que a minha organização usa aba e, em seguida, procure e selecione **SecretAPI**.

1. Na página de **permissões API do pedido** seguinte, selecione a caixa de verificação ao lado **de user_impersonation**, e, em seguida, selecione **Adicionar permissões**.

    ![Selecionar uma API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. De volta à página de **permissões da API,** pode selecionar o consentimento do **administrador grant para Contoso** para evitar que outros utilizadores tenham de consentir individualmente com a app.

## <a name="configure-the-native-app-code"></a>Configure o código de aplicações nativo

O último passo é configurar a aplicação nativa. O seguinte corte do ficheiro *Form1.cs* na aplicação de amostra NativeClient faz com que a biblioteca ADAL adquira o símbolo para solicitar a chamada da API e anexe-a como portadora do cabeçalho da aplicação.

   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));

       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");

       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```

Para configurar a aplicação nativa para ligar ao Azure Ative Directory e ligar para a API App Proxy, atualize os valores do espaço reservado no ficheiro *App.config* da aplicação de amostra NativeClient com valores do Azure AD:

- Colar a identificação do **Diretório (inquilino)** no `<add key="ida:Tenant" value="" />` terreno. Pode encontrar e copiar este valor (um GUID) a partir da página **de visão geral** de qualquer uma das suas aplicações.

- Colar o ID appProxyNativeAppSample **Application (cliente)** no `<add key="ida:ClientId" value="" />` campo. Pode encontrar e copiar este valor (um GUID) a partir da página de **visão geral** da AppProxyNativeAppSample.

- Colar o AppProxyNativeAppSample **Redirecionamento URI** no `<add key="ida:RedirectUri" value="" />` campo. Pode encontrar e copiar este valor (um URI) na página de **autenticação** AppProxyNativeAppSample.

- Colhe o **ID DE APLICAÇÃO** SecretAPI URI no `<add key="todo:TodoListResourceId" value="" />` campo. Pode encontrar e copiar este valor (um URI) a partir do SecretAPI Expor uma página **aPI.**

- Colhe o URL da **página** `<add key="todo:TodoListBaseAddress" value="" />` inicial secretano no campo. Pode encontrar e copiar este valor (um URL) na página de **Branding** SecretAPI.

Depois de configurar os parâmetros, construa e execute a aplicação nativa. Quando seleciona o botão **Sign In,** a aplicação permite-lhe iniciar sessão e, em seguida, exibe um ecrã de sucesso para confirmar que está ligado com sucesso ao SecretAPI.

![Êxito](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Adicione um pedido no local para acesso remoto através de Application Proxy no Diretório Ativo Azure](application-proxy-add-on-premises-application.md)
- [Quickstart: Configure uma aplicação de cliente para aceder a APIs web](../develop/quickstart-configure-app-access-web-apis.md)
- [Como permitir que as aplicações de clientes nativos interajam com aplicações de procuração](application-proxy-configure-native-client-application.md)
