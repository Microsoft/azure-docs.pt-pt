---
title: Acesso no local APIs com o Proxy de aplicações do Azure AD
description: Proxy de aplicações do Azure do Active Directory permite aceder a APIs de forma segura de aplicações nativas e lógica de negócio que alojar no local ou em VMs na cloud.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 47f6678f8d18d734176d964f18a6febecea957ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481418"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Proteger o acesso a APIs no local com o Proxy de aplicações do Azure AD

Pode ter a lógica de negócios APIs em execução no local ou alojado em máquinas virtuais na cloud. As suas aplicações Android, iOS, Mac ou Windows nativas precisam interagir com os pontos finais de API para utilizar os dados ou fornecer a interação do utilizador. Proxy de aplicações do Azure AD e o [do Azure Active Directory Authentication bibliotecas (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) let suas aplicações nativas acessem em segurança as suas APIs no local. Proxy de aplicação do Active Directory do Azure é uma solução mais rápida e mais segura do que a abertura de portas de firewall e controlar a autenticação e autorização ao nível da aplicação. 

Este artigo orienta-o pela configuração de uma solução de Proxy de aplicações do Azure AD para hospedar um serviço de web API que podem aceder a aplicações nativas. 

## <a name="overview"></a>Descrição geral

O diagrama seguinte mostra uma forma tradicional para publicar APIs no local. Esta abordagem requer a abertura de portas de entrada 80 e 443.

![Acesso de API tradicional](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

O diagrama seguinte mostra como pode utilizar o Proxy de aplicações do Azure AD para publicar APIs de forma segura sem abrir as portas de entrada:

![Acesso de API de Proxy de aplicação do AD do Azure](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

O Proxy de aplicações do Azure AD constitui o backbone da solução, trabalhando como um ponto final público para o acesso à API e fornecer autenticação e autorização. Pode aceder as suas APIs a partir de uma grande diversidade de plataformas ao utilizar o [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) bibliotecas. 

Uma vez que a autenticação de Proxy de aplicações do Azure AD e a autorização são criados sobre o Azure AD, pode utilizar o acesso condicional do Azure AD para garantir que apenas dispositivos fidedignos podem aceder a APIs publicadas através do Proxy de aplicações. Utilize associação do Azure AD ou Azure AD híbrido associado para desktops e geridos pelo Intune em dispositivos. Também pode tirar partido das funcionalidades do Azure Active Directory Premium, como o Azure multi-factor Authentication e a segurança do machine learning com cópia de segurança dos [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este passo a passo, terá de:

- Acesso de administrador para um diretório do Azure, com uma conta que pode criar e registar aplicações
- O exemplo de API web e aplicações de cliente nativo do [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publicar a API através do Proxy de aplicações

Para publicar uma API fora da sua intranet através do Proxy de aplicações, siga o mesmo padrão e para a publicação de web de aplicações. Para obter mais informações, consulte [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory](application-proxy-add-on-premises-application.md).

Para publicar a API de web SecretAPI através do Proxy de aplicações:

1. Criar e publicar o projeto de SecretAPI de exemplo como uma aplicação web do ASP.NET no seu computador local ou da intranet. Certifique-se de que pode acessar a aplicação web localmente. 
   
1. Na [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** na navegação à esquerda. Em seguida, no **descrição geral** página, selecione **aplicações empresariais**.
   
1. Na parte superior a **aplicações empresariais - todas as aplicações** página, selecione **novo aplicativo**.
   
1. Sobre o **adicionar uma aplicação** página, em **adicionar sua própria aplicação**, selecione **aplicação no local**. 
   
1. Se não tiver um conector de Proxy de aplicações instaladas, será solicitado a instalá-lo. Selecione **transferir conector do Proxy de aplicações** para transferir e instalar o conector. 
   
1. Assim que instalou o conector do Proxy de aplicações, o **adicionar sua própria aplicação no local** página:
   
   1. Introduza *SecretAPI* junto a **nome**.
      
   1. Introduza o URL que utilizar para aceder a API a partir de sua intranet junto a **Url interno**. 
      
   1. Certifique-se **pré-autenticação** está definida como **Azure Active Directory**. 
      
   1. Selecione **adicionar** na parte superior da página e aguarde que a aplicação a ser criada.
   
   ![Adicionar a aplicação API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Sobre o **aplicações empresariais - todas as aplicações** página, selecione a **SecretAPI** aplicação. 
   
1. Sobre o **SecretAPI - descrição geral** página, selecione **propriedades** na navegação à esquerda.
   
1. Não quer que as APIs para estar disponível para utilizadores finais no **MyApps** painel, por isso, defina **visível aos utilizadores** para **não** na parte inferior a **propriedades**página e, em seguida, selecione **guardar**.
   
   ![Não visível aos utilizadores](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Publicar a API web através do Proxy de aplicações do Azure AD. Agora, adicione os utilizadores que podem aceder à aplicação. 

1. Sobre o **SecretAPI - descrição geral** página, selecione **utilizadores e grupos** na navegação à esquerda.
   
1. Sobre o **utilizadores e grupos** página, selecione **adicionar utilizador**.  
   
1. Sobre o **adicionar atribuição** página, selecione **utilizadores e grupos**. 
   
1. Sobre o **utilizadores e grupos** página, procure e selecione os utilizadores que podem aceder à aplicação, incluindo, pelo menos, a próprio. Depois de selecionar todos os utilizadores, selecione **selecione**. 
   
   ![Selecionar e atribuir utilizador](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Volta a **adicionar atribuição** página, selecione **atribuir**. 

> [!NOTE]
> APIs que usam a autenticação integrada do Windows pode requerer [passos adicionais](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registre-se a aplicação nativa e conceder acesso à API

Aplicações nativas são programas desenvolvidos para utilizar num dispositivo ou plataforma específica. Antes de seu aplicativo nativo pode ligar e aceder a uma API, tem de o registar no Azure AD. Os passos seguintes mostram como registar uma aplicação nativa e fornecer acesso para a API publicadas através do Proxy de aplicações web.

Para registar a aplicação nativa AppProxyNativeAppSample:

1. No Azure Active Directory **descrição geral** página, selecione **registos das aplicações**e na parte superior a **registos das aplicações** painel, selecione **novo registo** .
   
1. Sobre o **registar uma aplicação** página:
   
   1. Sob **Name**, introduza *AppProxyNativeAppSample*. 
      
   1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**. 
      
   1. Sob **URL de redirecionamento**, menu pendente e selecione **cliente público (móveis e de ambiente de trabalho)** e, em seguida, introduza *https:\//appproxynativeapp*. 
      
   1. Selecione **registar**e aguarde que a aplicação para ser registado com êxito. 
      
      ![Novo registo de aplicação](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Agora que registou a aplicação de AppProxyNativeAppSample no Azure Active Directory. Para conceder o acesso de aplicação nativa para a API da web de SecretAPI:

1. No Azure Active Directory **descrição geral** > **registos das aplicações** página, selecione o **AppProxyNativeAppSample** aplicação. 
   
1. Sobre o **AppProxyNativeAppSample** página, selecione **permissões API** na navegação à esquerda. 
   
1. Sobre o **permissões de API** página, selecione **adicionar uma permissão**.
   
1. Na primeira **permissões de pedido de API** página, selecione a **APIs utiliza a minha organização** separador e, em seguida, procure e selecione **SecretAPI**. 
   
1. No próximo **permissões de pedido de API** página, selecione a caixa de verificação junto a **user_impersonation**e, em seguida, selecione **adicionar permissões**. 
   
    ![Selecionar uma API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Volta a **permissões de API** página, pode selecionar **conceder autorização de administrador da Contoso** para impedir que outros utilizadores tenham consentimento individualmente para a aplicação. 

## <a name="configure-the-native-app-code"></a>Configurar o código de aplicação nativa

A última etapa é configurar a aplicação nativa. O seguinte fragmento do *Form1.cs* arquivo no aplicativo de exemplo NativeClient faz com que a biblioteca ADAL adquirir o token para pedir a chamada de API e anexá-lo como portador ao cabeçalho da aplicação. 
   
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
   
Para configurar a aplicação nativa para se ligar ao Azure Active Directory e chamar o Proxy de aplicação de API, atualize os valores de marcador de posição na *App. config* ficheiro da aplicação de exemplo NativeClient com valores do Azure AD: 

- Colar o **ID de diretório (inquilino)** no `<add key="ida:Tenant" value="" />` campo. Pode encontrar e copie este valor (uma GUID) a partir da **descrição geral** página de qualquer uma das suas aplicações. 
  
- Cole o AppProxyNativeAppSample **ID da aplicação (cliente)** no `<add key="ida:ClientId" value="" />` campo. Pode encontrar e copie este valor (uma GUID) da AppProxyNativeAppSample **descrição geral** página.
  
- Cole o AppProxyNativeAppSample **URI de redirecionamento** no `<add key="ida:RedirectUri" value="" />` campo. Pode encontrar e copie este valor (um URI) a partir do AppProxyNativeAppSample **autenticação** página. 
  
- Cole o SecretAPI **URI de ID de aplicação** no `<add key="todo:TodoListResourceId" value="" />` campo. Pode encontrar e copie este valor (um URI) a partir do SecretAPI **expor uma API** página.
  
- Cole o SecretAPI **URL da Home Page** no `<add key="todo:TodoListBaseAddress" value="" />` campo. Pode encontrar e copie este valor (uma URL) da SecretAPI **marca** página.

Depois de configurar os parâmetros, crie e execute a aplicação nativa. Quando seleciona a **sessão** botão, o aplicativo permite iniciar sessão e, em seguida, apresenta um ecrã de êxito para confirmar que a TI com êxito ao SecretAPI.

![Êxito](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Quickstart: Configurar uma aplicação de cliente para aceder a APIs web](../develop/quickstart-configure-app-access-web-apis.md)
- [Como ativar aplicações de cliente nativo interagir com aplicativos de proxy](application-proxy-configure-native-client-application.md)
