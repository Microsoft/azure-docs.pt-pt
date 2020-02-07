---
title: 'Tutorial: integração do Azure Active Directory com o Dropbox for Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7fc366c5087e66c3022c212870397d77e6e34d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046751"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Tutorial: integrar o Dropbox for Business com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Dropbox for Business com o Azure Active Directory (Azure AD). Ao integrar o Dropbox for Business ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao dropbox for Business.
* Habilite seus usuários a serem conectados automaticamente ao dropbox for Business com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único (SSO) do Dropbox for Business.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. Dropbox para Negócios suporta **SP** iniciado SSO

* Dropbox para Negócios suporta [fornecimento e deprovisionamento automatizado](dropboxforbusiness-tutorial.md) de utilizadores
* Assim que configurar o Dropbox, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionando o Dropbox for Business por meio da Galeria

Para configurar a integração do Dropbox for Business ao Azure AD, você precisará adicionar o Dropbox for Business por meio da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite Dropbox para Negócios** na caixa de pesquisa.
1. Selecione **Dropbox para Negócios** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste Azure AD SSO com Dropbox para Negócios utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Dropbox for Business.

Para configurar e testar o SSO do Azure AD com o Dropbox for Business, conclua os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.    
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Configure o Dropbox para Business SSO](#configure-dropbox-for-business-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Create Dropbox for Business test user](#create-dropbox-for-business-test-user)** - para ter uma contrapartida da Britta Simon no Dropbox para negócios que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **dropbox para empresas,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **identificador (Id** da entidade), digite um valor: `Dropbox`

    > [!NOTE]
    > O valor da URL de logon anterior não é um valor real. Você atualizará o valor com a URL de logon real, que é explicada posteriormente no tutorial.

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na **secção 'Dropbox' de configuração para negócios,** copie os URL(s) adequados de acordo com o seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado Brenda Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao dropbox for Business.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Dropbox para Negócios**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-dropbox-for-business-sso"></a>Configurar o SSO do Dropbox for Business

1. Para automatizar a configuração dentro do Dropbox para o Negócio, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Dropbox for Business** irá direcioná-lo para a aplicação Dropbox para Empresa. A partir daí, forneça as credenciais de administrador para entrar no Dropbox for Business. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Dropbox for Business manualmente, abra uma nova janela do navegador da Web e vá para seu locatário do Dropbox for Business e faça logon em seu locatário do Dropbox for Business. e execute as seguintes etapas:

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar o início de sessão único") (Configurar o início de sessão único)

4. Clique no ícone do **utilizador** e selecione o separador **Definições.**

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure1.png "Configurar o início de sessão único") (Configurar o início de sessão único)

5. No painel de navegação do lado esquerdo, clique na **consola Admin**.

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure2.png "Configurar o início de sessão único") (Configurar o início de sessão único)

6. Na **consola Admin,** clique em **Definições** no painel de navegação esquerdo.

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure3.png "Configurar o início de sessão único") (Configurar o início de sessão único)

7. Selecione uma única opção **de inscrição** na secção **Autenticação.**

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure4.png "Configurar o início de sessão único") (Configurar o início de sessão único)

8. Na secção **de inscrição individual,** efetue os seguintes passos:  

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure5.png "Configurar o início de sessão único") (Configurar o início de sessão único)

    a. Selecione **Necessário** como uma opção a partir do dropdown para o **sinal único**.

    b. Clique em **Adicionar URL de entrada** de registo e na caixa de texto URL do fornecedor de **identidade,** colar o valor URL de **Login** que copiou do portal Azure e, em seguida, selecionar **Done**.

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure6.png "Configurar o início de sessão único") (Configurar o início de sessão único)

    c. Clique no **certificado upload**e, em seguida, navegue para o seu ficheiro de **certificado codificado Base64** que descarregou do portal Azure.

    d. Clique no **link Copy** e cole o valor copiado na caixa de texto **url sign-on** do **Dropbox para domínio empresarial e urLs** no portal Azure.

    e. Clique em **Guardar**.

### <a name="create-dropbox-for-business-test-user"></a>Criar usuário de teste do Dropbox for Business

Nesta seção, um usuário chamado Brenda Simon é criado no Dropbox for Business. O Dropbox for Business dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Dropbox for Business, um novo será criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte o [Dropbox para a equipa](https://www.dropbox.com/business/contact) de suporte ao Cliente Empresarial

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Dropbox for Business no painel de acesso, você deverá entrar automaticamente no Dropbox for Business para o qual você configurou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Dropbox com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-dropbox)