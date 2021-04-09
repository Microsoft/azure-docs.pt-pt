---
title: Adicionar o Facebook como fornecedor de identidade - Azure AD
description: Federate com o Facebook para permitir que utilizadores externos (convidados) inscrevam-se nas suas aplicações AD Azure com as suas próprias contas no Facebook.
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
ms.openlocfilehash: bcacfc8cede0ab91241fd431aace10cbbfbea916
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709689"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Adicione o Facebook como fornecedor de identidades externas

Pode adicionar o Facebook aos fluxos de utilizador de inscrição de autosserviço para que os utilizadores possam iniciar sôm nas suas aplicações usando as suas próprias contas de Facebook. Para permitir que os utilizadores entrem no Facebook, primeiro terá de ativar a [inscrição de autosserviço](self-service-sign-up-user-flow.md) para o seu inquilino. Depois de adicionar o Facebook como fornecedor de identidade, crie um fluxo de utilizador para a aplicação e selecione o Facebook como uma das opções de entrada.

Depois de ter adicionado o Facebook como uma das opções de entrada da sua aplicação, no **Sinal na** página, um utilizador pode simplesmente introduzir o e-mail que usa para iniciar sins no Facebook, ou pode selecionar **opções de Iniciar s-in** e escolher **Iniciar sê-in com o Facebook.** Em qualquer dos casos, serão redirecionados para a página de login do Facebook para autenticação.

![Iniciar sinsções para utilizadores do Facebook](media/facebook-federation/sign-in-with-facebook-overview.png)

> [!NOTE]
> Os utilizadores só podem utilizar as suas contas do Facebook para se inscreverem através de apps que utilizam a inscrição de self-service e fluxos de utilizadores. Os utilizadores não podem ser convidados e resgatar o seu convite através de uma conta no Facebook.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Criar uma aplicação na consola de desenvolvedores do Facebook

Para utilizar uma conta do Facebook como fornecedor de [identidade,](identity-providers.md)é necessário criar uma aplicação na consola de desenvolvedores do Facebook. Se ainda não tiver uma conta no Facebook, pode inscrever-se em [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Utilize os seguintes URLs nos passos 9 e 16 abaixo.
> - Para **URL do site** insira o endereço da sua aplicação, tal como `https://contoso.com` .
> - Para **OAuth redirecionar URIs,** insira `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . Pode encontrar o seu `<tenant-ID>` na lâmina geral do Diretório Ativo Azure.


1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
2. Se ainda não o fez, tem de se registar como programador do Facebook. Para isso, **selecione Começar** no canto superior direito da página, aceite as políticas do Facebook e complete as etapas de inscrição.
3. Selecione **as minhas apps** e, em seguida, **crie app.**
4. Introduza um **Nome de Exibição** e um **e-mail de contacto** válido.
5. Selecione **Criar ID de aplicação.** Isto pode exigir que aceite as políticas da plataforma do Facebook e preencha uma verificação de segurança online.
6. Selecione **Definições**  >  **Básicas**.
7. Escolha uma **categoria,** por exemplo Negócios e Páginas. Este valor é exigido pelo Facebook, mas não é utilizado para a Azure AD.
8. Na parte inferior da página, selecione **Add Platform** e, em seguida, selecione **Website**.
9. No **URL do site,** insira o URL apropriado (indicado acima).
10. No **URL de Política de Privacidade,** insira o URL para a página onde mantém informações de privacidade para a sua aplicação, por exemplo `http://www.contoso.com` .
11. Selecione **Guardar alterações**.
12. No topo da página, copie o valor do ID da **aplicação.**
13. Selecione **Mostrar** e copie o valor da **App Secret.** Usa os dois para configurar o Facebook como fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança.
14. Selecione o sinal de mais ao lado **de PRODUTOS** e, em seguida, selecione **Configurar** no **Facebook Login**.
15. No **Início de Sessão do Facebook**, selecione **Definições**.
16. Em **IURD de redireccionamento válido,** insira o URL apropriado (acima referido).
17. Selecione **Guardar Alterações** na parte inferior da página.
18. Para disponibilizar a sua aplicação do Facebook ao Azure AD, selecione o seletor de Estado no topo direito da página e **ligue-o** para tornar a aplicação pública e, em seguida, selecione **o Modo Switch**. Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Viver**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar uma conta do Facebook como fornecedor de identidade
Agora você vai definir o ID do cliente do Facebook e o segredo do cliente, quer inserindo-o no portal AD Azure ou usando o PowerShell. Pode testar a sua configuração do Facebook inscrevendo-se através de um fluxo de utilizador numa aplicação ativada para inscrição de autosserviço.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Para configurar a federação do Facebook no portal AD AZure
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do seu inquilino AZure AD.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **Todos os fornecedores de identidade** e, em seguida, selecione **Facebook**.
5. Para o ID do **Cliente,** insira o ID da **aplicação** do Facebook que criou anteriormente.
6. Para o segredo do **Cliente,** insira a **App Secret** que gravou.

   ![Screenshot mostrando a página do provedor de identidade social Adicionar](media/facebook-federation/add-social-identity-provider-page.png)

7. Selecione **Guardar**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Para configurar a federação do Facebook usando o PowerShell
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Executar o seguinte comando: `Connect-AzureAD` .
3. Na 00da vez, inscreva-se na conta gerida do Administrador Global.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Utilize o ID do cliente e o segredo do cliente a partir da aplicação que criou acima na consola de desenvolvedores do Facebook. Para mais informações, consulte o artigo [New-AzureADMSIdentityProvider.](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true) 

## <a name="how-do-i-remove-facebook-federation"></a>Como retiro a federação do Facebook?
Pode eliminar a configuração da federação do Facebook. Se o fizer, qualquer utilizador que se tenha inscrito através dos fluxos de utilizadores com as suas contas do Facebook deixará de poder fazer login. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Para eliminar a federação do Facebook no portal AD Azure: 
1. Aceda ao [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **identidades externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Na linha do **Facebook,** selecione o menu de contexto **(...)** e, em seguida, selecione **Delete**. 
5. Selecione **Sim** para confirmar a eliminação.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Para eliminar a federação do Facebook utilizando o PowerShell: 
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Execute `Connect-AzureAD`.  
4. No pedido de inscrição, inscreva-se na conta gerida do Administrador Global.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="next-steps"></a>Passos seguintes

- [Adicione inscrição de self-service a uma aplicação](self-service-sign-up-user-flow.md)