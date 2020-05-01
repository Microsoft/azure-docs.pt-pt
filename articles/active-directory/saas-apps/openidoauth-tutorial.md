---
title: Configure uma aplicação OpenID/OAuth da galeria de aplicações Azure AD [ Microsoft Docs
description: Passos para configurar uma aplicação OpenID/OAuth da galeria de aplicações Azure AD.
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
ms.custom: has-adal-ref
ms.openlocfilehash: 13c3a7f8376d4c852a74be75e323c6bb042b5407
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610994"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configure uma aplicação OpenID/OAuth da galeria de aplicações Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processo de adição de uma aplicação OpenID da galeria

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png))

2. Ir a **aplicações** > da Enterprise**Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o nome da aplicação. Selecione a aplicação desejada a partir do painel de resultados e inscreva-se na aplicação.

    ![Openid na lista de resultados](common/search-new-app.png)

    > [!NOTE]
    > Para aplicações OpenID Connect e OAuth, o botão **Adicionar** é desativado por padrão. Aqui o administrador do inquilino deve selecionar o botão de inscrição e fornecer o consentimento para o pedido. A aplicação é então adicionada ao inquilino do cliente, onde pode fazer as configurações. Não há necessidade de adicionar a aplicação explicitamente.

    ![Botão Adicionar](./media/openidoauth-tutorial/addbutton.png)

5. Ao selecionar o link de inscrição, é redirecionado para a página azure Ative Directory (Azure AD) para credenciais de inscrição.

6. Após autenticação bem sucedida, aceita o consentimento da página de consentimento. Depois disso, aparece a página inicial da aplicação.

    > [!NOTE]
    > Só pode adicionar uma instância da aplicação. Se já adicionou um e tentou dar o consentimento novamente, não será adicionado novamente no inquilino. Então logicamente, você pode usar apenas uma instância de aplicativo no inquilino.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação usando OpenID Connect

O fluxo de entrada mais básico contém os seguintes passos:

