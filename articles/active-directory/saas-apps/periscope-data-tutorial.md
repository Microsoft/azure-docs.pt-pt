---
title: 'Tutorial: Integração do Azure Ative Directory com Dados do Periscópio Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Periscope Data.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ca0bfcb0c90142924285215c427a7071297be730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553923"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Tutorial: Integração do Diretório Ativo Azure com dados do Periscópio

Neste tutorial, aprende-se a integrar os Dados do Periscópio com o Azure Ative Directory (Azure AD).
A integração de dados do Periscope com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Dados do Periscope.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nos Dados do Periscope (Único SÚM) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com os Dados do Periscope, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por dados periscópios

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Periscópio Data suporta **SSO** iniciado SP

## <a name="adding-periscope-data-from-the-gallery"></a>Adicionar dados do Periscópio da galeria

Para configurar a integração dos Dados do Periscope no AD Azure, é necessário adicionar dados do Periscope da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar dados do Periscópio da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Dados do Periscópio**, selecione Dados do **Periscópio** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Dados do Periscópio na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com dados periscópios com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Dados do Periscope.

Para configurar e testar o único sinal de Ad AD com dados do Periscope, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure os dados do periscópio único sinal-on](#configure-periscope-data-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Periscope Data test user](#create-periscope-data-test-user)** - para ter uma contraparte de Britta Simon em Dados do Periscópio que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com dados periscópios, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **de dados do Periscope,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Domínio de dados do periscópio e informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto URL sign **on URL,** digite qualquer um dos URLs:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > O valor do sinal de URL não é real. Atualizar os valores com o sinal real no URL. Contacte a [equipa de suporte do Cliente de Dados do Periscope](mailto:support@periscopedata.com) para obter este valor e o valor do Identificador que obtém da secção **Configure Periscope Data Single Sign-On,** que é explicada mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Configurar dados periscópios Sign-On

1. Numa janela diferente do navegador web, inscreva-se em Dados do Periscope como Administrador.

2. Abra o menu de engrenagens na parte inferior esquerda e abra o menu De segurança de **faturação**  >  **Security** e execute os seguintes passos. Só os administradores têm acesso a estas definições.

    ![Informações sobre dados do periscópio Configure](./media/periscope-data-tutorial/configure01.png)

    a. Copie o **URL de metadados** da Federação de Aplicações a partir do passo #5 **Certificado de Assinatura SAML** e abra-o num browser. Isto abrirá um documento XML.

    b. Na caixa de texto **'Sign-On' única,** selecione **Azure Ative Directory**.

    c. Encontre a etiqueta **SingleSignOnService** e cole o valor **de Localização** na caixa de texto **SSO URL.**

    d. Encontre a etiqueta **SingleLogoutService** e cole o valor **de Localização** na caixa de texto **URL SLO.**

    e. Copie o valor **do Identificador** para o seu exemplo e cole-o na caixa de texto **Identifier (Entity ID)** da secção **de Configuração BÁSICA SAML** no portal Azure.

    f. Encontre a primeira etiqueta do ficheiro XML, copie o valor da **entidadeID** e cole-a na caixa de texto do **Emitente.**

    exemplo, Encontre a etiqueta **IDPSSODescriptor** com o protocolo SAML. Nessa secção, encontre o **Chavedscriptor** com **utilização=assinatura**. copiar o valor de **X509Certificar** e colá-lo na caixa de texto **do Certificado.**

    h. Sites com vários espaços podem escolher o espaço predefinido a partir da queda do **Espaço Padrão.** Este será o espaço a que os novos utilizadores serão adicionados quando iniciarem sessão nos Dados do Periscope pela primeira vez e são a provisionados através do Ative Directory Single Sign On.

    i. Por fim, clique em **Guardar** e **confirme** a alteração das definições SSO digitando **o Logout**.

    ![Informações sobre dados do periscópio Configure](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso a Dados do Periscope.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Dados do Periscópio**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dados do Periscópio**.

    ![A ligação de dados do Periscópio na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-periscope-data-test-user"></a>Criar utilizador de teste de dados do Periscópio

Para permitir que os utilizadores de Azure AD iniciem sessão nos Dados do Periscope, estes devem ser ateados em Dados do Periscope. Nos Dados do Periscópio, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão nos Dados do Periscope como Administrador.

2. Clique no ícone **Definições** na parte inferior esquerda do menu e navegue para **Permissões**.

    ![Informações sobre dados do periscópio Configure](./media/periscope-data-tutorial/configure03.png)

3. Clique no **UTILIZADOR ADD** e execute os seguintes passos:

      ![Informações sobre dados do periscópio Configure](./media/periscope-data-tutorial/configure04.png)

    a. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **Britta**.

    b. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

    c. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como ** \@ brittasimon contoso.com**.

    d. Clique **em ADD**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Dados do Periscope no Painel de Acesso, deverá ser automaticamente inscrito nos Dados do Periscópio para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

