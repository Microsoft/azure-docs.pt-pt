---
title: Adicione o Google como fornecedor de identidade para B2B - Azure AD
description: Federate com o Google para permitir que os utilizadores convidados inscrevam-se nas suas aplicações AD Azure com a sua própria conta Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dba592417ca07d1858a7942b162b4dfa6a85680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273998"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicione o Google como um fornecedor de identidade para utilizadores convidados B2B

Ao criar uma federação com o Google, pode permitir que os utilizadores convidados inscrevam-se nas suas apps e recursos partilhados com as suas próprias contas Gmail, sem ter de criar contas da Microsoft (MSAs). 

> [!NOTE]
> A federação da Google foi concebida especificamente para utilizadores do Gmail. Para federar com domínios G Suite, utilize a [função de federação direta](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência para o utilizador do Google?
Quando envia um convite para um utilizador do Google Gmail, o utilizador convidado deve aceder às suas aplicações ou recursos partilhados utilizando um link que inclua o contexto do inquilino. A sua experiência varia consoante já estejam inscritos no Google:
  - Se o utilizador convidado não estiver inscrito no Google, é solicitado que entre no Google.
  - Se o utilizador convidado já estiver inscrito no Google, será solicitado que escolham a conta que pretendem utilizar. Devem escolher a conta que usou para convidá-los.

Se o utilizador convidado vir um erro de "cabeçalho demasiado longo", pode tentar limpar os seus cookies, ou pode abrir uma janela privada ou incógnita e tentar iniciar novamente a sessão.

![Screenshot mostrando o sinal do Google na página](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limitações

As equipas suportam totalmente os utilizadores convidados do Google em todos os dispositivos. Os utilizadores do Google podem iniciar sação em equipas a partir de um ponto final comum como `https://teams.microsoft.com` .

Os pontos finais comuns de outras aplicações podem não suportar os utilizadores do Google. Os utilizadores do Google guest devem iniciar sação usando um link que inclua as informações do seu inquilino. Seguem-se alguns exemplos:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se os utilizadores de hóspedes do Google tentarem utilizar um link como `https://myapps.microsoft.com` `https://portal.azure.com` ou, eles vão ter um erro.

Também pode dar aos utilizadores do Google um link direto para uma aplicação ou recurso, desde que este link inclua as informações do seu inquilino, por `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` exemplo. 

## <a name="step-1-configure-a-google-developer-project"></a>Passo 1: Configurar um projeto de desenvolvimento da Google
Em primeiro lugar, crie um novo projeto na Consola Google Developers para obter um ID de cliente e um segredo de cliente que pode adicionar mais tarde ao Azure AD. 
1. Vá às APIs do Google https://console.developers.google.com em , e inscreva-se na sua conta google. Recomendamos que utilize uma conta partilhada do Google.
2. Aceitar os Termos de Serviço se solicitado
3. Criar um novo projeto: No Painel de Instrumentos, selecione **Create Project,** dê ao projeto um nome (por exemplo , "Azure AD B2B") e, em seguida, selecione **Criar**. 
   
   ![Screenshot mostrando uma nova página de projeto para o Google](media/google-federation/google-new-project.png)

4. Na página **apis & Services** que agora lhe é apresentada, clique em **Ver** no âmbito do seu novo projeto.

5. Clique **em Ir à visão geral** das APIs no cartão APIs. Selecione **o ecrã de consentimento OAuth**.

6. Selecione **Externo**e, em seguida, selecione **Criar**. 

7. No **ecrã de consentimento OAuth,** insira um **nome de aplicação**. 

   ![Screenshot mostrando a opção do ecrã de consentimento do Google OAuth](media/google-federation/google-oauth-consent-screen.png)

8. Percorra a secção **de domínios autorizados** e introduza microsoftonline.com.

   ![Screenshot mostrando a secção de domínios autorizados](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selecione **Guardar**.

10. Escolha **credenciais.** No menu **Criar credenciais,** escolha **ID do cliente OAuth.**

    ![Screenshot mostrando as APIs do Google criam opção de credenciais](media/google-federation/google-api-credentials.png)

11. Sob **o tipo de aplicação**, escolha a **aplicação Web** e dê à aplicação um nome adequado, por exemplo "Azure AD B2B", e, em seguida, em **URIs de redirecionamento autorizado,** insira os seguintes URIs:
    - `https://login.microsoftonline.com` 
    - `https://login.microsoftonline.com/te/<tenant id>/oauth2/authresp` <br>(onde `<tenant id>` está a sua iD do seu inquilino)
   
    > [!NOTE]
    > Para encontrar o seu ID do inquilino, vá https://portal.azure.com a, e sob **o Diretório Ativo Azure,** escolha **Propriedades** e copie o ID do **Inquilino.**

    ![Screenshot mostrando a secção uris de redirecionamento autorizado](media/google-federation/google-create-oauth-client-id.png)

12. Selecione **Criar**. Copie o ID do cliente e o segredo do cliente, que utilizará quando adicionar o fornecedor de identidade no portal AD AZure.

    ![Screenshot mostrando o ID do cliente OAuth e o segredo do cliente](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passo 2: Configurar a Federação google em Azure AD 
Agora você vai definir o ID do cliente do Google e o segredo do cliente, quer inserindo-o no portal AD Azure ou usando o PowerShell. Não se esqueça de testar a configuração da sua federação google convidando-se a usar um endereço Gmail e tentando resgatar o convite com a sua conta google convidada. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Para configurar a federação do Google no portal AD AZure 
1. Aceda ao [portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **identidades externas**.
3. Selecione **Todos os fornecedores de identidade**e, em seguida, clique no botão **Google.**
4. Em seguida, insira a identificação do cliente e o segredo do cliente que obteve anteriormente. Selecione **Guardar**. 

   ![Screenshot mostrando a página do fornecedor de identidade do Google Adicionar](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Para configurar a federação do Google usando o PowerShell
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Executar o seguinte comando: `Connect-AzureAD` .
3. Na 00da vez, inscreva-se na conta gerida do Administrador Global.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Use o id do cliente e o segredo do cliente a partir da app que criou em "Passo 1: Configurar um projeto de desenvolvimento do Google". Para mais informações, consulte o artigo [New-AzureADMSIdentityProvider.](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 
 
## <a name="how-do-i-remove-google-federation"></a>Como retiro a federação do Google?
Pode eliminar a configuração da federação do Google. Se o fizer, os utilizadores convidados do Google que já tenham resgatado o seu convite não poderão iniciar sedutar, mas pode dar-lhes acesso aos seus recursos novamente, eliminando-os do diretório e reenvidá-los. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Para eliminar a federação do Google no portal AD Azure: 
1. Aceda ao [portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **identidades externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Na linha **google,** selecione o menu de contexto **(...**) e, em seguida, selecione **Delete**. 
   
   ![Screenshot mostrando a opção Eliminar para o fornecedor de identidade social](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a eliminação. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Para eliminar a federação do Google utilizando o PowerShell: 
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Execute `Connect-AzureAD`.  
4. No login em prompt, inicie sessão com a conta gerida do Administrador Global.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
