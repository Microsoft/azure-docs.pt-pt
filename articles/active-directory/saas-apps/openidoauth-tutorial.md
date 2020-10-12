---
title: Configure uma aplicação OpenID/OAuth da galeria de aplicações AZure AD ! Microsoft Docs
description: Passos para configurar uma aplicação OpenID/OAuth da galeria de aplicações AZure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: f36c80b9d08f2fde07483c1dde3afe99ec9f92d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705627"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Configure uma aplicação OpenID/OAuth da galeria de aplicações AZure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processo de adição de uma aplicação OpenID da galeria

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png))

2. Ir para **aplicações da Enterprise**  >  **Todas as aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o nome da aplicação. Selecione a aplicação desejada do painel de resultados e inscreva-se na aplicação.

    ![Openid na lista de resultados](common/search-new-app.png)

    > [!NOTE]
    > Para as aplicações OpenID Connect e OAuth, o botão **Adicionar** é desativado por predefinição. Aqui o administrador do inquilino deve selecionar o botão de inscrição e fornecer o consentimento para o pedido. A aplicação é então adicionada ao inquilino do cliente, onde pode fazer as configurações. Não há necessidade de adicionar a aplicação explicitamente.

    ![Botão Adicionar](./media/openidoauth-tutorial/addbutton.png)

5. Quando seleciona o link de inscrição, é redirecionado para a página Azure Ative Directory (Azure AD) para obter credenciais de inscrição.

6. Após a autenticação bem sucedida, aceita o consentimento da página de consentimento. Depois disso, aparece a página inicial da aplicação.

    > [!NOTE]
    > Pode adicionar apenas uma instância da aplicação. Se já adicionou um e tentou dar o consentimento novamente, não voltará a ser adicionado ao arrendatário. Logicamente, você pode usar apenas uma instância de aplicação no inquilino.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação utilizando o OpenID Connect

O fluxo de entrada mais básico contém os seguintes passos:

