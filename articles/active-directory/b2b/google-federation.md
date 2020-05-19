---
title: Adicione o Google como fornecedor de identidade para B2B - Azure AD
description: Federate com o Google para permitir que os utilizadores convidados acedam às suas aplicações Azure AD com a sua própria conta Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1a743f313ca4159ce4ef03413a94ec25bb8fae
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591238"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicione o Google como fornecedor de identidade para utilizadores convidados B2B

Ao configurar a federação com o Google, pode permitir que os utilizadores convidados inscrevam as suas aplicações e recursos partilhados com as suas próprias contas gmail, sem ter de criar contas da Microsoft (MSAs). 

> [!NOTE]
> A federação da Google foi concebida especificamente para utilizadores do Gmail. Para federar com os domínios G Suite, use a [função de federação direta](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência para o utilizador do Google?
Ao enviar um convite a um utilizador do Google Gmail, o utilizador convidado deve aceder às suas aplicações ou recursos partilhados utilizando um link que inclua o contexto do inquilino. A sua experiência varia consoante já estejam inscritas no Google:
  - Se o utilizador convidado não estiver inscrito no Google, é solicitado que entre no Google.
  - Se o utilizador convidado já estiver inscrito no Google, serão solicitados a escolher a conta que pretendem utilizar. Devem escolher a conta que usou para convidá-los.

Se o utilizador hóspede vir um erro de "cabeçalho demasiado longo", pode tentar limpar os seus cookies, ou pode abrir uma janela privada ou incógnita e tentar iniciar sessão novamente.

![Screenshot mostrando o sinal do Google na página](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limitações

As equipas suportam totalmente os utilizadores convidados do Google em todos os dispositivos. Os utilizadores do Google podem iniciar sessão em Equipas a partir de um ponto final comum como `https://teams.microsoft.com` .

Os pontos finais comuns de outras aplicações podem não suportar os utilizadores do Google. Os utilizadores convidados do Google devem iniciar sessão utilizando um link que inclua informações do seu inquilino. Seguem-se alguns exemplos:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se os utilizadores do Google tentarem utilizar um link como `https://myapps.microsoft.com` ou `https://portal.azure.com` , terão um erro.

Também pode dar aos utilizadores do Google um link direto para uma aplicação ou recurso, desde que este link inclua informações do seu inquilino, por `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` exemplo. 

## <a name="step-1-configure-a-google-developer-project"></a>Passo 1: Configure um projeto de desenvolvimento da Google
Em primeiro lugar, crie um novo projeto na Consola do Google Developers para obter um ID de cliente e um segredo de cliente que poderá adicionar mais tarde ao Azure AD. 
1. Vá às APIs do Google https://console.developers.google.com e inscreva-se na sua conta do Google. Recomendamos que utilize uma conta partilhada do Google.
2. Criar um novo projeto: No Dashboard, selecione **Criar Projeto,** e depois selecione **Criar**. Na página New Project, introduza um Nome de **Projeto**e, em seguida, selecione **Criar**.
   
   ![Screenshot mostrando uma nova página de projeto para o Google](media/google-federation/google-new-project.png)

3. Certifique-se de que o seu novo projeto está selecionado no menu do projeto. Em seguida, sob **os Serviços & APIs,** selecione o ecrã de **consentimento oAuth**.

4. Selecione **External**, e, em seguida, selecione **Criar**. 
5. No ecrã de **consentimento da OAuth,** introduza um **nome de aplicação**. (Deixe as outras definições.)

   ![Screenshot mostrando a opção de ecrã de consentimento do Google OAuth](media/google-federation/google-oauth-consent-screen.png)

6. Percorra a secção de **domínios autorizados** e introduza microsoftonline.com.

   ![Screenshot mostrando a secção de domínios autorizados](media/google-federation/google-oauth-authorized-domains.png)

7. Selecione **Guardar**.

8. Escolha **credenciais**. No menu **de credenciais Criar,** escolha **ID do cliente OAuth**.

   ![Screenshot mostrando as APIs do Google criar a opção de credenciais](media/google-federation/google-api-credentials.png)

9. No **tipo de aplicação,** escolha **a aplicação Web,** e depois em **URIs de redirecionamento autorizado,** introduza as seguintes URIs:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(onde `<directory id>` está o seu ID de diretório)
   
     > [!NOTE]
     > Para encontrar o seu ID de diretório, vá a https://portal.azure.com , e sob o **Diretório Ativo Azure,** escolha **Propriedades** e copie o ID do **Diretório**.

   ![Screenshot mostrando a secção de REdirecionamento autorizado de URIs](media/google-federation/google-create-oauth-client-id.png)

10. Selecione **Criar**. Copie o ID do cliente e o segredo do cliente, que utilizará quando adicionar o fornecedor de identidade no portal Azure AD.

   ![Screenshot mostrando o ID do cliente OAuth e segredo do cliente](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passo 2: Configure a federação do Google em Azure AD 
Agora irá definir o id do cliente da Google e o segredo do cliente, quer introduzindo-o no portal Azure AD ou utilizando o PowerShell. Certifique-se de testar a sua configuração da federação do Google convidando-se a usar um endereço Gmail e tentando resgatar o convite com a sua conta convidada do Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Para configurar a federação do Google no portal Azure AD 
1. Vá ao [portal Azure.](https://portal.azure.com) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os fornecedores de identidade**e, em seguida, clique no botão **Google.**
4. Insira um nome. Em seguida, insira a identificação do cliente e o segredo do cliente que obteve anteriormente. Selecione **Guardar**. 

   ![Screenshot mostrando a página do fornecedor de identidade do Google Adicionar](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Para configurar a federação da Google usando o PowerShell
1. Instale a versão mais recente do Azure AD PowerShell para módulo gráfico[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Executar o seguinte comando: `Connect-AzureAD` .
3. No momento de início de sessão, inscreva-se na conta gerida do Administrador Global.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Utilize o id do cliente e o segredo do cliente a partir da app que criou em "Step 1: Configure um projeto de desenvolvimento da Google". Para mais informações, consulte o artigo [New-AzureADMSIdentityProvider.](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 
 
## <a name="how-do-i-remove-google-federation"></a>Como posso remover a federação do Google?
Pode eliminar a configuração da federação do Google. Se o fizer, os utilizadores convidados do Google que já resgataram o seu convite não poderão inscrever-se, mas poderá dar-lhes acesso aos seus recursos novamente, apagando-os do diretório e reconvidando-os. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Para eliminar a federação do Google no portal Azure AD: 
1. Vá ao [portal Azure.](https://portal.azure.com) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Na linha **Google,** selecione o menu de contexto **(...**) e, em seguida, selecione **Delete**. 
   
   ![Screenshot mostrando a opção Delete para o fornecedor de identidade social](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a eliminação. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Para eliminar a federação da Google utilizando o PowerShell: 
1. Instale a versão mais recente do Azure AD PowerShell para módulo gráfico[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute `Connect-AzureAD`.  
4. No início de sessão em pronta, inicie sessão com a conta de Administrador Global gerida.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para mais informações, consulte [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
