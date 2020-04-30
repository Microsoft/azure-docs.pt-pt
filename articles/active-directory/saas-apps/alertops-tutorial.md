---
title: 'Tutorial: Integração do Diretório Ativo Azure com AlertaS / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67107063"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Tutorial: Integrar Alertas com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar AlertOps com o Azure Ative Directory (Azure AD). Quando integrar alertas com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a AlertaOps.
* Ative que os seus utilizadores sejam automaticamente inscritos no AlertOps com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* AlertOps única subscrição ativada por sessão (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. AlertOps suporta **SP e IDP** iniciado SSO.

## <a name="adding-alertops-from-the-gallery"></a>Adicionar Alertas da galeria

Para configurar a integração de AlertOps em Azure AD, você precisa adicionar AlertOps da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Alertas** na caixa de pesquisa.
1. Selecione **AlertOps** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com AlertOps utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em AlertOps.

Para configurar e testar o Azure AD SSO com Alertas, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure os Alertas](#configure-alertops)** Para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste alertOps](#create-alertops-test-user)** para ter uma contrapartida de Britta Simon em Alertas que está ligada à representação da AD Azure do utilizador.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de **aplicações alertOps,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.alertops.com`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte do [Cliente alertops](mailto:support@alertops.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Configurar 'Alertas',** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Configure Alertas

1. Para automatizar a configuração dentro do AlertOps, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configuração alertaops** irá direcioná-lo para a aplicação AlertOps. A partir daí, forneça as credenciais de administração para assinar em AlertOps. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar os AlertOps manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa AlertOps como administrador e execute os seguintes passos:

4. Clique nas **definições** da Conta a partir do painel de navegação esquerdo.

    ![Configuração alertOps](./media/alertops-tutorial/configure1.png)

5. Na página Definições de **Subscrição,** selecione **SSO** e execute os seguintes passos:

    ![Configuração alertOps](./media/alertops-tutorial/configure2.png)

    a. Selecione Utilize uma caixa de **verificação de entrada única (SSO).**

    b. Selecione **Azure Ative Directory** como **fornecedor SSO** a partir da queda.

    c. Na caixa de texto **URL emitente,** utilize o valor do identificador, que utilizou na secção **de Configuração SAML Básica** no portal Azure.

    d. Na caixa de texto URL de **ponto final SAML,** colá o valor URL de **Login,** que copiou do portal Azure.

    e. Na caixa de texto URL de ponto final do **SLO,** colá o valor URL de **Login,** que copiou do portal Azure.

    f. Selecione **SHA256** como um Algoritmo de **Assinatura SAML** a partir do dropdown.

    g. Abra o ficheiro Certificado descarregado (Base64) no Bloco de Notas. Copie o conteúdo da sua pasta e cole-o na caixa de texto x.509.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que Britta Simon use o único sign-on Azure, concedendo acesso a AlertOps.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AlertOps**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-alertops-test-user"></a>Criar o utilizador de teste alertOps

1. Numa janela de navegador diferente, inscreva-se no site da empresa AlertOps como administrador.

2. Clique nos **Utilizadores** a partir do painel de navegação esquerdo.

    ![Configuração alertOps](./media/alertops-tutorial/user1.png)

3. Selecione **Adicionar utilizador**.

    ![Configuração alertOps](./media/alertops-tutorial/user2.png)

4. No diálogo **Adicionar utilizador,** execute os seguintes passos:

    ![Configuração alertOps](./media/alertops-tutorial/user3.png)

    a. Na caixa de texto **Sin User Name,** introduza o nome de utilizador do utilizador como **Brittasimon**.

    b. Na caixa de texto oficial do **email,** introduza o endereço de e-mail do utilizador como **\@Brittasimon contoso.com**.

    c. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    d. Na caixa de texto **De Apelido,** introduza o primeiro nome de utilizador como **Simon**.

    e. Selecione o valor **tipo** a partir do dropdown de acordo com a sua organização.

    f. Selecione o **Papel** do utilizador a partir do dropdown de acordo com a sua organização.

    g. Selecione **Adicionar**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo AlertOps no Painel de Acesso, deve ser automaticamente inscrito nos AlertaS para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)