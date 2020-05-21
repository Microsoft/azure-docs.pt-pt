---
title: Adicione o Facebook como fornecedor de identidade - Azure AD
description: Federate com o Facebook para permitir que utilizadores externos (hóspedes) inscrevam-se nas suas aplicações Azure AD com as suas próprias contas de Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0428671cf41bf148bc76bbd963bdd8fd90fce8e5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712275"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Adicione o Facebook como fornecedor de identidade para identidades externas

Pode adicionar o Facebook aos fluxos de utilizadores de inscrição de autosserviço (Pré-visualização) para que os utilizadores possam iniciar sessão nas suas aplicações utilizando as suas próprias contas de Facebook. Para permitir que os utilizadores assinem através do Facebook, primeiro terá de ativar o [autosserviço de inscrição](self-service-sign-up-user-flow.md) para o seu inquilino. Depois de adicionar o Facebook como fornecedor de identidade, configurar um fluxo de utilizador para a aplicação e selecionar o Facebook como uma das opções de entrada.

> [!NOTE]
> Os utilizadores só podem utilizar as suas contas do Facebook para se inscreverem através de apps utilizando o autosserviço de inscrição e fluxos de utilizadores. Os utilizadores não podem ser convidados e resgatar o seu convite através de uma conta no Facebook.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Criar uma aplicação na consola de desenvolvedores do Facebook

Para utilizar uma conta de Facebook como fornecedor de [identidade,](identity-providers.md)é necessário criar uma aplicação na consola de desenvolvedores do Facebook. Se ainda não tem uma conta no Facebook, pode inscrever-se em [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Utilize os seguintes URLs nos passos 9 e 16 abaixo.
> - Para introduzir **url do site** `https://login.microsoftonline.com` .
> - Para **redirecionar URIs válidos,** introduza `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . Pode encontrar a sua na lâmina de `<tenant-ID>` visão geral do Diretório Ativo Azure.


1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
2. Se ainda não o fez, tem de se registar como desenvolvedor do Facebook. Para isso, selecione **Get Started** no canto superior direito da página, aceite as políticas do Facebook e complete os passos de registo.
3. Selecione **as minhas apps** e, em seguida, **crie app**.
4. Introduza um Nome de **Exibição** e um email de **contacto**válido.
5. Selecione **Criar id**de aplicativo . Isto pode exigir que você aceite as políticas da plataforma do Facebook e complete uma verificação de segurança on-line.
6. Selecione **Definições**  >  **Básicas**.
7. Escolha uma **categoria,** por exemplo, Negócios e Páginas. Este valor é exigido pelo Facebook, mas não utilizado para a AD Azure.
8. Na parte inferior da página, selecione **Adicionar Plataforma**, e, em seguida, selecione **Website**.
9. No **URL do Site,** introduza o URL apropriado (acima indicado).
10. No URL da **Política de Privacidade,** introduza o URL para a página onde mantém informações de privacidade para a sua aplicação, por `http://www.contoso.com` exemplo.
11. Selecione **Guardar Alterações**.
12. No topo da página, copie o valor do ID da **App**.
13. Selecione **Mostrar** e copiar o valor da **App Secret**. Usa-os para configurar o Facebook como fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança.
14. Selecione o sinal de mais ao lado de **PRODUTOS**, e, em seguida, selecione **Configurar** sob **o Facebook Login**.
15. Em **login no Facebook,** selecione **Definições**.
16. Em **UrIs de redirecionamento OAuth válidos,** introduza o URL apropriado (acima indicado).
17. Selecione **Guardar Alterações** na parte inferior da página.
18. Para disponibilizar a sua aplicação do Facebook para a AD Azure, selecione o seletor de Estado no canto superior direito da página e **ligue-o** para tornar a aplicação pública e, em seguida, selecione **Modo Switch**. Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configure uma conta de Facebook como fornecedor de identidade
Agora irá definir o id do cliente do Facebook e o segredo do cliente, quer introduzindo-o no portal Azure AD ou usando o PowerShell. Pode testar a sua configuração do Facebook através de um fluxo de utilizador numa aplicação ativada para o autosserviço.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Para configurar a federação do Facebook no portal Azure AD
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador global do seu inquilino Azure AD.
2. Sob **os serviços azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **Todos os fornecedores de identidade**e, em seguida, selecione **Facebook**.
5. Para o ID do **cliente**, insira o ID da **app** da aplicação do Facebook que criou anteriormente.
6. Para o segredo do **Cliente,** insira o Segredo da **App** que gravou.

   ![Screenshot mostrando a página adicionar identidade social fornecedor](media/facebook-federation/add-social-identity-provider-page.png)

7. Selecione **Guardar**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Para configurar a federação do Facebook usando o PowerShell
1. Instale a versão mais recente do Azure AD PowerShell para módulo gráfico[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Executar o seguinte comando: `Connect-AzureAD` .
3. No momento de início de sessão, inscreva-se na conta gerida do Administrador Global.  
4. Execute o seguinte comando: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Utilize o ID do cliente e o segredo do cliente a partir da aplicação que criou acima na consola de desenvolvedores do Facebook. Para mais informações, consulte o artigo [New-AzureADMSIdentityProvider.](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 

## <a name="how-do-i-remove-facebook-federation"></a>Como posso remover a federação do Facebook?
Pode eliminar a sua configuração da federação do Facebook. Se o fizer, quaisquer utilizadores que se tenham inscrito através de fluxos de utilizadores com as suas contas de Facebook deixarão de poder fazer login. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Para eliminar a federação do Facebook no portal Azure AD: 
1. Vá ao [portal Azure.](https://portal.azure.com) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Na linha do **Facebook,** selecione o menu de contexto (**...**) e, em seguida, selecione **Delete**. 
5. Selecione **Sim** para confirmar a eliminação.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Para eliminar a federação do Facebook utilizando o PowerShell: 
1. Instale a versão mais recente do Azure AD PowerShell para módulo gráfico[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute `Connect-AzureAD`.  
4. No pedido de inscrição, inscreva-se na conta gerida do Administrador Global.  
5. Introduza o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Para mais informações, consulte [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 

## <a name="next-steps"></a>Próximos passos

- [Adicione inscrição de autosserviço a uma app](self-service-sign-up-user-flow.md)
