---
title: Configurar uma aplicação de OpenID/OAuth a partir da Galeria de aplicações do Azure AD | Documentos da Microsoft
description: Passos para configurar uma aplicação de OpenID/OAuth a partir da Galeria de aplicações do Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713e4e7874b2ca650ab669d52f9d3026b5e80899
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780988"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configurar uma aplicação de OpenID/OAuth a partir da Galeria de aplicações do Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processo de adicionar uma aplicação de OpenID a partir da Galeria

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão do Azure Active Directory](common/select-azuread.png))

2. Aceda a **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva o nome da aplicação. Selecione a aplicação pretendida do painel de resultados e inscreva-se para a aplicação.

    ![Openid na lista de resultados](common/search-new-app.png)

    > [!NOTE]
    > Para aplicações de OpenID Connect e OAuth, a **adicionar** botão está desativado por predefinição. Aqui, o administrador de inquilino deve selecionar a inscrição botão e forneça o consentimento para a aplicação. O aplicativo, em seguida, é adicionado ao inquilino de cliente, onde pode fazer as configurações. Não é necessário para adicionar o aplicativo explicitamente.

    ![Botão Adicionar](./media/openidoauth-tutorial/addbutton.png)

5. Quando seleciona a ligação de inscrição, está redirecionado para a página do Azure Active Directory (Azure AD) para as credenciais de início de sessão.

6. Após a autenticação com êxito, está a aceitar o consentimento da página de consentimento. Depois disso, é apresentada a home page do aplicativo.

    > [!NOTE]
    > Pode adicionar apenas uma instância da aplicação. Se já tiver adicionado uma e tentou a fornecer o consentimento novamente, ele não será adicionado novamente no inquilino. Por isso, logicamente, pode usar apenas uma instância de aplicação no inquilino.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação através do OpenID Connect

O fluxo de início de sessão mais básico contém os seguintes passos:

