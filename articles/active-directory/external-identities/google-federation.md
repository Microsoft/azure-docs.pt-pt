---
title: Adicione o Google como fornecedor de identidade para B2B - Azure AD
description: Federate com o Google para permitir que os utilizadores convidados inscrevam-se nas suas aplicações AD Azure com as suas próprias contas Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2e9acda136654a0dd654eded7b4c17e560fc0aa
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709706"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicione o Google como um fornecedor de identidade para utilizadores convidados B2B

Ao criar uma federação com o Google, pode permitir que os utilizadores convidados inscrevam-se nas suas apps e recursos partilhados com as suas próprias contas Gmail, sem ter de criar contas da Microsoft.

Depois de ter adicionado o Google como uma das opções de entrada da sua aplicação, no **Sinal na** página, um utilizador pode simplesmente introduzir o e-mail que usa para iniciar sins no Google, ou pode selecionar **opções de Iniciar sintrodução** e escolher **iniciar sôm com o Google.** Em qualquer dos casos, serão redirecionados para a página de inscrição do Google para autenticação.

![Iniciar sinsções para utilizadores do Google](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> A federação da Google foi concebida especificamente para utilizadores do Gmail. Para federar com domínios G Suite, utilize [a federação direta](direct-federation.md).

> [!IMPORTANT]
> **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência para o utilizador do Google?

Quando um utilizador do Google resgata o seu convite, a sua experiência varia consoante já estejam inscritos no Google:

- Os utilizadores convidados que não se inscreveram no Google serão solicitados a fazê-lo.
- Os utilizadores convidados que já se tenham inscrito no Google serão solicitados a escolher a conta que pretendem utilizar. Devem escolher a conta que usou para convidá-los.

Os utilizadores convidados que vejam um erro de "cabeçalho demasiado longo" podem limpar os seus cookies ou abrir uma janela privada ou incógnita e tentar entrar novamente.

![Screenshot que mostra a página de sindes de sins de sção do Google.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Pontos finais de inscrição

Os utilizadores de hóspedes do Google podem agora iniciar sômposição nas suas aplicações multi-inquilinas ou na Microsoft, utilizando um [ponto final comum](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (por outras palavras, um URL de aplicações gerais que não inclui o contexto do seu inquilino). Durante o processo de entrada, o utilizador convidado escolhe **as opções de Inscrição** e, em seguida, seleciona **Iniciar sação para uma organização**. Em seguida, o utilizador escreve o nome da sua organização e continua a iniciar sessão com as suas credenciais google.

Os utilizadores de hóspedes do Google também podem usar pontos finais de aplicações que incluem informações do seu inquilino, por exemplo:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Também pode dar aos utilizadores do Google um link direto para uma aplicação ou recurso, incluindo as informações do seu inquilino, por `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` exemplo.

## <a name="deprecation-of-webview-sign-in-support"></a>Depreciação do suporte de s-in webView

A partir de 4 de janeiro de 2021, o Google está [a depreciar o suporte de sing-in do WebView incorporado.](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) Se estiver a utilizar a Federação do Google ou [a inscrição de self-service com](identity-providers.md)o Gmail, deverá testar as suas aplicações nativas de linha de negócios para compatibilidade. Se as suas aplicações incluir conteúdo WebView que requer autenticação, os utilizadores do Google Gmail não poderão autenticar. Seguem-se cenários conhecidos que terão impacto nos utilizadores do Gmail:

- Aplicativos Windows que usam WebView incorporado ou o WebAccountManager (WAM) em versões mais antigas do Windows.
- Outras aplicações nativas que desenvolveu que usam uma estrutura de navegador incorporada para autenticação.

Esta alteração não afeta:

- Aplicativos Windows que usam WebView incorporado ou o WebAccountManager (WAM) nas versões mais recentes do Windows
- Aplicativos microsoft iOS
- Identidades G Suite, por exemplo, quando você está usando [federação direta](direct-federation.md) baseada em SAML com Suíte G

Continuamos a testar várias plataformas e cenários, e atualizaremos este artigo em conformidade.
### <a name="to-test-your-apps-for-compatibility"></a>Para testar os seus aplicativos para compatibilidade

1. Siga as [orientações da Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) para determinar se as suas aplicações estão afetadas.
2. Utilizando o Fiddler ou outra ferramenta de teste, injete um cabeçalho durante a entrada e utilize uma identidade externa da Google para testar o teste de entrada:

   1. Adicione o Google-Contas-Check-OAuth-Login:verdadeiro aos cabeçalhos de pedido HTTP quando os pedidos forem enviados para accounts.google.com.
   1. Tente iniciar sing-in na aplicação inserindo um endereço Gmail na página de accounts.google.com de entrada.
   1. Se o início de sessão falhar e vir um erro como "Este navegador ou aplicação pode não ser seguro", as suas identidades externas da Google serão bloqueadas ao iniciar sessão.

3. Resolver a questão fazendo uma das seguintes:

   - Se a sua aplicação Windows utilizar o WebView incorporado ou o WebAccountManager (WAM) numa versão mais antiga do Windows, atualize para a versão mais recente do Windows.
   - Modifique as suas aplicações para utilizar o navegador do sistema para iniciar sôm. Para mais detalhes, consulte [Embedded vs System Web UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) na documentação MSAL.NET.  


## <a name="step-1-configure-a-google-developer-project"></a>Passo 1: Configurar um projeto de desenvolvimento da Google
Em primeiro lugar, crie um novo projeto na Consola Google Developers para obter um ID de cliente e um segredo de cliente que pode adicionar mais tarde ao Azure Ative Directory (Azure AD). 
1. Vá às APIs do Google https://console.developers.google.com em , e inscreva-se na sua conta google. Recomendamos que utilize uma conta partilhada do Google.
2. Aceite os termos de serviço se for solicitado a fazê-lo.
3. Criar um novo projeto: No canto superior esquerdo da página, selecione a lista de projetos e, em seguida, na página **do projeto Select,** selecione **New Project**.
4. Na página do **Novo Projeto,** dê um nome ao projeto (por exemplo, **Azure AD B2B),** e, em seguida, selecione **Criar**: 
   
   ![Screenshot que mostra uma página de Novo Projeto.](media/google-federation/google-new-project.png)

4. Na página **APIs & Services,** selecione **Ver** no âmbito do seu novo projeto.

5. Selecione **Ir para a visão geral** das APIs no cartão APIs. Selecione **o ecrã de consentimento OAuth**.

6. Selecione **Externo** e, em seguida, selecione **Criar**. 

7. No **ecrã de consentimento OAuth,** insira um **nome de aplicação:**

   ![Screenshot que mostra o ecrã de consentimento do Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Percorra a secção **de domínios autorizados** e introduza **microsoftonline.com:**

   ![Screenshot que mostra a secção de domínios autorizados.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selecione **Guardar**.

10. Selecione **Credenciais**. No menu **Criar credenciais,** selecione **OAuth client ID**:

    ![Screenshot que mostra o menu de credenciais do Google APIs Create.](media/google-federation/google-api-credentials.png)

11. Sob **o tipo de aplicação**, selecione **aplicação Web**. Dê à aplicação um nome adequado, como **Azure AD B2B**. Em **URIs de redirecionamento autorizado,** insira os seguintes URIs:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(onde `<tenant ID>` está a sua iD do seu inquilino)
   
    > [!NOTE]
    > Para encontrar a identificação do seu inquilino, vá ao [portal Azure.](https://portal.azure.com) Sob **diretório ativo Azure,** selecione **Propriedades** e copie o ID do **Inquilino.**

    ![Screenshot que mostra a secção de URIs de redirecionamento autorizado.](media/google-federation/google-create-oauth-client-id.png)

12. Selecione **Criar**. Copie a identificação do cliente e o segredo do cliente. Irá usá-los quando adicionar o fornecedor de identidade no portal Azure.

    ![Screenshot que mostra a identificação do cliente OAuth e o segredo do cliente.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passo 2: Configurar a Federação google em Azure AD 
Agora vai definir o ID do cliente do Google e o segredo do cliente. Pode utilizar o portal Azure ou o PowerShell para o fazer. Não se esqueça de testar a configuração da federação do Google convidando-se a si próprio. Use um endereço Gmail e tente resgatar o convite com a sua conta google convidada. 

**Para configurar a federação do Google no portal Azure** 
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Ative Directory**. 
2. Selecione **identidades externas**.
3. Selecione **todos os fornecedores de identidade** e, em seguida, selecione o botão **Google.**
4. Insira a identificação do cliente e o segredo do cliente que obteve anteriormente. **Selecione Guardar:** 

   ![Screenshot que mostra a página de fornecedor de identidade do Google.](media/google-federation/google-identity-provider.png)

**Para configurar a federação do Google usando o PowerShell**
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Executar este comando: `Connect-AzureAD`
3. Na 00da vez, inscreva-se na conta gerida do Administrador Global.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Utilize o ID do cliente e o segredo do cliente a partir da app que criou em "Passo 1: Configurar um projeto de desenvolvimento do Google". Para mais informações, consulte [o New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 
 
## <a name="how-do-i-remove-google-federation"></a>Como retiro a federação do Google?
Pode eliminar a configuração da federação do Google. Se o fizer, os utilizadores convidados do Google que já tenham resgatado o seu convite não poderão iniciar saúde. Mas pode dar-lhes acesso aos seus recursos novamente, eliminando-os do diretório e os convidando novamente. 
 
**Para eliminar a federação do Google no portal AD Azure**
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Ative Directory**. 
2. Selecione **identidades externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Na linha **google,** selecione o botão elipse **(...**) e, em seguida, selecione **Delete**. 
   
   ![Screenshot que mostra o botão Eliminar para o fornecedor de identidade social.](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a eliminação. 

**Para eliminar a federação do Google utilizando o PowerShell** 
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Execute `Connect-AzureAD`.  
4. No pedido de inscrição, inscreva-se na conta gerida do Administrador Global.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true).
