---
title: Adicione o Google como fornecedor de identidade para B2B - Azure AD
description: Federate com o Google para permitir que os utilizadores convidados inscrevam-se nas suas aplicações AD Azure com as suas próprias contas Gmail.
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
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926240"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicione o Google como um fornecedor de identidade para utilizadores convidados B2B

Ao criar uma federação com o Google, pode permitir que os utilizadores convidados inscrevam-se nas suas apps e recursos partilhados com as suas próprias contas Gmail, sem ter de criar contas da Microsoft. 

> [!NOTE]
> A federação da Google foi concebida especificamente para utilizadores do Gmail. Para federar com domínios G Suite, utilize [a federação direta](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência para o utilizador do Google?
Quando envia um convite aos utilizadores do Google Gmail, os utilizadores convidados devem aceder às suas aplicações ou recursos partilhados utilizando um link que inclua o contexto do inquilino. A sua experiência varia consoante já estejam inscritos no Google:
  - Os utilizadores convidados que não se inscreveram no Google serão solicitados a fazê-lo.
  - Os utilizadores convidados que já se tenham inscrito no Google serão solicitados a escolher a conta que pretendem utilizar. Devem escolher a conta que usou para convidá-los.

Os utilizadores convidados que vejam um erro de "cabeçalho demasiado longo" podem limpar os seus cookies ou abrir uma janela privada ou incógnita e tentar entrar novamente.

![Screenshot que mostra a página de sindes de sins de sção do Google.](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limitações

As equipas suportam totalmente os utilizadores convidados do Google em todos os dispositivos. Os utilizadores do Google podem iniciar sação em equipas a partir de um ponto final comum como `https://teams.microsoft.com` .

Os pontos finais comuns de outras aplicações podem não suportar os utilizadores do Google. Os utilizadores do Google guest devem iniciar sação usando um link que inclua as informações do seu inquilino. Seguem-se exemplos:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se os utilizadores de hóspedes do Google tentarem usar um link como `https://myapps.microsoft.com` `https://portal.azure.com` ou, eles vão ter um erro.

Também pode dar aos utilizadores do Google um link direto para uma aplicação ou recurso, desde que o link inclua as informações do seu inquilino. Por exemplo, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Passo 1: Configurar um projeto de desenvolvimento da Google
Em primeiro lugar, crie um novo projeto na Consola Google Developers para obter um ID de cliente e um segredo de cliente que pode adicionar mais tarde ao Azure Ative Directory (Azure AD). 
1. Vá às APIs do Google https://console.developers.google.com em , e inscreva-se na sua conta google. Recomendamos que utilize uma conta partilhada do Google.
2. Aceite os termos de serviço se for solicitado a fazê-lo.
3. Criar um novo projeto: No painel de instrumentos, selecione **Create Project,** dê ao projeto um nome (por exemplo, **Azure AD B2B),** e, em seguida, selecione **Criar** : 
   
   ![Screenshot que mostra uma página de Novo Projeto.](media/google-federation/google-new-project.png)

4. Na página **APIs & Services,** selecione **Ver** no âmbito do seu novo projeto.

5. Selecione **Ir para a visão geral** das APIs no cartão APIs. Selecione **o ecrã de consentimento OAuth** .

6. Selecione **Externo** e, em seguida, selecione **Criar** . 

7. No **ecrã de consentimento OAuth,** insira um **nome de aplicação:**

   ![Screenshot que mostra o ecrã de consentimento do Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Percorra a secção **de domínios autorizados** e introduza **microsoftonline.com:**

   ![Screenshot que mostra a secção de domínios autorizados.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selecione **Guardar** .

10. Selecione **Credenciais** . No menu **Criar credenciais,** selecione **OAuth client ID** :

    ![Screenshot que mostra o menu de credenciais do Google APIs Create.](media/google-federation/google-api-credentials.png)

11. Sob **o tipo de aplicação** , selecione **aplicação Web** . Dê à aplicação um nome adequado, como **Azure AD B2B** . Em **URIs de redirecionamento autorizado,** insira os seguintes URIs:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(onde `<tenant ID>` está a sua iD do seu inquilino)
   
    > [!NOTE]
    > Para encontrar a identificação do seu inquilino, vá ao [portal Azure.](https://portal.azure.com) Sob **diretório ativo Azure,** selecione **Propriedades** e copie o ID do **Inquilino.**

    ![Screenshot que mostra a secção de URIs de redirecionamento autorizado.](media/google-federation/google-create-oauth-client-id.png)

12. Selecione **Criar** . Copie a identificação do cliente e o segredo do cliente. Irá usá-los quando adicionar o fornecedor de identidade no portal Azure.

    ![Screenshot que mostra a identificação do cliente OAuth e o segredo do cliente.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passo 2: Configurar a Federação google em Azure AD 
Agora vai definir o ID do cliente do Google e o segredo do cliente. Pode utilizar o portal Azure ou o PowerShell para o fazer. Não se esqueça de testar a configuração da federação do Google convidando-se a si próprio. Use um endereço Gmail e tente resgatar o convite com a sua conta google convidada. 

**Para configurar a federação do Google no portal Azure** 
1. Aceda ao [portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Ative Directory** . 
2. Selecione **identidades externas** .
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
   > Utilize o ID do cliente e o segredo do cliente a partir da app que criou em "Passo 1: Configurar um projeto de desenvolvimento do Google". Para mais informações, consulte [o New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Como retiro a federação do Google?
Pode eliminar a configuração da federação do Google. Se o fizer, os utilizadores convidados do Google que já tenham resgatado o seu convite não poderão iniciar saúde. Mas pode dar-lhes acesso aos seus recursos novamente, eliminando-os do diretório e convidando-os novamente. 
 
**Para eliminar a federação do Google no portal AD Azure**
1. Aceda ao [portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Ative Directory** . 
2. Selecione **identidades externas** .
3. Selecione **Todos os fornecedores de identidade** .
4. Na linha **google,** selecione o botão elipse **(...** ) e, em seguida, selecione **Delete** . 
   
   ![Screenshot que mostra o botão Eliminar para o fornecedor de identidade social.](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a eliminação. 

**Para eliminar a federação do Google utilizando o PowerShell** 
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Execute `Connect-AzureAD`.  
4. No pedido de inscrição, inscreva-se na conta gerida do Administrador Global.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
