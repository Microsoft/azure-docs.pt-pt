---
title: Acesso às APIs no local com Proxy de Aplicação AD Azure
description: O Azure Ative Directory's Application Proxy permite que as aplicações nativas acedam de forma segura às APIs e à lógica de negócio que hospeda no local ou em VMs na nuvem.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258987"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Acesso seguro às APIs no local com Proxy de Aplicação AD Azure

Você pode ter APIs de lógica de negócio correndo no local, ou hospedado em máquinas virtuais na nuvem. As suas aplicações nativas Android, iOS, Mac ou Windows precisam interagir com os pontos finais da API para utilizar dados ou fornecer interação do utilizador. O Azure AD Application Proxy e a [Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) permitem que as suas aplicações nativas acedam de forma segura às suas APIs no local. O Azure Ative Directory Application Proxy é uma solução mais rápida e segura do que abrir portas de firewall e controlar a autenticação e autorização na camada de aplicações.

Este artigo acompanha-o através da criação de uma solução de Proxy de aplicação AD Azure para hospedar um serviço web API que as aplicações nativas podem aceder.

## <a name="overview"></a>Descrição geral

O diagrama que se segue mostra uma forma tradicional de publicar APIs no local. Esta abordagem requer a abertura das portas de entrada 80 e 443.

![Acesso tradicional da API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

O seguinte diagrama mostra como pode usar o Azure AD Application Proxy para publicar as APIs de forma segura sem abrir portas de entrada:

![Acesso Azure AD Application Proxy API](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

O Azure AD Application Proxy forma a espinha dorsal da solução, funcionando como um ponto final público para acesso a API, e fornecendo autenticação e autorização. Pode aceder às suas APIs a partir de uma vasta gama de plataformas utilizando as bibliotecas da [Microsoft Authentication Library (MSAL).](../azuread-dev/active-directory-authentication-libraries.md)

Uma vez que a autenticação e autorização de procuração de aplicações Azure AD são construídas em cima do Azure AD, pode utilizar o Acesso Condicionado AD Azure para garantir que apenas dispositivos fidedignos podem aceder a APIs publicados através do Application Proxy. Utilize a Azure AD Join ou Azure AD Hybrid Unidos para desktops e Intune Managed para dispositivos. Também pode usufruir de funcionalidades do Azure Ative Directory Premium, como a Autenticação Multi-Factor AD Azure, e a segurança apoiada pela aprendizagem automática da [Azure Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar esta passagem, precisa:

- Acesso a um diretório do Azure, com uma conta que pode criar e registar aplicações
- A amostra web API e aplicativos de clientes nativos de [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Publicar a API através do Application Proxy

Para publicar uma API fora da sua intranet através do Application Proxy, segue o mesmo padrão que a publicação de aplicações web. Para mais informações, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através de Aplicação Proxy no Diretório Ativo Azure](application-proxy-add-on-premises-application.md).

Para publicar a API web SecretAPI através do Application Proxy:

1. Construa e publique a amostra do projeto SecretAPI como uma aplicação web ASP.NET no seu computador local ou intranet. Certifique-se de que pode aceder à aplicação web localmente.

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**. Em seguida, selecione **aplicações enterprise**.

1. No topo das aplicações da **Enterprise - Todas as aplicações** página, selecione Nova **aplicação**.

1. Na página **De adicionar uma aplicação,** selecione **aplicações no local**. Aparece a sua própria página **de aplicação no local.**

1. Se não tiver um Conector Proxy de aplicação instalado, será solicitado que o instale. Selecione **Download Application Proxy Connector** para descarregar e instalar o conector.

1. Uma vez instalado o Conector Proxy de aplicação, na página de aplicação Adicionar a sua própria página **de aplicação no local:**

   1. Ao lado **do Nome,** insira *SecretAPI*.

   1. Ao lado **do Url Interno,** insira o URL que utiliza para aceder à API a partir da sua intranet.

   1. Certifique-se de que **a pré-autenticação** está definida para **o Diretório Ativo Azure**.

   1. **Selecione Adicionar** no topo da página e esperar que a aplicação seja criada.

   ![Adicionar app API](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Nas **aplicações Da Empresa - Todas as aplicações,** selecione a aplicação **SecretAPI.**

1. Na página **SecretAPI - Visão geral,** selecione **Propriedades** na navegação à esquerda.

1. Não pretende que as APIs estejam disponíveis para os utilizadores finais no painel **MyApps,** por isso, desaperte **o Visible to Utilizadores** para **Não** na parte inferior da página **Propriedades** e, em seguida, selecione **Save**.

   ![Não é visível para os utilizadores](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Publicou a sua API web através do Azure AD Application Proxy. Agora, adicione utilizadores que possam aceder à aplicação.

1. Na página **SecretAPI - Visão geral,** selecione **Utilizadores e grupos** na navegação à esquerda.

1. Na página **de Utilizadores e grupos,** selecione **Adicionar utilizador**.

1. Na página **de atribuição Adicionar,** selecione **Utilizadores e grupos**.

1. Na página **de Utilizadores e grupos,** procure e selecione utilizadores que possam aceder à aplicação, incluindo pelo menos a si mesmo. Depois de selecionar todos os utilizadores, **selecione Select**.

   ![Selecione e atribua utilizador](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. De volta à página **'Adicionar Atribuição',** selecione **Atribuir**.

> [!NOTE]
> AS APIs que utilizam a Autenticação Integrada do Windows podem requerer [passos adicionais](./application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registe a app nativa e conceda acesso à API

As aplicações nativas são programas desenvolvidos para usar numa determinada plataforma ou dispositivo. Antes que a sua aplicação nativa possa ligar e aceder a uma API, tem de registá-la em Azure AD. Os passos seguintes mostram como registar uma aplicação nativa e dar-lhe acesso à API web que publicou através do Application Proxy.

Para registar a aplicação nativa AppProxyNativeAppSample:

1. Na **página** geral do Diretório Ativo Azure, selecione registos de **Aplicações,** e no topo do painel de registos da **App,** selecione **Novo registo**.

1. Na página **de registo de uma candidatura:**

   1. Under **Name**, *insitar AppProxyNativeAppSample*.

   1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**

   1. Em **URL de redirecionamento,** desça e selecione **cliente público (mobile & desktop)** e, em seguida, insira *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. Selecione **Registar-se** e aguarde que a aplicação seja registada com sucesso.

      ![Novo registo de aplicação](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Já registou a aplicação AppProxyNativeAppSample no Azure Ative Directory. Para dar acesso à sua aplicação nativa à API web SecretAPI:

1. Na página Azure Ative **Directory Overview**  >  **App Registrations,** selecione a aplicação **AppProxyNativeAppSample.**

1. Na página **AppProxyNativeAppSample,** selecione **permissões API** na navegação à esquerda.

1. Na página de permissões da **API,** selecione **Adicionar uma permissão**.

1. Na primeira página de **permissões da API do Pedido,** selecione as **APIs que a minha organização utiliza** e, em seguida, procure e selecione **SecretAPI**.

1. Na página de **permissões api** do pedido seguinte, selecione a caixa de verificação ao lado **de user_impersonation** e, em seguida, selecione **Permissões de adicionar**.

    ![Selecionar uma API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. De volta à página de permissões da **API,** pode selecionar o **consentimento de administração grant para Contoso** para evitar que outros utilizadores tenham de consentir individualmente a app.

## <a name="configure-the-native-app-code"></a>Configure o código de aplicação nativo

O último passo é configurar a aplicação nativa. O seguinte corte do ficheiro *Form1.cs* na aplicação de amostras NativeClient faz com que a biblioteca MSAL adquira o token para solicitar a chamada da API e anexá-lo como portador ao cabeçalho da aplicação.

   ```
   // Acquire Access Token from AAD for Proxy Application
 IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }
 
if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application
  
  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Para configurar a app nativa para ligar ao Azure Ative Directory e ligar para o API App Proxy, atualize os valores de espaço reservado no ficheiro *App.config* da aplicação de amostras NativeClient com valores da Azure AD:

- Cole a identificação do **Diretório (inquilino)** no `<add key="ida:Tenant" value="" />` campo. Pode encontrar e copiar este valor (a GUID) a partir da página **de visão geral** de qualquer uma das suas aplicações.

- Cole o ID da Aplicação AppProxyNativeAppSample **(cliente)** no `<add key="ida:ClientId" value="" />` campo. Pode encontrar e copiar este valor (a GUID) a partir da página De **visão geral** do AppProxyNativeAppSample.

- Cole o AppProxyNativeAppSample **Redirect URI** no `<add key="ida:RedirectUri" value="" />` campo. Pode encontrar e copiar este valor (a URI) a partir da página appProxyNativeAppSample **Authentication.**

- Cole o **ID URI da aplicação** SecretAPI no `<add key="todo:TodoListResourceId" value="" />` campo. Pode encontrar e copiar este valor (a URI) a partir da página SecretAPI Expor uma página **API.**

- Cole o URL **da página inicial** da SecretAPI no `<add key="todo:TodoListBaseAddress" value="" />` campo. Pode encontrar e copiar este valor (um URL) a partir da página de **Branding** SecretAPI.

Depois de configurar os parâmetros, construa e execute a aplicação nativa. Quando seleciona o botão **'Iniciar',** a aplicação permite-lhe iniciar sposição e, em seguida, apresenta um ecrã de sucesso para confirmar que está ligado com sucesso ao SecretAPI.

![A screenshot mostra uma mensagem Secret A P I Successful e um botão OK.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Adicionar uma aplicação no local para acesso remoto através de Aplicação Proxy em Diretório Ativo Azure](application-proxy-add-on-premises-application.md)
- [Quickstart: Configurar uma aplicação do cliente para aceder a APIs web](../develop/quickstart-configure-app-access-web-apis.md)
- [Como permitir que as aplicações de clientes nativos interajam com aplicações de procuração](application-proxy-configure-native-client-application.md)