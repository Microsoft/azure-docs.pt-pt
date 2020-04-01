---
title: 'Tutorial: Integração de Diretório Ativo Azure com Ficheiros Em Qualquer Lugar [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e os Ficheiros Em Qualquer Lugar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26311a6c9a1b751243c0571ce9a3417af891959
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158209"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Tutorial: Integração de Diretório Ativo Azure com Ficheiros Em Qualquer Lugar

Neste tutorial, aprende-se a integrar ficheiros em qualquer lugar com o Azure Ative Directory (Azure AD).
Integrar ficheiros Em qualquer lugar com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Ficheiros Em Qualquer lugar.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no FilesAnywhere (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o FilesAnywhere, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Ficheiros Qualquer subscrição ativada por sinal único em qualquer lugar

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* FilesAnywhere suporta **SP** e **IDP** iniciadoS SSO

* FilesAnywhere suporta aprovisionamento de utilizadores **justo no tempo**

## <a name="adding-filesanywhere-from-the-gallery"></a>Adicionar Ficheiros Em qualquer lugar da galeria

Para configurar a integração dos Ficheiros Em Qualquer Lugar no AD Azure, é necessário adicionar Ficheiros Em qualquer lugar da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Ficheiros Em qualquer lugar da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Ficheiros Em qualquer lugar**, selecione **Ficheiros Em qualquer lugar** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Ficheiros em qualquer lugar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com filesAnywhere com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em FilesAnywhere.

Para configurar e testar o único sinal de Azure AD com filesAnywhere, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure ficheiros em qualquer lugar um sinal único](#configure-filesanywhere-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create FilesAnywhere test user](#create-filesanywhere-test-user)** - para ter uma contrapartida de Britta Simon em FilesAnywhere que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com filesAnywhere, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **FilesAnywhere,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    ![FilesAnywhere Domain e URLs informações únicas de inscrição](common/both-replyurl.png)

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<company name>.filesanywhere.com/saml20.aspx?c=<Client Id>`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![FilesAnywhere Domain e URLs informações únicas de inscrição](common/both-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<sub domain>.filesanywhere.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal. Contacte a equipa de [suporte do Cliente FilesAnywhere](mailto:support@FilesAnywhere.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. A aplicação FilesAnywhere espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone Editar para adicionar os atributos.

    ![image](common/edit-attribute.png)

    Quando os utilizadores se inscrevem no FilesAnywhere obtêm o valor do atributo do **cliente da** [equipa FilesAnywhere](mailto:support@FilesAnywhere.com). Tem de adicionar o atributo "Id do Cliente" com o valor único fornecido pelos Ficheiros Em Qualquer lugar.

7. Além de acima, a aplicação FilesAnywhere espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:

    | Nome | Atributo fonte|
    | ---------------| --------------- |    
    | clientid | *"valor único"* |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

9. Na secção **Configurar Ficheiros Em Qualquer lugar,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-filesanywhere-single-sign-on"></a>Configure ficheiros em qualquer lugar um único sign-on

Para configurar um único sinal no lado do **FilesAnywhere,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte FilesAnywhere](mailto:support@FilesAnywhere.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso a FilesAnywhere.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Ficheiros Em qualquer lugar**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **FilesAnywhere**.

    ![O link FilesAnywhere na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-filesanywhere-test-user"></a>Criar ficheiros Qualquer um utilizador de teste

Nesta secção, um utilizador chamado Britta Simon é criado em FilesAnywhere. O FilesAnywhere suporta o fornecimento de utilizadores just-in-time, que é ativado por predefinição. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em FilesAnywhere, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo FilesAnywhere no Painel de Acesso, deve ser automaticamente inscrito nos Ficheiros Onde configura r'se de configurar o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

