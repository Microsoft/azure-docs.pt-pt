---
title: Configurar um aplicativo OpenID/OAuth na Galeria de aplicativos do Azure AD | Microsoft Docs
description: Etapas para configurar um aplicativo OpenID/OAuth na Galeria de aplicativos do Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbf9cde8dd2032e81abe0fb2572c2181d4ba21ee
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160210"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configurar um aplicativo OpenID/OAuth na Galeria de aplicativos do Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processo de adicionar um aplicativo OpenID da Galeria

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory](common/select-azuread.png))

2. Vá para **aplicativos empresariais** > **todos os aplicativos**.

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Selecione **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite o nome do aplicativo. Selecione o aplicativo desejado no painel de resultados e inscreva-se no aplicativo.

    ![OpenID na lista de resultados](common/search-new-app.png)

    > [!NOTE]
    > Para aplicativos do OpenID Connect e OAuth, o botão **Adicionar** é desabilitado por padrão. Aqui, o administrador do locatário deve selecionar o botão de inscrição e fornecer o consentimento para o aplicativo. O aplicativo é então adicionado ao locatário do cliente, no qual você pode fazer as configurações. Não é necessário adicionar o aplicativo explicitamente.

    ![Botão Adicionar](./media/openidoauth-tutorial/addbutton.png)

5. Ao selecionar o link de inscrição, você será redirecionado para a página Azure Active Directory (Azure AD) para credenciais de entrada.

6. Após a autenticação bem-sucedida, você aceita o consentimento da página de consentimento. Depois disso, o aplicativo home page é exibido.

    > [!NOTE]
    > Você pode adicionar apenas uma instância do aplicativo. Se você já tiver adicionado um e tentado fornecer o consentimento novamente, ele não será adicionado novamente no locatário. Logicamente, você pode usar apenas uma instância de aplicativo no locatário.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando o OpenID Connect

O fluxo de entrada mais básico contém as seguintes etapas:

