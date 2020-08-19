---
title: 'Tutorial: Integração do Azure Ative Directory com o Saba TalentSpace Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 1743908652a70667a368dced18b2e808ce590a1b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Saba TalentSpace

Neste tutorial, você vai aprender a integrar Saba TalentSpace com Azure Ative Directory (Azure AD). Quando integrar o Saba TalentSpace com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Saba TalentSpace.
* Permita que os seus utilizadores sejam automaticamente inscritos no Saba TalentSpace com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Saba TalentSpace assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Saba TalentSpace apoia SSO iniciado **pela SP**
* Assim que configurar o Saba TalentSpace, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Adicionando Saba TalentSpace da galeria

Para configurar a integração do Saba TalentSpace no Azure AD, precisa adicionar saba TalentSpace da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite Saba TalentSpace** na caixa de pesquisa.
1. Selecione **Saba TalentSpace** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Configurar e testar Azure AD single sign-on para Saba TalentSpace

Configure e teste Azure AD SSO com Saba TalentSpace usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Saba TalentSpace.

Para configurar e testar o Azure AD SSO com o Saba TalentSpace, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Saba TalentSpace SSO](#configure-saba-talentspace-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Saba TalentSpace test user](#create-saba-talentspace-test-user)** - para ter uma contrapartida de B.Simon no Saba TalentSpace que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Saba TalentSpace,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://global.hgncloud.com/[companyname]/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Na caixa de texto **URL de resposta (URL do serviço de consumo de afirmação),** digite um URL utilizando o seguinte padrão: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de apoio ao cliente Saba TalentSpace](https://support.saba.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar Saba **TalentSpace,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Saba TalentSpace.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Saba TalentSpace**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-saba-talentspace-sso"></a>Configurar Saba TalentSpace SSO

1. Numa janela de navegador diferente, inscreva-se na sua aplicação **Saba TalentSpace** como administrador.

2. Clique na **opções** separador.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. No painel de navegação esquerdo, clique na **configuração SAML**.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Na página **de Configuração SAML,** execute os seguintes passos:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Como **Identificador Único,** selecione **NameID**.

    b. Como **mapas de identificador exclusivos para**, selecione **username**.
  
    c. Para fazer o upload do seu ficheiro de metadados descarregado, clique em **Procurar** para selecionar o ficheiro e, em seguida, **carregar o Ficheiro**.

    d. Para testar a configuração, clique em **'Teste de Execução'.**

    > [!NOTE]
    > Tens de esperar pela mensagem "*O teste SAML está completo. Por favor, feche esta janela".* Em seguida, feche a janela aberta do navegador. A caixa de verificação **Enable SAML** só está ativada se o teste tiver sido concluído.

    e. Selecione **Ativar SAML**.

    f. Clique em **Guardar Alterações**.

### <a name="create-saba-talentspace-test-user"></a>Criar utilizador de teste Saba TalentSpace

O objetivo desta secção é criar um utilizador chamado Britta Simon no Saba TalentSpace.

**Para criar um utilizador chamado Britta Simon em Saba TalentSpace, execute os seguintes passos:**

1. Inscreva-se na sua aplicação **Saba TalentSpace** como administrador.

2. Clique no separador **Centro de Utilização** e, em seguida, clique em **Criar Utilizador**.

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Na página de diálogo do **Novo Utilizador,** execute os seguintes passos:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Na caixa de texto **Name Name,** escreva o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Do Último Nome,** escreva o apelido do utilizador como **Simon**.

    c. Na caixa de texto **username,** tipo **B.Simon,** o nome de utilizador está no portal Azure.

    d. Na caixa de texto **da palavra-passe,** escreva uma palavra-passe para B.Simon.

    e. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Saba TalentSpace no Painel de Acesso, deverá ser automaticamente inscrito no Saba TalentSpace para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Saba TalentSpace com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)