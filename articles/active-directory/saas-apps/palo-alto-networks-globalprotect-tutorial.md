---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Palo Alto Networks - GlobalProtect Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Palo Alto Networks - GlobalProtect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: 2b8c74b8a456815400f6d68200ea93f43e3adff0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554056"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Azure Ative Directy integração única (SSO) com Palo Alto Networks - GlobalProtect

Neste tutorial, você vai aprender a integrar Palo Alto Networks - GlobalProtect com Azure Ative Directory (Azure AD). Quando integrar a Palo Alto Networks - GlobalProtect com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Palo Alto Networks - GlobalProtect.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Palo Alto Networks - GlobalProtect com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Palo Alto Networks - GlobalProtect única assinatura (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Palo Alto Networks - GlobalProtect suporta **SSO** iniciado SP
* Palo Alto Networks - GlobalProtect suporta provisão de utilizadores **just in time**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Adicionar Palo Alto Networks - GlobalProtect da galeria

Para configurar a integração da Palo Alto Networks - GlobalProtect em AD AZure, é necessário adicionar a Palo Alto Networks - GlobalProtect da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Palo Alto Networks - GlobalProtect** na caixa de pesquisa.
1. Selecione **Palo Alto Networks - GlobalProtect** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Configurar e testar Azure AD único sinal para Palo Alto Networks - GlobalProtect

Configure e teste Azure AD SSO com Palo Alto Networks - GlobalProtecte usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Palo Alto Networks - GlobalProtect.

Para configurar e testar a Azure AD SSO com a Palo Alto Networks - GlobalProtect, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Palo Alto Networks - GlobalProtect SSO](#configure-palo-alto-networks---globalprotect-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar a Palo Alto Networks - utilizador de teste GlobalProtect](#create-palo-alto-networks---globalprotect-test-user)** - para ter uma contrapartida de B.Simon em Palo Alto Networks - GlobalProtect que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **palo Alto Networks - GlobalProtect** application integration, encontre a secção **Gerir** e selecione um único sinal de **saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<Customer Firewall URL>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a Palo Alto Networks - GlobalProtect Client support team](https://support.paloaltonetworks.com/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar as Redes Palo Alto - GlobalProtect,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Palo Alto Networks - GlobalProtect.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Palo Alto Networks - GlobalProtect**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Configure Palo Alto Networks - GlobalProtect SSO

1. Abra o Palo Alto Networks Firewall Admin UI como administrador em outra janela do navegador.

2. Clique no **Dispositivo**.

    ![Configurar Palo Alto Single Sign-on](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecione O Fornecedor de **Identidade SAML** da barra de navegação esquerda e clique em "Importar" para importar o ficheiro de metadados.

    ![Configurar Palo Alto Single Sign-on](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Realizar as seguintes ações na janela Import

    ![Configurar Palo Alto Single Sign-on](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Na caixa de texto **'Nome perfil'** do Perfil, forneça um nome por exemplo Azure AD GlobalProtect.

    b. Nos **Metadados do Fornecedor de Identidade,** clique em **Procurar** e selecione o ficheiro metadata.xml que descarregou a partir do portal Azure

    c. Clique **em OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Criar Redes Palo Alto - GlobalProtect utilizador de testes

Nesta secção, um utilizador chamado B.Simon é criado em Palo Alto Networks - GlobalProtect. Palo Alto Networks - GlobalProtect suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Palo Alto Networks - GlobalProtect, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar nas Redes Palo Alto - Azulejo GlobalProtect no Painel de Acesso, deverá ser automaticamente inscrito nas Redes Palo Alto - GlobalProtect para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Experimente a Palo Alto Networks - GlobalProtect com Azure AD](https://aad.portal.azure.com/)
