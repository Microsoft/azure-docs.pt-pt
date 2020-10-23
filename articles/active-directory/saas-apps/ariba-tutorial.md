---
title: 'Tutorial: Integração do Azure Ative Directory com a Ariba Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ariba.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 139aeb2fad37f96ad9e3d7fc801d574a93e896bd
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457707"
---
# <a name="tutorial-azure-active-directory-integration-with-ariba"></a>Tutorial: Integração do Diretório Ativo Azure com a Ariba

Neste tutorial, aprende-se a integrar a Ariba com o Azure Ative Directory (Azure AD).
A integração de Ariba com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Ariba.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Ariba (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Ariba, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por Ariba

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Ariba apoia **SP** iniciado SSO

* Uma vez configurado Ariba, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ariba-from-the-gallery"></a>Adicionando Ariba da galeria

Para configurar a integração de Ariba em Azure AD, você precisa adicionar Ariba da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Ariba** na caixa de pesquisa.
1. Selecione **Ariba** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, você configura e testa Azure AD single sign-on com Ariba com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Ariba.

Para configurar e testar o Azure AD com Ariba, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Ariba SSO](#configure-ariba-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Ariba test user](#create-ariba-test-user)** - ter uma contraparte de Britta Simon em Ariba que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Ariba,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações únicas de sign-on do Domínio de Ariba e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:
    
    ```http
    https://<subdomain>.sourcing.ariba.com
    https://<subdomain>.supplier.ariba.com
    ```

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `http://<subdomain>.procurement-2.ariba.com`

    c. Para **URL de resposta**, insira um dos seguintes padrões de URL:

    | URL de Resposta|
    |----------|
    | `https://<subdomain>.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com/CUSTOM_URL` |
    | `https://<subdomain>.procurement-eu.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com` |
    | `https://<subdomain>.procurement-2.ariba.com/CUSTOM_URL` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, o identificador e o URL de resposta. Aqui sugerimos que use o valor único da corda no Identificador. Contacte a equipa de apoio ao Cliente Ariba em **1-866-218-2155** para obter estes valores.. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 


Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **O Diretório Ativo Azure,** selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon**.  
   1. No campo **nome do utilizador,** insira `<username>@<companydomain>.<extension>` . Por exemplo: `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe Show** e, em seguida, tome nota do valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Ariba.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Ariba**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ariba-sso"></a>Configure Ariba SSO

Para obter sSo configurado para a sua aplicação, ligue para a equipa de apoio da Ariba **em 1-866-218-2155** e eles vão ajudá-lo ainda mais sobre como fornecer-lhes o arquivo certificado descarregado **(Base64).**

### <a name="create-ariba-test-user"></a>Criar utilizador de teste de Ariba

Nesta secção, cria-se um utilizador chamado Britta Simon em Ariba. Trabalhe com a equipa de apoio da Ariba em **1-866-218-2155** para adicionar os utilizadores na plataforma Ariba. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Ariba no Painel de Acesso, deverá ser automaticamente inscrito no Ariba para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)