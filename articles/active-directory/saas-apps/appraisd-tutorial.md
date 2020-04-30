---
title: 'Tutorial: Integração do Diretório Ativo Azure com appraisd Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67561205"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Tutorial: Integrar appraisd com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar appraisd com O Diretório Ativo Azure (Azure AD). Quando integrar a Appraisd com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Appraisd.
* Ative que os seus utilizadores sejam automaticamente inscritos na Appraisd com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Appraisd suporta **SP e IDP** iniciado SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Adicionando Appraisd da galeria

Para configurar a integração da Appraisd em Azure AD, precisa adicionar Appraisd da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite Appraisd** na caixa de pesquisa.
1. Selecione **Appraisd** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Appraisd utilizando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Appraisd.

Para configurar e testar o Azure AD SSO com appraisd, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a appraisd](#configure-appraisd)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B. Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador de teste appraisd](#create-appraisd-test-user)** para ter uma contraparte de B. Simon em Appraisd que está ligada à representação da AD Azure do utilizador.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **appraisd,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica do SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão Guardar e executar os seguintes passos:

    a. Clique em **definir URLs adicionais**.

    b. Na caixa de texto **do Estado relé,** escreva um URL:`<TENANTCODE>`

    c. Se desejar configurar a aplicação no modo iniciado por **SP,** na caixa de texto **url sign-on,** digite um URL utilizando o seguinte padrão:`https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Obtém o valor real do Estado de Inscrição e Retransmissão na página de Configuração SSO appraisd que é explicada mais tarde no tutorial.

1. A aplicação appraisd espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação appraisd espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar a **Appraisd,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Configure Appraisd

1. Para automatizar a configuração dentro do Appraisd, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Appraisd** irá direcioná-lo para a aplicação Appraisd. A partir daí, forneça as credenciais de administração para assinar em Appraisd. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Appraisd manualmente, abra uma nova janela do navegador web e inscreva-se no site da sua empresa appraisd como administrador e execute os seguintes passos:

4. Na parte superior direita da página, clique no ícone **Definições** e, em seguida, navegue para **Configuração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Do lado esquerdo do menu, clique no **saml single sign-on**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na página de **configuração SAML 2.0 Single Sign-On,** execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copie o valor do Estado de **Retransmissão Predefinido** e cole-o na caixa de texto **do Estado de Retransmissão** na **configuração Básica SAML** no portal Azure.

    b. Copie o valor URL **de login iniciado pelo serviço** e cole-o na caixa de texto URL **sign-on** na **configuração Básica SAML** no portal Azure.

7. Percorra a mesma página em **utilizadores de identificação,** execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Na caixa de texto **single sign-on URL** do fornecedor de identidade, colá o valor do URL de **Login,** que copiou do portal Azure e clique em **Guardar**.

    b. Na caixa de texto URL do Fornecedor de **Identidade,** colhe o valor do **Identificador AD Azure,** que copiou do portal Azure e clique em **Guardar**.

    c. No Notepad, abra o certificado codificado base-64 que descarregou do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa de **certificadoX.509** e clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B. Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que B. Simon utilize um único sign-on azure, concedendo acesso à Appraisd.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Appraisd**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-appraisd-test-user"></a>Criar utilizador de teste Appraisd

Para permitir que os utilizadores de Anúncios Azure inscrevam-se na Appraisd, devem ser aprovisionados em Appraisd. Em Appraisd, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na Appraisd como Administrador de Segurança.

2. Na parte superior direita da página, clique no ícone **Definições** e, em seguida, navegue para o centro de **administração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas no topo da página, clique em **Pessoas,** depois navegue para **Adicionar um novo utilizador**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na **página adicionar uma nova** página de utilizador, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Na caixa de texto **de primeiro nome,** introduza o primeiro nome de utilizador como **Britta**.

    b. Na caixa de texto **de apelido,** introduza o último nome do utilizador como **simon**.

    c. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como `B. Simon@contoso.com`.

    d. Clique em **Adicionar utilizador**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo Appraisd no Painel de Acesso, deve ser automaticamente inscrito no Appraisd para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