![Fluxo de autenticação utilizando o OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplicação multitenant
Uma aplicação multitenant destina-se a ser usada em muitas organizações, e não apenas numa organização. Estas são aplicações tipicamente software-as-a-service (SaaS) escritas por um fornecedor de software independente (ISV).

As aplicações multitenantes devem ser a provisionadas em cada diretório onde serão usadas. Exigem o consentimento do utilizador ou do administrador para os registar. Este processo de consentimento começa quando um pedido foi registado no diretório e é dado acesso à API do Gráfico ou talvez a outra API web. Quando um utilizador ou administrador de uma organização diferente se inscreve para utilizar a aplicação, uma caixa de diálogo exibe as permissões de que a aplicação necessita.

O utilizador ou administrador pode então consentir com a aplicação. O consentimento dá acesso ao pedido aos dados indicados e, finalmente, regista o pedido no diretório.

> [!NOTE]
> Se está a disponibilizar a sua aplicação aos utilizadores em vários diretórios, precisa de um mecanismo para determinar em que inquilino estão. Uma aplicação de um único inquilino só precisa de procurar no seu próprio diretório para um utilizador. Uma aplicação multitenant precisa identificar um utilizador específico de todos os diretórios em Azure AD.
>
> Para realizar esta tarefa, a Azure AD fornece um ponto final de autenticação comum onde qualquer aplicação multitenante pode dirigir pedidos de inscrição, em vez de um ponto final específico do inquilino. Este ponto final é `https://login.microsoftonline.com/common` para todos os diretórios em Azure AD. Um ponto final específico do inquilino pode `https://login.microsoftonline.com/contoso.onmicrosoft.com` ser.
>
> O ponto final comum é importante de considerar quando está a desenvolver a sua aplicação. Você precisará da lógica necessária para lidar com vários inquilinos durante a sindúsia, súmis e validação simbólica.

Por padrão, a Azure AD promove aplicações multitenantes. São facilmente acedidos através de organizações, e são fáceis de usar depois de aceitar o consentimento.

## <a name="consent-framework"></a>Enquadramento do consentimento

Você pode usar o quadro de consentimento Azure AD para desenvolver aplicações multitenantes web e clientes nativos. Estas aplicações permitem a inscrição por conta de utilizador de um inquilino AZure AD, diferente daquele em que a aplicação está registada. Podem também ter de aceder a APIs web, tais como:
- A Microsoft Graph API, para aceder a Azure AD, Intune e serviços na Microsoft 365.
- APIs de outros serviços da Microsoft.
- As tuas próprias APIs web.

O quadro baseia-se num utilizador ou administrador que dá consentimento a uma aplicação que pede para ser registada no seu diretório. O registo pode envolver o acesso aos dados do diretório. Após o consentimento ser dado, a aplicação do cliente pode ligar para a API do Gráfico Microsoft em nome do utilizador e utilizar as informações conforme necessário.

A [API do Microsoft Graph](https://developer.microsoft.com/graph/) fornece acesso a dados no Microsoft 365, como:

- Calendários e mensagens de Exchange.
- Sites e listas do SharePoint.
- Documentos da OneDrive.
- Cadernos do OneNote.
- Tarefas do Planejador.
- Livros do Excel.

A API do Gráfico também fornece acesso a utilizadores e grupos a partir de Azure AD e outros objetos de dados de mais serviços na nuvem da Microsoft.

Os seguintes passos mostram-lhe como funciona a experiência de consentimento para o desenvolvedor de aplicações e utilizador:

1. Assuma que tem uma aplicação de cliente web que precisa solicitar permissões específicas para aceder a um recurso ou API. O portal Azure é utilizado para declarar pedidos de permissão na hora da configuração. Tal como outras definições de configuração, tornam-se parte dos registos Azure AD da aplicação. Para o caminho de pedido de permissão de permissão, precisa seguir os passos abaixo:

    a. Clique nas **inscrições** da App a partir do lado esquerdo do menu e abra a sua aplicação digitando o nome da aplicação na caixa de pesquisa.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Clique **em Ver Permissões API**.

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Clique em **Adicionar uma permissão**.

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Clique no **Gráfico microsoft**.

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Selecione as opções necessárias a partir de **permissões delegadas** e **permissões de aplicação.**

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Considere que as permissões da sua aplicação foram atualizadas. A aplicação está em execução e um utilizador está prestes a usá-la pela primeira vez. Em primeiro lugar, o pedido precisa de obter um código de autorização do Azure AD /authorize endpoint. O código de autorização pode então ser usado para adquirir um novo acesso e atualização token.

3. Se o utilizador ainda não estiver autenticado, o Azure AD /autoriza as indicações de entrada.

    ![Uma screenshot do pedido de inscrição para a conta](./media/openidoauth-tutorial/authentication.png)

4. Depois de o utilizador ter assinado, o Azure AD determina se o utilizador precisa de ser mostrado uma página de consentimento. Esta determinação baseia-se no facto de o utilizador (ou administrador da sua organização) já ter concedido o consentimento do pedido.

   Se o consentimento não tiver sido concedido, a Azure AD solicita o consentimento do utilizador e exibe as permissões necessárias para o seu funcionamento. As permissões apresentadas na caixa de diálogo de consentimento coincidem com as selecionadas nas permissões delegadas no portal Azure.

    ![Página de consentimento](./media/openidoauth-tutorial/consentpage.png)

Um utilizador regular pode consentir com algumas permissões. Outras permissões requerem o consentimento de um administrador do inquilino.

## <a name="difference-between-admin-consent-and-user-consent"></a>Diferença entre o consentimento administrativo e o consentimento do utilizador

Como administrador, também pode consentir com as permissões delegadas de uma aplicação em nome de todos os utilizadores do seu inquilino. O consentimento administrativo impede que a caixa de diálogo de consentimento apareça para todos os utilizadores do arrendatário. Os utilizadores que tenham a função de administrador podem fornecer consentimento no portal Azure. A partir da página **Definições** para a sua aplicação, **selecione Permissões Necessárias**  >  **Conceder consentimento administrativo**.

![Botão de permissões de concessão](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A concessão de consentimento explícito utilizando o botão **de consentimento de administração Grant** é agora necessária para aplicações de uma página (SPAs) que usam ADAL.js. Caso contrário, a aplicação falha quando o token de acesso é solicitado.

Permissões só de aplicativos requerem sempre o consentimento de um administrador do inquilino. Se a sua aplicação solicitar uma permissão apenas para aplicações e um utilizador tentar iniciar sôm na aplicação, aparece uma mensagem de erro. A mensagem diz que o utilizador não pode consentir.

Se a sua aplicação utilizar permissões que exijam consentimento administrativo, tem de ter um gesto como um botão ou ligação onde o administrador pode iniciar a ação. O pedido que o seu pedido envia para esta ação é o habitual pedido de autorização OAuth2/OpenID Connect. Este pedido inclui o parâmetro de cadeia *de consulta de consulta prompt=admin_consent.*

Depois de o administrador ter consentido e o diretor de serviço ser criado no arrendatário do cliente, os pedidos de inscrição posteriores não precisam do parâmetro *de prompt=admin_consent.* Uma vez que o administrador decidiu que as permissões solicitadas são aceitáveis, nenhum outro utilizadores no arrendatário é solicitado a pedir o consentimento a partir desse ponto.

Um administrador de inquilino pode desativar a capacidade de os utilizadores regulares consentirem com as aplicações. Se esta capacidade for desativada, o consentimento administrativo é sempre necessário para que o pedido seja utilizado no arrendatário. Se pretender testar a sua aplicação com o consentimento do utilizador final desativado, pode encontrar o interruptor de configuração no [portal Azure](https://portal.azure.com/). Está na secção [de definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) do Utilizador sob **aplicações da Enterprise.**

O parâmetro *prompt=admin_consent* também pode ser usado por aplicações que solicitam permissões que não requerem consentimento administrativo. Um exemplo é uma aplicação que requer uma experiência em que o administrador do arrendatário "inscreve-se" uma vez, e nenhum outro utilizadores é solicitado a consentir a partir desse ponto.

Imagine que uma aplicação requer consentimento administrativo, e um sinal de administração sem o parâmetro *de prompt=admin_consent* sendo enviado. Quando o administrador consente com sucesso na aplicação, aplica-se apenas à sua conta de utilizador. Os utilizadores regulares continuarão a não conseguir iniciar sê-lo ou consentir a aplicação. Esta funcionalidade é útil se quiser dar ao administrador inquilino a capacidade de explorar a sua aplicação antes de permitir o acesso de outros utilizadores.
