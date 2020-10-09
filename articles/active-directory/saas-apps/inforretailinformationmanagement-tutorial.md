---
title: 'Tutorial: Integração do Azure Ative Directory com o Infor Retail – Gestão de Informação ; Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Infor Retail – Information Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 1a98bec006a1bbb755a0c310b8e0409302c45198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819185"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Tutorial: Integração do Azure Ative Directory com o Infor Retail – Information Management

Neste tutorial, aprende-se a integrar o Infor Retail – Information Management com o Azure Ative Directory (Azure AD).
Integrar o Infor Retail – Gestão de Informação com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao Infor Retail – Information Management.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Infor Retail – Gestão de Informação (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a Infor Retail – Information Management, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Infor Retail – Gestão de Informação única de assinatura ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Infor Retail – Gestão de Informação apoia **SP e IDP** iniciado SSO

## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Adicionar Infor Retail – Gestão de Informação da galeria

Para configurar a integração do Infor Retail – Gestão de Informação em AD Azure, é necessário adicionar Infor Retail – Information Management da galeria à sua lista de aplicações geridas pela SaaS.

**Para adicionar Infor Retail – Gestão de Informação da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Infor Retail – Information Management**, selecione **Infor Retail – Information Management** from result panel e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Infor Retail – Gestão de Informação na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com a Infor Retail – Information Management com base num utilizador de teste chamado **Britta Simon.**
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Infor Retail – Gestão de Informação.

Para configurar e testar o Azure AD com um único sign-on com o Infor Retail – Information Management, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar Infor Retail – Gestão de Informação Único Sinal-On](#configure-infor-retail--information-management-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Infor Retail – Utilizador de teste de Gestão](#create-infor-retail--information-management-test-user)** de Informação – para ter uma contrapartida da Britta Simon no Infor Retail – Information Management que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sign-on com a Infor Retail – Information Management, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **infor retail – Information Management** integração de aplicações, selecione Single **sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://<company name>.mingle.infor.com
    http://<company name>.mingledev.infor.com
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<company name>.mingle.infor.com/sp/ACS.saml2`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<company name>.mingle.infor.com/<company code>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a Equipa de Apoio ao Cliente da Infor Retail – Gestão de Informação](mailto:innovate@infor.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configuração Infor Retail – Information Management,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-infor-retail--information-management-single-sign-on"></a>Configure Infor Retail – Information Management Single Sign-On

Para configurar um único sign-on **inFor Retail – Information Management** side, é necessário enviar os **Metadadata XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte infor retail – Information Management.](mailto:innovate@infor.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, concedendo acesso ao Infor Retail – Information Management.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Infor Retail – Information Management**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Infor Retail – Information Management**.

    ![O link Infor Retail – Information Management na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-infor-retail--information-management-test-user"></a>Criar Infor Retail – Utilizador de teste de Gestão de Informação

Nesta secção, cria-se um utilizador chamado Britta Simon in Infor Retail – Information Management. Trabalhar com a Equipa de Apoio à [Gestão de Informação infor](mailto:innovate@infor.com) para adicionar os utilizadores na plataforma Infor Retail – Information Management. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Infor Retail – Information Management no Painel de Acesso, deverá ser automaticamente inscrito no Infor Retail – Information Management para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
