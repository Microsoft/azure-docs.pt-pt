---
title: Adicionar o Google como um fornecedor de identidade para B2B - Azure Active Directory | Documentos da Microsoft
description: Federar com o Google para permitir que os utilizadores convidados entrar para as suas aplicações do Azure AD com a sua própria conta do Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe259b6a0f45828e100de33f533e370323128eef
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821701"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicionar o Google como um fornecedor de identidade para utilizadores convidados B2B

Ao configurar a Federação com o Google, pode permitir que os utilizadores convidados iniciar sessão sua aplicações partilhadas e recursos com as suas contas do Google, sem precisar criar Accounts da Microsoft (Msa) ou contas do Azure AD.  
> [!NOTE]
> Os utilizadores de convidado do Google tem de iniciar sessão através de uma ligação que inclui o contexto do inquilino (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` ou `https://portal.azure.com/<tenant id>`, ou no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Ligações diretas para aplicativos e recursos também funcionam, desde que eles incluem o contexto do inquilino. Os utilizadores convidados são não foi possível iniciar sessão com pontos finais que não têm nenhum contexto de inquilino. Por exemplo, utilizando `https://myapps.microsoft.com`, `https://portal.azure.com`, ou o ponto de extremidade comum as Equipes resultará num erro.
 
## <a name="what-is-the-experience-for-the-google-user"></a>O que é a experiência do utilizador de Google?
Quando envia um convite para um utilizador de Google Gmail, o utilizador convidado deve acessar seus recursos através de uma ligação que inclui o contexto do inquilino ou aplicações partilhadas. Sua experiência varia consoante se eles já sessão iniciados para a Google:
  - Se o utilizador convidado não tiver sessão iniciado Google, é-lhe pedidos para iniciar sessão no Google.
  - Se o utilizador convidado já tem sessão iniciado no Google, serão solicitados para escolher a conta que deseja usar. Têm de escolher a conta que utilizou para convidá-los.

Se o utilizador convidado vê um erro de "cabeçalho demasiado longo", podem tentar limpar os cookies ou podem abrir uma janela incógnita ou privada e tente iniciar sessão novamente.

![Iniciar sessão com o Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Passo 1: Configurar um projeto de programador do Google
Primeiro, crie um novo projeto na consola de programadores do Google para obter uma client ID e um segredo do cliente que pode adicionar mais tarde para o Azure AD. 
1. Aceda às APIs do Google em https://console.developers.google.come inicie sessão com a sua conta do Google. Recomendamos que utilize uma equipe partilhada conta Google.
2. Crie um novo projeto: No Dashboard, selecione **criar projeto**e, em seguida, selecione **criar**. Na página de novo projeto, introduza um **nome do projeto**e, em seguida, selecione **criar**.
   
   ![Novo projeto do Google](media/google-federation/google-new-project.png)

3. Certifique-se de que o novo projeto está selecionado no menu do projeto. Em seguida, abra o menu no canto superior esquerdo e selecione **APIs e serviços** > **credenciais**.

   ![Credenciais de API do Google](media/google-federation/google-api.png)
 
4. Escolha o **ecrã de consentimento do OAuth** separador e introduza uma **nome da aplicação**. (Deixe as outras definições).

   ![Ecrã de consentimento do OAuth do Google](media/google-federation/google-oauth-consent-screen.png)

5. Desloque-se para o **autorizado domínios** secção e introduza microsoftonline.com.

   ![Secção de domínios autorizados](media/google-federation/google-oauth-authorized-domains.png)

6. Selecione **Guardar**.

7. Escolha o **credenciais** separador. Na **criar credenciais** menu, escolha **ID de cliente OAuth**.

   ![Credenciais de API do Google](media/google-federation/google-api-credentials.png)

8. Sob **tipo de aplicação**, escolha **aplicação Web**e, em **URIs de redirecionamento de autorizado**, introduza os seguintes URIs:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(onde `<directory id>` é o seu ID de diretório)
   
    > [!NOTE]
    > Para encontrar o seu ID de diretório, aceda a https://portal.azure.come, em **do Azure Active Directory**, escolha **propriedades** e copie o **ID do diretório**.

   ![Criar ID de cliente OAuth](media/google-federation/google-create-oauth-client-id.png)

9. Selecione **Criar**. Copie o ID de cliente e segredo do cliente, que irá utilizar ao adicionar o fornecedor de identidade no portal do Azure AD.

   ![Segredo de cliente e o ID do cliente de OAuth](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passo 2: Configurar a Federação de Google no Azure AD 
Agora definirá o cliente de Google ID e segredo do cliente, tanto pelo introduzi-la no portal do Azure AD com o PowerShell. Certifique-se de testar a configuração de Federação do Google ao convidá-se através de um endereço de Gmail e tentar para resgatar o convite com sua conta Google convidada. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Para configurar a Federação de Google no portal do Azure AD 
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **fornecedores de identidade**e, em seguida, clique nas **Google** botão.
4. Introduza um nome. Em seguida, introduza o ID de cliente e o segredo de cliente que obteve anteriormente. Selecione **Guardar**. 

   ![Adicionar o fornecedor de identidade do Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Para configurar a Federação de Google com o PowerShell
1. Instalar a versão mais recente do Azure AD PowerShell para o módulo de gráfico ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute o seguinte comando: `Connect-AzureAD`.
3. No prompt de início de sessão, inicie sessão com a conta de Administrador Global gerenciada.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Utilize o id de cliente e o cliente secreta da aplicação que criou no "passo 1: Configurar um projeto do Google developer." Para obter mais informações, consulte a [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) artigo. 
 
## <a name="how-do-i-remove-google-federation"></a>Como posso remover o Federação Google?
É possível eliminar a configuração de Federação do Google. Se fizer isso, os utilizadores de convidado do Google que já tem resgatado o seu convite não será possível iniciar sessão, mas pode lhes dar acesso aos seus recursos novamente, excluí-los a partir do diretório e voltar a convidá-los. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Para eliminar o Federação Google no portal do Azure AD: 
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **fornecedores de identidade**.
4. Sobre o **Google** , selecione o menu de contexto (**...** ) e, em seguida, selecione **eliminar**. 
   
   ![Eliminar o fornecedor de identidade social](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a eliminação. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Para eliminar o Federação Google com o PowerShell: 
1. Instalar a versão mais recente do Azure AD PowerShell para o módulo de gráfico ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute `Connect-AzureAD`.  
4. No início de sessão da linha de comandos, inicie sessão com a conta de Administrador Global gerenciada.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
