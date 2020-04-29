---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com CakeHR [ Integração de assinaturaúnica de Diretório Ativo Azure) com CakeHR [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o CakeHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c9bbdb1-ac47-4fb8-a1cc-1e647b0323a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0860411c95e48a16d75df4aeeedf3405a5b1835
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72595028"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com CakeHR

Neste tutorial, você vai aprender a integrar cakeHR com O Diretório Ativo Azure (Azure AD). Quando integrar o CakeHR com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a CakeHR.
* Permita que os seus utilizadores sejam automaticamente inscritos na CakeHR com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de assinatura de assinatura única cakeHR (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* CakeHR apoia **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-cakehr-from-the-gallery"></a>Adicionando CakeHR da galeria

Para configurar a integração da CakeHR em Azure AD, você precisa adicionar CakeHR da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **CakeHR** na caixa de pesquisa.
1. Selecione **CakeHR** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Configure e teste Azure AD único sign-on para CakeHR

Configure e teste Azure AD SSO com CakeHR utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no CakeHR.

Para configurar e testar o Azure AD SSO com cakeHR, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure CakeHR SSO](#configure-cakehr-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador](#create-cakehr-test-user)** do teste CakeHR - para ter uma contrapartida de B.Simon em CakeHR que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **CakeHR,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<yourcakedomain>.cake.hr/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Inscrição e Resposta real. Contacte a equipa de [apoio ao Cliente CakeHR](mailto:info@cake.hr) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção Certificado de **Assinatura SAML,** copie o valor **THUMBPRINT** e guarde-o no seu Bloco de Notas.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **"Configurar o CakeHR",** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso à CakeHR.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **CakeHR**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-cakehr-sso"></a>Configure CakeHR SSO

1. Para automatizar a configuração dentro do CakeHR, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar CakeHR** irá direcioná-lo para a aplicação CakeHR. A partir daí, forneça as credenciais de administração para assinar no CakeHR. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se quiser configurar o CakeHR manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa CakeHR como administrador e execute os seguintes passos:

1. No canto superior direito da página, clique no **Perfil** e, em seguida, navegue para **Definições**.

    ![Configuração CakeHR](./media/cakehr-tutorial/config01.png)

1. Do lado esquerdo da barra de menus, clique em **INTEGRAÇÕES** > **SAML SSO** e execute os seguintes passos:

    ![Configuração CakeHR](./media/cakehr-tutorial/config02.png)

    a. Na caixa de texto `cake.hr` **id entidade,** escreva .

    b. Na caixa de texto URL de **Autenticação,** cola o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **Key print (formato SHA1),** colá o valor **THUMBPRINT,** que copiou do portal Azure.

    d. Verifique o **sinal único ativar na** caixa.

    e. Clique em **Guardar**.

### <a name="create-cakehr-test-user"></a>Criar o utilizador de teste CakeHR

Para permitir que os utilizadores da AD Azure inscrevam-se na CakeHR, devem ser aprovisionados no CakeHR. No CakeHR, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na CakeHR como administrador de segurança.

2. Do lado esquerdo da barra de menus, clique em**ADD** **COMPANY** > .

    ![Configuração CakeHR](./media/cakehr-tutorial/config03.png)

3. No **pop-up add novo empregado,** execute os seguintes passos:

     ![Configuração CakeHR](./media/cakehr-tutorial/config04.png)

    a. Na caixa de texto **de nome completo,** introduza o nome de utilizador como B.Simon.

    b. No **Work email** box, insira `B.Simon@contoso.com`o e-mail do utilizador como .

    c. Clique em **CRIAR CONTA**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo CakeHR no Painel de Acesso, deve ser automaticamente inscrito no CakeHR para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente CakeHR com Azure AD](https://aad.portal.azure.com/)