![Fluxo de autenticação usando o OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplicativo multilocatário 
Um aplicativo multilocatário é destinado ao uso em muitas organizações, não apenas em uma organização. Esses são normalmente aplicativos de software como serviço (SaaS) escritos por um fornecedor independente de software (ISV). 

Aplicativos multilocatários precisam ser provisionados em cada diretório onde serão usados. Eles exigem o consentimento do usuário ou do administrador para registrá-los. Esse processo de consentimento é iniciado quando um aplicativo é registrado no diretório e recebe acesso ao API do Graph ou talvez a outra API da Web. Quando um usuário ou administrador de uma organização diferente se inscreve para usar o aplicativo, uma caixa de diálogo exibe as permissões que o aplicativo precisa. 

O usuário ou administrador pode então consentir o aplicativo. O consentimento fornece ao aplicativo acesso aos dados declarados e, por fim, registra o aplicativo no diretório.

> [!NOTE]
> Se estiver disponibilizando seu aplicativo para os usuários em vários diretórios, você precisará de um mecanismo para determinar em qual locatário eles estão. Um aplicativo de locatário único precisa apenas examinar seu próprio diretório para um usuário. Um aplicativo multilocatário precisa identificar um usuário específico de todos os diretórios no Azure AD.
> 
> Para realizar essa tarefa, o AD do Azure fornece um ponto de extremidade de autenticação comum em que qualquer aplicativo multilocatário pode direcionar solicitações de entrada, em vez de um ponto de extremidade específico do locatário. Esse ponto de extremidade é [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) para todos os diretórios no Azure AD. Um ponto de extremidade específico de locatário pode ser [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> O ponto de extremidade comum é importante considerar quando você está desenvolvendo seu aplicativo. Você precisará da lógica necessária para lidar com vários locatários durante a entrada, saída e validação de token.

Por padrão, o Azure AD promove aplicativos multilocatários. Eles são acessados facilmente entre organizações e são fáceis de usar depois que você aceita o consentimento.

## <a name="consent-framework"></a>Enquadramento do consentimento

Você pode usar a estrutura de consentimento do Azure AD para desenvolver aplicativos de cliente nativos e Web multilocatário. Esses aplicativos permitem a entrada por contas de usuário de um locatário do Azure AD, diferente daquele em que o aplicativo é registrado. Eles também podem precisar acessar APIs da Web, como:
- A API Microsoft Graph, para acessar o Azure AD, o Intune e os serviços no Office 365. 
- Outras APIs de serviços da Microsoft.
- Suas próprias APIs Web. 

A estrutura é baseada em um usuário ou administrador que dá consentimento a um aplicativo que solicita ser registrado em seu diretório. O registro pode envolver o acesso a dados do diretório. Após o consentimento ser fornecido, o aplicativo cliente pode chamar a API de Microsoft Graph em nome do usuário e usar as informações conforme necessário.

A [API Microsoft Graph](https://developer.microsoft.com/graph/) fornece acesso aos dados no Office 365, como:

- Calendários e mensagens do Exchange.
- Sites e listas do SharePoint.
- Documentos do OneDrive.
- Blocos de anotações do OneNote.
- Tarefas do planejador.
- Pastas de trabalho do Excel.

O API do Graph também fornece acesso a usuários e grupos do Azure AD e outros objetos de dados de mais serviços em nuvem da Microsoft.

As etapas a seguir mostram como a experiência de consentimento funciona para o desenvolvedor e o usuário do aplicativo:

1. Suponha que você tenha um aplicativo cliente Web que precisa solicitar permissões específicas para acessar um recurso ou uma API. O portal do Azure é usado para declarar solicitações de permissão no momento da configuração. Assim como outras definições de configuração, elas se tornam parte dos registros do Azure AD do aplicativo. Para o caminho de solicitação de permissão, você precisará do seguinte procedimento:

    a. Clique na **registros de aplicativo** do lado esquerdo do menu e abra o aplicativo digitando o nome do aplicativo na caixa de pesquisa.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Clique em **exibir permissões de API**.

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Clique em **Adicionar uma permissão**.

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Clique em **Microsoft Graph**.

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Selecione as opções necessárias de permissões **delegadas** e **permissões de aplicativo**.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Considere que as permissões do seu aplicativo foram atualizadas. O aplicativo está em execução e um usuário está prestes a usá-lo pela primeira vez. Primeiro, o aplicativo precisa obter um código de autorização do ponto de extremidade/Authorize do Azure AD. O código de autorização pode então ser usado para adquirir um novo token de acesso e de atualização.

3. Se o usuário ainda não estiver autenticado, o ponto de extremidade do Azure AD/Authorize solicitará a entrada.

    ![Autenticação](./media/openidoauth-tutorial/authentication.png)

4. Depois que o usuário tiver entrado, o AD do Azure determinará se o usuário precisa ser mostrado na página de consentimento. Essa determinação se baseia em se o usuário (ou o administrador da organização) já concedeu o consentimento do aplicativo.

   Se o consentimento não tiver sido concedido, o Azure AD solicitará o consentimento do usuário e exibirá as permissões necessárias que ele precisa para funcionar. As permissões exibidas na caixa de diálogo de consentimento correspondem àquelas selecionadas nas permissões delegadas no portal do Azure.

    ![Página de consentimento](./media/openidoauth-tutorial/consentpage.png)

Um usuário normal pode consentir algumas permissões. Outras permissões exigem o consentimento de um administrador de locatários.

## <a name="difference-between-admin-consent-and-user-consent"></a>Diferença entre consentimento de administrador e consentimento do usuário

Como administrador, você também pode concordar com as permissões delegadas de um aplicativo em nome de todos os usuários em seu locatário. O consentimento administrativo impede que a caixa de diálogo de consentimento apareça para cada usuário no locatário. Os usuários que têm a função administrador podem fornecer consentimento na portal do Azure. Na página **configurações** do seu aplicativo, selecione **as permissões necessárias** > **conceder consentimento de administrador**.

![Botão conceder permissões](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A concessão de consentimento explícito usando o botão **conceder consentimento de administrador** agora é necessária para aplicativos de página única (spas) que usam o Adal. js. Caso contrário, o aplicativo falhará quando o token de acesso for solicitado.

Permissões somente de aplicativo sempre exigem o consentimento de um administrador de locatários. Se seu aplicativo solicitar uma permissão somente de aplicativo e um usuário tentar entrar no aplicativo, uma mensagem de erro será exibida. A mensagem diz que o usuário não é capaz de consentir.

Se seu aplicativo usa permissões que exigem o consentimento do administrador, você precisa ter um gesto, como um botão ou link no qual o administrador pode iniciar a ação. A solicitação que seu aplicativo envia para essa ação é a solicitação de autorização OAuth2/OpenID Connect usual. Essa solicitação inclui o parâmetro de cadeia de caracteres de consulta *prompt = admin_consent* . 

Depois que o administrador tiver consentido e a entidade de serviço for criada no locatário do cliente, as solicitações de entrada posteriores não precisarão do parâmetro *prompt = admin_consent* . Como o administrador decidiu que as permissões solicitadas são aceitáveis, nenhum outro usuário no locatário é solicitado a fornecer consentimento desse ponto em diante.

Um administrador de locatários pode desabilitar a capacidade de usuários normais consentirem com os aplicativos. Se esse recurso estiver desabilitado, o consentimento do administrador sempre será necessário para que o aplicativo seja usado no locatário. Se você quiser testar seu aplicativo com o consentimento do usuário final desabilitado, poderá encontrar a opção de configuração no [portal do Azure](https://portal.azure.com/). Ele está na seção [configurações do usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) em **aplicativos empresariais**.

O parâmetro *prompt = admin_consent* também pode ser usado por aplicativos que solicitam permissões que não exigem o consentimento do administrador. Um exemplo é um aplicativo que requer uma experiência em que o administrador de locatários "se inscreve" uma vez e nenhum outro usuário é solicitado a fornecer consentimento desse ponto em diante.

Imagine que um aplicativo exija o consentimento do administrador e um administrador entre sem o parâmetro *prompt = admin_consent* sendo enviado. Quando o administrador consentiu com êxito ao aplicativo, ele se aplica somente à sua conta de usuário. Os usuários regulares ainda não poderão entrar ou consentir o aplicativo. Esse recurso será útil se você quiser conceder ao administrador de locatários a capacidade de explorar seu aplicativo antes de permitir o acesso de outros usuários.
