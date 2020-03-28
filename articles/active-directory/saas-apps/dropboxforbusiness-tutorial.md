---
title: 'Tutorial: Integração de Diretório Sonícola Azure com Dropbox para Negócios [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Dropbox para o Negócios.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046751"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Tutorial: Integrar dropbox para negócios com diretório ativo Azure

Neste tutorial, você vai aprender a integrar o Dropbox para negócios com o Azure Ative Directory (Azure AD). Quando integrar o Dropbox para negócios com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Dropbox para negócios.
* Ative que os seus utilizadores sejam automaticamente inscritos no Dropbox para Negócios com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Dropbox para assinatura única (SSO) ativada pelo Dropbox para Negócios.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Dropbox para Negócios suporta **SP** iniciado SSO

* Dropbox para Negócios suporta [fornecimento e deprovisionamento automatizado](dropboxforbusiness-tutorial.md) de utilizadores
* Assim que configurar o Dropbox, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionar Dropbox para Negócios a partir da galeria

Para configurar a integração do Dropbox para o Business no Azure AD, é necessário adicionar o Dropbox para negócios da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite Dropbox para Negócios** na caixa de pesquisa.
1. Selecione **Dropbox para Negócios** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Dropbox para Negócios utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Dropbox para o Negócio.

Para configurar e testar o Azure AD SSO com o Dropbox para o Negócios, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.    
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Configure o Dropbox para Business SSO](#configure-dropbox-for-business-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Create Dropbox for Business test user](#create-dropbox-for-business-test-user)** - para ter uma contrapartida da Britta Simon no Dropbox para negócios que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **dropbox para empresas,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **identificador (Id entidade),** escreva um valor:`Dropbox`

    > [!NOTE]
    > O valor de URL de sign-on anterior não é um valor real. Atualizará o valor com o URL de sign-on real, o que é explicado mais tarde no tutorial.

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na **secção 'Dropbox' de configuração para negócios,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL


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

Nesta secção, permitirá à Britta Simon utilizar o único sign-on do Azure, concedendo acesso ao Dropbox para negócios.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Dropbox para Negócios**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-dropbox-for-business-sso"></a>Configure Dropbox para SSO De Negócios

1. Para automatizar a configuração dentro do Dropbox para o Negócio, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Dropbox for Business** irá direcioná-lo para a aplicação Dropbox para Empresa. A partir daí, forneça as credenciais de administração para assinar no Dropbox para o Negócios. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar manualmente o Dropbox para o Negócio, abra uma nova janela do navegador web e vá ao seu Dropbox para inquilino de negócios e inscreva-se no seu Dropbox para inquilino de negócios. e executar os seguintes passos:

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar o início de sessão único")

4. Clique no ícone do **utilizador** e selecione o separador **Definições.**

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure1.png "Configurar o início de sessão único")

5. No painel de navegação do lado esquerdo, clique na **consola Admin**.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure2.png "Configurar o início de sessão único")

6. Na **consola Admin,** clique em **Definições** no painel de navegação esquerdo.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure3.png "Configurar o início de sessão único")

7. Selecione uma única opção **de inscrição** na secção **Autenticação.**

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure4.png "Configurar o início de sessão único")

8. Na secção **de inscrição individual,** efetue os seguintes passos:  

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure5.png "Configurar o início de sessão único")

    a. Selecione **Necessário** como uma opção a partir do dropdown para o **sinal único**.

    b. Clique em **Adicionar URL de entrada** de registo e na caixa de texto URL do fornecedor de **identidade,** colar o valor URL de **Login** que copiou do portal Azure e, em seguida, selecionar **Done**.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure6.png "Configurar o início de sessão único")

    c. Clique no **certificado upload**e, em seguida, navegue para o seu ficheiro de **certificado codificado Base64** que descarregou do portal Azure.

    d. Clique no **link Copy** e cole o valor copiado na caixa de texto **url sign-on** do **Dropbox para domínio empresarial e urLs** no portal Azure.

    e. Clique em **Guardar**.

### <a name="create-dropbox-for-business-test-user"></a>Criar dropbox para utilizador de teste de negócios

Nesta secção, um utilizador chamado Britta Simon é criado no Dropbox para negócios. O Dropbox para Negócios suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Dropbox para o Negócios, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte o [Dropbox para a equipa](https://www.dropbox.com/business/contact) de suporte ao Cliente Empresarial

### <a name="test-sso"></a>Teste SSO

Quando selecionar o Dropbox para azulejos de negócios no Painel de Acesso, deve ser automaticamente inscrito no Dropbox for Business para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Dropbox com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-dropbox)