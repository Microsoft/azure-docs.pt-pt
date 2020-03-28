---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com hRworks single sign-on [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o HRworks Single Sign-On.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75638767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com hRworks single sign-On

Neste tutorial, você aprenderá a integrar hRworks single sign-on com Azure Ative Directory (Azure AD). Quando integrar o HRworks Single Sign-On com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a HRworks Single Sign-On.
* Ative que os seus utilizadores sejam automaticamente inscritos no HRworks Single Sign-On com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* HRworks Single Sign-On single sign-on (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* HRworks Single Sign-On suporta **SP** iniciado SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Adicionando HRworks Single Sign-On da galeria

Para configurar a integração do HRworks Single Sign-On em Azure AD, você precisa adicionar HRworks Single Sign-On da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **HRworks Single Sign-On** na caixa de pesquisa.
1. Selecione **HRworks Single Sign-On** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Configure e teste Azure AD único sign-on para HRworks Single Sign-On

Configure e teste Azure AD SSO com HRworks Single Sign-On utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no HRworks Single Sign-On.

Para configurar e testar o Azure AD SSO com hRworks single sign-on, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure HRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - para configurar as definições de sinal única no lado da aplicação.
    * Crie o utilizador de **[teste Single Sign-On da HRworks](#create-hrworks-single-sign-on-test-user)** - para ter uma contraparte de B.Simon em HRworks Single Sign-On que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **De Entrada Única hRworks,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte do Cliente De Assinatura Única da [HRworks](mailto:nadja.sommerfeld@hrworks.de) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar hRworks single sign-on,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao HRworks Single Sign-On.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **HRworks Single Sign-On**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-hrworks-single-sign-on-sso"></a>Configure HRworks Single Sign-On SSO

1. Para automatizar a configuração dentro do HRworks Single Sign-On, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar HRworks Single Sign-On** irá direcioná-lo para a aplicação HRworks Single Sign-On. A partir daí, forneça as credenciais de administração para assinar no HRworks Single Sign-On. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o HRworks Single Sign-On manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa HRworks Single Sign-On como administrador e execute os seguintes passos:

1. Clique no **Administrador** > Basics**Security** > **Single Sign-on** do lado esquerdo da barra de menu sintetizador e execute os**seguintes passos:** > 

    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Verifique a caixa **de sinalização single Use.**

    b. Selecione **Metadados XML** como **método de entrada**de dados Meta .

    c. Selecione **identificador NameID individual** como **Valor para NameID**.

    d. No Notepad, abra o Metadata XML que descarregou do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa de texto **metadados.**

    e. Clique em **Guardar**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Criar o utilizador de teste de sinal único hRworks

Para ativar os utilizadores de Anúncios Azure, inscreva-se no HRworks Single Sign-On, devem ser aprovisionados no HRworks Single Sign-On. No HRworks Single Sign-On, o fornecimento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no HRworks Single Sign-On como administrador.

1. Clique em > **Pessoas** >  **Administrator****Administradoras Pessoas** > Nova**pessoa** do lado esquerdo da barra de menu.

     ![Configurar um único sinal](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. No Pop-up, clique em **Next**.

    ![Configurar um único sinal](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Na **Create new person with country for legal terms** pop-up, preencha os respetivos detalhes como Primeiro **nome**, **Apelido** e clique **em Criar**.

    ![Configurar um único sinal](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo De entrada única hRworks no Painel de Acesso, deve ser automaticamente inscrito no HRworks Single Sign-On para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente hRworks single sign-on com Azure AD](https://aad.portal.azure.com/)