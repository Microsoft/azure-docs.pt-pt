---
title: Adicionar o Google como um provedor de identidade para B2B-Azure Active Directory | Microsoft Docs
description: Federar com o Google para permitir que os usuários convidados entrem em seus aplicativos do Azure AD com sua própria conta do gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b26679542753d5fb429c33e4220c23a3937c5cb
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430437"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Adicionar o Google como um provedor de identidade para usuários convidados B2B (visualização)

|     |
| --- |
| O Google Federation é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ao configurar a Federação com o Google, você pode permitir que os usuários convidados entrem em seus aplicativos compartilhados e recursos com suas próprias contas do Gmail, sem precisar criar contas da Microsoft (MSAs) ou contas do Azure AD. O Google Federation foi projetado especificamente para usuários do gmail. Para federar com domínios do G Suite, use o [recurso de Federação direta](direct-federation.md) .
> [!NOTE]
> Seus usuários convidados do Google devem entrar usando um link que inclui o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` ou `https://portal.azure.com/<tenant id>`, ou no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam contanto que incluam o contexto do locatário. Atualmente, os usuários convidados não podem entrar usando pontos de extremidade que não têm nenhum contexto de locatário. Por exemplo, usando `https://myapps.microsoft.com`, `https://portal.azure.com` ou o ponto de extremidade comum das equipes resultará em um erro.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência para o usuário do Google?
Quando você envia um convite para um usuário do Google Gmail, o usuário convidado deve acessar seus aplicativos ou recursos compartilhados usando um link que inclui o contexto do locatário. Sua experiência varia dependendo se elas já estão conectadas ao Google:
  - Se o usuário convidado não estiver conectado ao Google, será solicitado que ele entre no Google.
  - Se o usuário convidado já estiver conectado ao Google, ele será solicitado a escolher a conta que deseja usar. Eles devem escolher a conta usada para convidá-los.

Se o usuário convidado vir um erro de "cabeçalho muito longo", ele poderá tentar limpar seus cookies ou abrir uma janela particular ou Incognito e tentar entrar novamente.

![Captura de tela mostrando a página de entrada do Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Etapa 1: configurar um projeto de desenvolvedor do Google
Primeiro, crie um novo projeto no console de desenvolvedores do Google para obter uma ID do cliente e um segredo do cliente que você pode adicionar posteriormente ao Azure AD. 
1. Vá para as APIs do Google em https://console.developers.google.com e entre com sua conta do Google. Recomendamos que você use uma conta compartilhada do Google da equipe.
2. Criar um novo projeto: no painel, selecione **criar projeto**e, em seguida, selecione **criar**. Na página novo projeto, insira um **nome de projeto**e, em seguida, selecione **criar**.
   
   ![Captura de tela mostrando uma nova página de projeto para o Google](media/google-federation/google-new-project.png)

3. Verifique se o novo projeto está selecionado no menu projeto. Em seguida, abra o menu no canto superior esquerdo e selecione **APIs & serviços** > **credenciais**.

   ![Captura de tela mostrando a opção de credenciais da API do Google](media/google-federation/google-api.png)
 
4. Escolha a guia de **tela de consentimento do OAuth** e insira um nome de **aplicativo**. (Deixe as outras configurações.)

   ![Captura de tela mostrando a opção de ecrã de consentimento do Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Role até a seção **domínios autorizados** e insira microsoftonline.com.

   ![Captura de tela mostrando a seção domínios autorizados](media/google-federation/google-oauth-authorized-domains.png)

6. Selecione **Guardar**.

7. Escolha a guia **credenciais** . No menu **criar credenciais** , escolha **ID do cliente OAuth**.

   ![Captura de tela mostrando a opção criar credenciais do Google APIs](media/google-federation/google-api-credentials.png)

8. Em **tipo de aplicativo**, escolha **aplicativo Web**e, em **URIs de redirecionamento autorizados**, insira os seguintes URIs:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(onde `<directory id>` é sua ID de diretório)
   
     > [!NOTE]
     > Para localizar a ID do diretório, vá para https://portal.azure.com e, em **Azure Active Directory**, escolha **Propriedades** e copie a **ID do diretório**.

   ![Captura de tela mostrando a seção de URIs de redirecionamento autorizados](media/google-federation/google-create-oauth-client-id.png)

9. Selecione **Criar**. Copie a ID do cliente e o segredo do cliente, que você usará ao adicionar o provedor de identidade no portal do AD do Azure.

   ![Captura de tela mostrando a ID do cliente OAuth e o segredo do cliente](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Etapa 2: configurar a Federação do Google no Azure AD 
Agora você definirá a ID do cliente do Google e o segredo do cliente, seja inserindo-o no portal do AD do Azure ou usando o PowerShell. Certifique-se de testar sua configuração do Google Federation convidando-se usando um endereço do Gmail e tentando resgatar o convite com sua conta convidada do Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Para configurar o Google Federation no portal do Azure AD 
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **provedores de identidade**e, em seguida, clique no botão **Google** .
4. Insira um nome. Em seguida, insira a ID do cliente e o segredo do cliente obtidos anteriormente. Selecione **Guardar**. 

   ![Captura de tela mostrando a página Adicionar provedor de identidade do Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Para configurar o Google Federation usando o PowerShell
1. Instale a versão mais recente do módulo do Azure AD PowerShell para Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute o seguinte comando: `Connect-AzureAD`.
3. No prompt de entrada, entre com a conta de administrador global gerenciada.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Use a ID do cliente e o segredo do cliente do aplicativo criado em "etapa 1: configurar um projeto de desenvolvedor do Google". Para obter mais informações, consulte o artigo [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 
 
## <a name="how-do-i-remove-google-federation"></a>Como fazer remover o Google Federation?
Você pode excluir a configuração do Google Federation. Se você fizer isso, os usuários convidados do Google que já tiverem resgatado seu convite não poderão entrar, mas você poderá conceder a eles acesso a seus recursos novamente, excluindo-os do diretório e convidando-os novamente. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Para excluir o Google Federation no portal do AD do Azure: 
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **provedores de identidade**.
4. Na linha do **Google** , selecione o menu de contexto ( **...** ) e, em seguida, selecione **excluir**. 
   
   ![Captura de tela mostrando a opção Excluir para o provedor de identidade social](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a exclusão. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Para excluir o Google Federation usando o PowerShell: 
1. Instale a versão mais recente do módulo do Azure AD PowerShell para Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute `Connect-AzureAD`.  
4. No prompt de logon, entre com a conta de administrador global gerenciada.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
