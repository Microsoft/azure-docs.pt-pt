---
title: 'Tutorial: Integração do Diretório Ativo Azure com o GitHub [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e812e1b03637a3ecd7a45f02664c4e3547f1aef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79138982"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com gitHub

Neste tutorial, você vai aprender a integrar o GitHub com o Azure Ative Directory (Azure AD). Quando integrar o GitHub com o Azure AD, pode:

* Controle em Azure AD que tem acesso à sua GitHub Enterprise Cloud Organization.
* Gerencie o acesso à sua GitHub Enterprise Cloud Organization numa localização central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o GitHub, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização GitHub criada na [GitHub Enterprise Cloud,](https://help.github.com/articles/github-s-products/#github-enterprise)que requer o plano de faturação da [GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* GitHub suporta **SP** iniciado SSO

* GitHub suporta fornecimento [ **automatizado** de utilizadores (convites da organização)](github-provisioning-tutorial.md)
* Assim que configurar o GitHub, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>Adicionando GitHub da galeria

Para configurar a integração do GitHub em Azure AD, precisa adicionar gitHub da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **GitHub** na caixa de pesquisa.
1. Selecione **GitHub** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Configure e teste Azure AD single sign-on para GitHub

Configure e teste Azure AD SSO com GitHub utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no GitHub.

Para configurar e testar o Azure AD SSO com o GitHub, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure gitHub SSO](#configure-github-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste GitHub](#create-github-test-user)** - para ter uma contraparte de B.Simon no GitHub que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **GitHub,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

   a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Por favor, note que estes não são os valores reais. Tem de atualizar estes valores com o sinal real no URL e identificador. Aqui sugerimos que use o valor único da corda no Identificador. Vá à secção GitHub Admin para recuperar estes valores.

5. A sua aplicação GitHub espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação GitHub espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção Configurar o **GitHub,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao GitHub.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **GitHub**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-github-sso"></a>Configure GitHub SSO

1. Numa janela diferente do navegador web, inicie sessão no site da organização gitHub como administrador.

2. Navegue para **Definições** e clique em **Segurança**

    ![Definições](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Verifique a caixa de **autenticação Enable SAML,** revelando os campos de configuração de entrada única. Em seguida, utilize o valor url de início de sessão único para atualizar o URL de início de sessão único na configuração da AD Azure.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configure os seguintes campos:

    ![Definições](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. No **Sign on URL** textbox, colhe o valor URL de **Login** que copiou do portal Azure.

    b. Na caixa de texto **emitentes,** pasta **Azure AD Identifier** valor que copiou do portal Azure.

    c. Abra o certificado descarregado do portal Azure no bloco de notas, colao o conteúdo na caixa de texto **do Certificado Público.**

    d. Clique no ícone **Editar** para editar o método de **assinatura** e **digerir** o método de **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** como mostrado abaixo.
    
    e. Atualize o URL do **serviço de consumo de afirmação (URL** de resposta) a partir do URL predefinido de modo a que o URL em Github corresponda ao URL no registo da aplicação Azure.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Clique na **configuração SAML de teste** para confirmar que não há falhas ou erros de validação durante o SSO.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Clique em **Guardar**

> [!NOTE]
> O único sinal in audato no GitHub autentica a uma organização específica no GitHub e não substitui a autenticação do próprio GitHub. Portanto, se a sessão de github.com do utilizador tiver expirado, poderá ser-lhe pedido que autenticasse com o ID/palavra-passe do GitHub durante o processo de início de sessão individual.

### <a name="create-github-test-user"></a>Criar o utilizador de teste GitHub

O objetivo desta secção é criar um utilizador chamado Britta Simon no GitHub. O GitHub suporta o fornecimento automático de utilizadores, que é por padrão ativado. Pode encontrar mais detalhes [aqui](github-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no site da empresa GitHub como administrador.

2. Clique em **Pessoas**.

    ![People](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. Clique em **Convidar membro**.

    ![Convidar utilizadores](./media/github-tutorial/tutorial_github_config_github_09.png "Convidar utilizadores")

4. Na página de diálogo do **membro Convidar,** execute os seguintes passos:

    a. Na caixa de texto **por e-mail,** digite o endereço de e-mail da conta Britta Simon.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_10.png "Convidar pessoas")

    b. Clique em **Enviar Convite**.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_11.png "Convidar pessoas")

    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de se tornar ativo.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo GitHub no Painel de Acesso, deve ser automaticamente inscrito no GitHub para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente gitHub com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
