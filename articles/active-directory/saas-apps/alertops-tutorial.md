---
title: 'Tutorial: Integração do Azure Ative Directory com AlertOps Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o AlertOps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: a278204da556b41bd663b8b5c9ff5152e16017cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715875"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Tutorial: Integrar Alertas com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar AlertOps com Azure Ative Directory (Azure AD). Quando integra AlertOps com AD AZure, pode:

* Controlo em Azure AD que tem acesso a AlertOps.
* Capacitar os seus utilizadores a serem automaticamente inscritos nos AlertOps com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por AlertaOps (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. AlertOps suporta SSO iniciado **sp e IDP.**

## <a name="adding-alertops-from-the-gallery"></a>Adicionar Alertas da galeria

Para configurar a integração dos AlertOps no AD Azure, é necessário adicionar AlertOps da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **AlertOps** na caixa de pesquisa.
1. Selecione **AlertOps** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com AlertOps usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em AlertOps.

Para configurar e testar o Azure AD SSO com AlertOps, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure alertOps](#configure-alertops)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
4. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. **[Crie o utilizador de teste AlertOps](#create-alertops-test-user)** para ter uma contraparte de Britta Simon em AlertOps que está ligada à representação AD AZure do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **AlertOps,** encontre a secção **Gerir** e selecione **'Único sinal de sção'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.alertops.com`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente AlertOps](mailto:support@alertops.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar AlertaOps,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Configure Alertas

1. Para automatizar a configuração dentro dos AlertOps, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Alertas de Configuração** irá direcioná-lo para a aplicação AlertOps. A partir daí, forneça as credenciais de administração para assinar em AlertOps. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o AlertOps manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa AlertOps como administrador e execute os seguintes passos:

4. Clique nas **definições** de Conta a partir do painel de navegação esquerdo.

    ![A screenshot mostra o menu AlertOps com definições de conta chamadas.](./media/alertops-tutorial/configure1.png)

5. Na página Definições de **Subscrição** selecione **SSO** e execute os seguintes passos:

    ![A screenshot mostra a janela definições de subscrição de S S Com valores introduzidos como descrito neste passo.](./media/alertops-tutorial/configure2.png)

    a. Selecione Use a caixa de verificação **SSO (SSO) de** utilização única.

    b. Selecione **O Azure Ative Directory** como **Um Fornecedor SSO** a partir do dropdown.

    c. Na caixa de texto URL do **emitente,** utilize o valor do identificador, que utilizou na secção **Configuração Básica SAML** no portal Azure.

    d. Na caixa de texto URL do **ponto final DA SAML,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    e. Na caixa de texto **slo final de URL,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    f. Selecione **SHA256** como um algoritmo de **assinatura SAML** a partir do dropdown.

    exemplo, Abra o seu ficheiro Certificado (Base64) descarregado no Bloco de Notas. Copie o conteúdo da sua pasta e, em seguida, cole-o na caixa de texto do Certificado X.509.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso a AlertOps.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AlertOps**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-alertops-test-user"></a>Criar utilizador de teste AlertOps

1. Numa janela de navegador diferente, inscreva-se no site da empresa AlertOps como administrador.

2. Clique nos **Utilizadores** a partir do painel de navegação esquerdo.

    ![A screenshot mostra o menu AlertOps com os Utilizadores chamados.](./media/alertops-tutorial/user1.png)

3. Selecione **Adicionar Utilizador**.

    ![A screenshot mostra a janela do Utilizador com o botão Adicionar Utilizador.](./media/alertops-tutorial/user2.png)

4. No diálogo **do Utilizador Adicionar,** execute os seguintes passos:

    ![A screenshot mostra o painel de utilizadores add com valores introduzidos como descrito neste passo.](./media/alertops-tutorial/user3.png)

    a. Na caixa de texto **'Nome do Utilizador de Início** de Sessão', insira o nome de utilizador do utilizador como o **Brittasimon**.

    b. Na caixa de texto **oficial do Email,** insira o endereço de e-mail do utilizador como **o Brittasimon \@ contoso.com**.

    c. Na caixa de texto **Name Name,** insira o primeiro nome de utilizador como **Britta**.

    d. Na caixa de texto **Do Último Nome,** insira o primeiro nome de utilizador como **Simon**.

    e. Selecione o valor **Tipo** a partir do dropdown de acordo com a sua organização.

    f. Selecione o **Papel** do utilizador a partir do dropdown de acordo com a sua organização.

    exemplo, Selecione **Adicionar**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo AlertOps no Painel de Acesso, deverá ser automaticamente inscrito nos AlertOps para os quais configura SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)