![Fluxo de autenticação usando OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplicação multiarrendatária
Uma aplicação multiarrendatária destina-se a ser usada em muitas organizações, e não apenas numa organização. Estas são aplicações tipicamente de software como um serviço (SaaS) escritas por um fornecedor de software independente (ISV).

As aplicações multiarrendatárias têm de ser aprovisionadas em cada diretório onde serão utilizadas. Exigem que o consentimento do utilizador ou administrador os registe. Este processo de consentimento começa quando uma aplicação foi registada no diretório e tem acesso à API do Gráfico ou talvez a outra API web. Quando um utilizador ou administrador de uma organização diferente se inscreve para utilizar a aplicação, uma caixa de diálogo apresenta as permissões de que a aplicação necessita.

O utilizador ou administrador pode então consentir com a aplicação. O consentimento dá à aplicação acesso aos dados indicados e, finalmente, regista o pedido no diretório.

> [!NOTE]
> Se está a disponibilizar a sua aplicação aos utilizadores em vários diretórios, precisa de um mecanismo para determinar em que inquilino estão. Uma aplicação de inquilino único só precisa de olhar no seu próprio diretório para um utilizador. Uma aplicação multiarrendatária precisa identificar um utilizador específico de todos os diretórios da AD Azure.
>
> Para realizar esta tarefa, a Azure AD fornece um ponto final de autenticação comum onde qualquer aplicação multiarrendatária pode dirigir pedidos de inscrição, em vez de um ponto final específico do arrendatário. Este ponto `https://login.microsoftonline.com/common` final é para todos os diretórios em Azure AD. Um ponto final específico `https://login.microsoftonline.com/contoso.onmicrosoft.com`do inquilino pode ser.
>
> O ponto final comum é importante de considerar quando está a desenvolver a sua aplicação. Você precisará da lógica necessária para lidar com vários inquilinos durante a inscrição, inscrição e validação simbólica.

Por predefinição, a Azure AD promove aplicações multiarrendatárias. São facilmente acedidos através de organizações, e são fáceis de usar depois de aceitaro consentimento.

## <a name="consent-framework"></a>Enquadramento do consentimento

Você pode usar o quadro de consentimento da AD Azure para desenvolver aplicações de clientes web e nativos multiarrendatários. Estas aplicações permitem o registo por conta de utilizador de um inquilino da AD Azure, diferente daquele em que a aplicação está registada. Podem também ter de aceder a APIs web tais como:
- O Microsoft Graph API, para aceder à Azure AD, Intune e serviços no Office 365.
- APIs de outros serviços da Microsoft.
- A tua própria web APIs.

O quadro baseia-se num utilizador ou num administrador que dá consentimento a uma aplicação que pede para ser registada no seu diretório. O registo pode envolver o acesso a dados de diretório. Após o consentimento, a aplicação do cliente pode ligar para a Microsoft Graph API em nome do utilizador e usar a informação conforme necessário.

O [Microsoft Graph API](https://developer.microsoft.com/graph/) fornece acesso a dados no Office 365, como:

- Calendários e mensagens da Exchange.
- Sites e listas do SharePoint.
- Documentos da OneDrive.
- Cadernos do OneNote.
- Tarefas do Planner.
- Livros do Excel.

O Graph API também fornece acesso a utilizadores e grupos de AD Azure e outros objetos de dados de mais serviços na nuvem da Microsoft.

Os seguintes passos mostram-lhe como funciona a experiência de consentimento para o desenvolvedor e utilizador da aplicação:

1. Assuma que tem uma aplicação de cliente web que precisa de solicitar permissões específicas para aceder a um recurso ou API. O portal Azure é utilizado para declarar pedidos de permissão no momento da configuração. À semelhança de outras configurações de configuração, tornam-se parte dos registos da AD Azure da aplicação. Para o caminho de pedido de permissão, você precisa seguir os passos abaixo:

    a. Clique nas **inscrições** da App do lado esquerdo do menu e abra a sua aplicação digitando o nome da aplicação na caixa de pesquisa.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Clique em **ver permissões API**.

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Clique em **Adicionar uma permissão**.

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Clique no **gráfico da Microsoft**.

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Selecione opções necessárias a partir **de permissões delegadas** e **permissões**de aplicação .

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Considere que as permissões do seu pedido foram atualizadas. A aplicação está em execução e um utilizador está prestes a usá-la pela primeira vez. Primeiro, o pedido precisa de obter um código de autorização da AD Azure /ponto final. O código de autorização pode então ser utilizado para adquirir um novo acesso e um sinal de atualização.

3. Se o utilizador ainda não estiver autenticado, o Azure AD /autorizar solicitações de ponto final para iniciar sessão.

    ![Autenticação](./media/openidoauth-tutorial/authentication.png)

4. Depois de o utilizador ter assinado o contrato, a Azure AD determina se o utilizador precisa de ser mostrado uma página de consentimento. Esta determinação baseia-se no facto de o utilizador (ou administrador da sua organização) já ter concedido o consentimento da aplicação.

   Se o consentimento não tiver sido concedido, a AD Azure solicita ao utilizador o consentimento e apresenta as permissões necessárias para que funcione. As permissões apresentadas na caixa de diálogo de consentimento coincidem com as selecionadas nas permissões delegadas no portal Azure.

    ![Página de consentimento](./media/openidoauth-tutorial/consentpage.png)

Um utilizador regular pode consentir com algumas permissões. Outras permissões requerem o consentimento de um administrador inquilino.

## <a name="difference-between-admin-consent-and-user-consent"></a>Diferença entre o consentimento do administrador e o consentimento do utilizador

Como administrador, também pode consentir com as permissões delegadas de uma aplicação em nome de todos os utilizadores do seu inquilino. O consentimento administrativo impede que a caixa de diálogo de consentimento apareça para todos os utilizadores do inquilino. Os utilizadores que tenham a função de administrador podem fornecer consentimento no portal Azure. A partir da página **Definições** para a sua aplicação, selecione O consentimento de**administrador**de **permissões exigidas** > .

![Botão de permissões de concessão](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A concessão de um consentimento explícito utilizando o botão de consentimento do **administrador Grant** é agora necessária para aplicações de uma página única (SPAs) que usam ADAL.js. Caso contrário, o pedido falha quando o token de acesso é solicitado.

As permissões apenas para aplicações requerem sempre o consentimento de um administrador inquilino. Se a sua aplicação solicitar uma permissão apenas para aplicações e um utilizador tentar iniciar sessão na aplicação, aparece uma mensagem de erro. A mensagem diz que o utilizador não pode consentir.

Se o seu pedido utilizar permissões que requerem consentimento administrativo, precisa de ter um gesto como um botão ou link onde o administrador possa iniciar a ação. O pedido que o seu pedido envia para esta ação é o habitual pedido de autorização OAuth2/OpenID Connect. Este pedido inclui o parâmetro de corda *de consulta pronta=admin_consent.*

Depois de o administrador ter consentido e o diretor de serviço ser criado no inquilino do cliente, os pedidos de inscrição posteriores não precisam do parâmetro *de pronta=admin_consent.* Como o administrador decidiu que as permissões solicitadas são aceitáveis, nenhum outro utilizador no inquilino é solicitado para o consentimento a partir desse ponto.

Um administrador de inquilino pode desativar a capacidade de os utilizadores regulares consentirem com as candidaturas. Se esta capacidade for desativada, o consentimento do administrador é sempre necessário para que o pedido seja utilizado no inquilino. Se pretender testar a sua aplicação com o consentimento do utilizador final desativado, pode encontrar o interruptor de configuração no [portal Azure](https://portal.azure.com/). Está na secção de [definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) do Utilizador sob **aplicações da Enterprise**.

O parâmetro *prompt=admin_consent* também pode ser usado por aplicações que solicitam permissões que não requerem consentimento administrativo. Um exemplo é uma aplicação que requer uma experiência em que o administrador do inquilino "inscreve-se" uma vez, e nenhum outro utilizador é solicitado para o consentimento a partir desse ponto.

Imagine que um pedido requer consentimento administrativo, e um administrador entra sem que o *parâmetro de admin_consent seja* enviado. Quando o administrador consente com sucesso na aplicação, aplica-se apenas à sua conta de utilizador. Os utilizadores regulares continuarão a não conseguir iniciar sessão ou a consentir com a aplicação. Esta funcionalidade é útil se pretender dar ao administrador inquilino a capacidade de explorar a sua aplicação antes de permitir o acesso de outros utilizadores.