![Fluxo de autenticação através do OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplicação multi-inquilino 
Uma aplicação multi-inquilino destina-se para uso em muitas organizações, não apenas uma organização. Estas são normalmente software-como-serviço (SaaS) aplicações escritas por um fabricante de software independente (ISV). 

Aplicações multi-inquilino tem de ser aprovisionado em cada diretório em que serão usados. Eles exigem o consentimento de utilizador ou administrador para registá-los. Este processo de consentimento é iniciado quando uma aplicação foi registrada no diretório e é dado acesso para a Graph API ou talvez outro web API. Quando um utilizador ou administrador a partir de uma organização diferente se inscreve para utilizar a aplicação, uma caixa de diálogo apresenta as permissões que o aplicativo precisa. 

O utilizador ou administrador, em seguida, pode autorizar a aplicação. O consentimento dá à aplicação acesso aos dados declarados e finalmente registra a aplicação no diretório.

> [!NOTE]
> Se estiver a fazer seu aplicativo disponível para utilizadores em vários diretórios, precisa de um mecanismo para determinar qual o inquilino estão na. Uma aplicação de inquilino único apenas precisa de ter um aspeto em seu próprio diretório para um utilizador. Precisa de uma aplicação multi-inquilino identificar um utilizador específico no todos os diretórios no Azure AD.
> 
> Para realizar esta tarefa, o Azure AD fornece um ponto de extremidade autenticação comum em que qualquer aplicação multi-inquilino pode direcionar o início de sessão solicitações, em vez de um ponto de extremidade específico de inquilino. Este ponto final fizer [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) para todos os diretórios no Azure AD. Pode ser um ponto de extremidade específico de inquilino [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> O ponto de extremidade comum é importante levar em consideração ao desenvolver seu aplicativo. Tem a lógica necessária para lidar com vários inquilinos durante o início de sessão, fim de sessão e a validação do token.

Por predefinição, o Azure AD promove aplicações multi-inquilino. São acedidos facilmente entre organizações, e são fáceis de usar depois de aceitar o consentimento.

## <a name="consent-framework"></a>Enquadramento do consentimento

Pode utilizar a estrutura de consentimento do Azure AD para desenvolver aplicações clientes nativas e web de multi-inquilino. Esses aplicativos permitem início de sessão por contas de utilizador de um inquilino do Azure AD diferente em que a aplicação fica registada. Também poderá ter de aceder a web APIs, tal como:
- O Microsoft Graph API, para acesso do Azure AD, o Intune e serviços do Office 365. 
- APIs de outros serviços da Microsoft.
- Suas próprias APIs web. 

O framework baseia-se um utilizador ou um administrador a dar o consentimento para um aplicativo que solicita a ser registados no respetivo diretório. O registo pode envolver a aceder a dados do diretório. Depois de consentimento for dado, a aplicação cliente pode chamar o Microsoft Graph API em nome do utilizador e utilize as informações conforme necessário.

O [Microsoft Graph API](https://developer.microsoft.com/graph/) fornece acesso aos dados no Office 365, como:

- Calendários e mensagens do Exchange.
- Sites e listas do SharePoint.
- Documentos do OneDrive.
- Blocos de notas do OneNote.
- Tarefas do Planner.
- Livros do Excel.

A Graph API também fornece acesso a utilizadores e grupos do Azure AD e outros objetos de dados de mais serviços cloud da Microsoft.

Os passos seguintes mostram como o consentimento refletir funciona para o desenvolvedor de aplicativos e o utilizador:

1. Suponha que tem uma aplicação de cliente web que precisa para solicitar permissões específicas para aceder um recurso ou API. O portal do Azure é utilizado para declarar as solicitações de permissão no momento da configuração. Como outras definições de configuração, que se tornem parte do registo da aplicação do Azure AD:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Considere que as permissões de seu aplicativo foram atualizadas. A execução do aplicativo e um utilizador estiver prestes a utilizá-lo pela primeira vez. O aplicativo tem primeiro de obter um código de autorização do Azure AD /autorizar ponto final. O código de autorização, em seguida, pode ser usado para adquirir um novo acesso e atualizar o token.

3. Se o utilizador já não for autenticado, o Azure AD / autorizar pedidos de ponto final de início de sessão.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Depois do utilizador tem sessão iniciada, o Azure AD determina se o utilizador tem de ser apresentada uma página de consentimento. Essa determinação baseia-se o utilizador (ou o administrador da sua organização) já concedeu o consentimento da aplicação.

   Se não tiver sido concedido o consentimento, o Azure AD solicita o consentimento do usuário e exibe as permissões necessárias que para funcionar. As permissões que são apresentadas na caixa de diálogo de consentimento corresponder aos perfis selecionados nas permissões de delegado no portal do Azure.

    ![Página de consentimento](./media/openidoauth-tutorial/consentpage.png)

Um utilizador normal pode consentir algumas permissões. Outras permissões requerem consentimento de um administrador de inquilino.

## <a name="difference-between-admin-consent-and-user-consent"></a>Diferença entre o consentimento de administrador e o consentimento do utilizador

Como administrador, pode também autorizar permissões delegadas de uma aplicação em nome de todos os utilizadores no seu inquilino. Consentimento administrativo impede que a caixa de diálogo de consentimento que aparece para cada utilizador no inquilino. Os utilizadores que têm a função de administrador podem fornecer consentimento no portal do Azure. Partir do **definições** página para a sua aplicação, selecione **permissões obrigatórias** > **conceder permissões**.

![Botão de conceder permissões](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Concessão de consentimento explícito, utilizando o **conceder permissões** botão agora é necessário para a página única (SPAs aplicativos) que utilizam ADAL.js. Caso contrário, o aplicativo falhar quando o token de acesso é solicitado.

Permissões só de aplicação necessitam sempre de consentimento de um administrador de inquilino. Se a aplicação solicite uma permissão só de aplicação e um utilizador tenta iniciar sessão na aplicação, é apresentada uma mensagem de erro. A mensagem diz que o utilizador não é capaz de dar consentimento.

Se seu aplicativo utiliza as permissões que requerem o consentimento de administrador, tem de ter um gesto, como um botão ou uma ligação em que o administrador pode iniciar a ação. O pedido que a aplicação envia para esta ação é o habitual pedido de autorização de OAuth2/OpenID Connect. Este pedido inclui o *perguntar = admin_consent* parâmetro de cadeia de caracteres de consulta. 

Depois do administrador já autorizou e o principal de serviço é criado no inquilino do cliente, os pedidos de início de sessão posteriores não precisam da *perguntar = admin_consent* parâmetro. Uma vez que o administrador tenha decidido que as permissões solicitadas são aceitáveis, não existem outros utilizadores no inquilino são lhe seja pedidos consentimento a partir desse ponto.

Um administrador inquilino pode desativar a capacidade dos usuários regulares dar consentimento a aplicações. Se esta capacidade estiver desativada, o consentimento de administrador é sempre necessário para que o aplicativo a ser utilizado no inquilino. Se pretender testar a sua aplicação com o consentimento do utilizador final desativado, pode encontrar o comutador de configuração no [portal do Azure](https://portal.azure.com/). Está a ser o [definições de utilizador](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) secção sob **aplicações empresariais**.

O *perguntar = admin_consent* parâmetro também pode ser utilizado por aplicações que solicitam permissões que não requerem o consentimento de administrador. Um exemplo é um aplicativo que requer uma experiência em que o inquilino "se inscreve" um tempo e não a outros utilizadores administradores são lhe for pedido consentimento a partir desse ponto em.

Imagine que um aplicativo exigir o consentimento de administrador e um administrador inicia sessão, sem o *perguntar = admin_consent* parâmetro a ser enviado. Quando o administrador autorizar com êxito a aplicação, aplica-se apenas para sua conta de utilizador. Usuários regulares ainda será possível iniciar sessão ou autorizar a aplicação. Esta funcionalidade é útil se quiser fornecer o administrador de inquilinos a capacidade de explorar a aplicação antes de permitir o acesso dos outros utilizadores.
