---
title: 'Tutorial: Integração do Azure Ative Directory com a SAP Business ByDesign / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 91c0eaf5b1e614e185747cd9ae2c32e7f4f7a707
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672062"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração do Azure Active Directory com SAP Business ByDesign

Neste tutorial, aprende-se a integrar o SAP Business ByDesign com o Azure Ative Directory (Azure AD).
A integração da SAP Business ByDesign com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SAP Business ByDesign.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SAP Business ByDesign (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o SAP Business ByDesign, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por SAP Business ByDesign

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAP Business ByDesign apoia **SSO** iniciado sp

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adicionando SAP Business ByDesign da galeria

Para configurar a integração do SAP Business ByDesign no AD Azure, é necessário adicionar o SAP Business ByDesign da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o SAP Business ByDesign da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva O BYDesign DO NEGÓCIO SAP** , selecione **SAP Business ByDesign** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

    ![SAP Business ByDesign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD um único sign-on com SAP Business ByDesign com base em um utilizador de teste chamado **Britta Simon** .
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SAP Business ByDesign.

Para configurar e testar o Azure AD com um único signo com o SAP Business ByDesign, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SAP Business ByDesign Single Sign-On](#configure-sap-business-bydesign-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SAP Business ByDesign test user](#create-sap-business-bydesign-test-user)** - para ter uma contraparte de Britta Simon em SAP Business ByDesign que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com o SAP Business ByDesign, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **SAP Business ByDesign,** selecione **Single sign-on** .

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![SAP Business ByDesign Domain e URLs informações únicas de súpr-on](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<servername>.sapbydesign.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de suporte do CLIENTE DA SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação SAP Business ByDesign espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

6. Clique no ícone **Editar** para editar o valor do **identificador nome** .

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na secção **Gerir reclamações do utilizador,** execute os seguintes passos:  ![ imagem](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecione **a Transformação** como **Fonte** .

    b. Na lista de abandonos de **transformação,** selecione **ExtractMailPrefix()** .

    c. Na lista de retirada do **parâmetro 1,** selecione o atributo do utilizador que pretende utilizar para a sua implementação. Por exemplo, se pretender utilizar o EmployeeID como identificador único do utilizador e tiver armazenado o valor do atributo na ExtensionAttribute2, então selecione user.extensionattribute2.

    d. Clique em **Guardar** .

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

9. Na secção **set up SAP Business ByDesign,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Configurar o NEGÓCIO SAP ByDesign Single Sign-On

1. Inscreva-se no seu portal SAP Business ByDesign com direitos de administrador.

2. Navegue para a **Tarefa Comum de Aplicação e Gestão do Utilizador** e clique no **separador Fornecedor de Identidade.**

3. Clique em **Novo Fornecedor de Identidade** e selecione o ficheiro XML de metadados que descarregou a partir do portal Azure. Ao importar os metadados, o sistema envia automaticamente o certificado de assinatura e certificado de encriptação necessários.

    ![Configurar Sign-On Individuais](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir o **URL do Serviço ao Consumidor de Afirmação** no pedido SAML, selecione Incluir URL de Serviço ao Consumidor de **Afirmação** .

5. Clique **em Ativar um único sinal de inscrição** .

6. Guarde as alterações.

7. Clique no **separador 'O Meu Sistema'.**

    ![Configurar Sign-On Individuais](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. No **sinal AD Azure Na** caixa de texto URL, cole o valor URL do **Login,** que copiou a partir do portal Azure.

    ![Configurar Sign-On Individuais](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especificar se o empregado pode escolher manualmente entre iniciar sessão com iD do utilizador e senha ou SSO selecionando a **Seleção manual do Fornecedor de Identidade** .

10. Na secção **URL SSO,** especifique o URL que deve ser usado pelo empregado para assinar no sistema.
    Na lista de despedimentos do URL enviada para o número de colaboradores, pode escolher entre as seguintes opções:

    **URL não-SSO**

    O sistema envia apenas a URL normal do sistema para o empregado. O empregado não pode assinar usando SSO, e deve usar senha ou certificado em vez disso.

    **SSO URL**

    O sistema envia apenas o URL SSO para o empregado. O empregado pode inscrever-se usando SSO. O pedido de autenticação é redirecionado através do IdP.

    **Seleção Automática**

    Se o SSO não estiver ativo, o sistema envia o URL normal do sistema ao empregado. Se o SSO estiver ativo, o sistema verifica se o empregado tem uma palavra-passe. Se estiver disponível uma palavra-passe, tanto o URL SSO como o URL não-SSO são enviados ao empregado. No entanto, se o empregado não tiver senha, apenas o URL SSO é enviado ao empregado.

11. Guarde as alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon** .
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SAP Business ByDesign.

1. No portal Azure, selecione **Aplicações empresariais** , selecione **Todas as aplicações,** em seguida, selecione **SAP Business ByDesign** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **SAP Business ByDesign** .

    ![O link SAP Business ByDesign na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos** .

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-sap-business-bydesign-test-user"></a>Criar utilizador de teste SAP Business ByDesign

Nesta secção, cria-se um utilizador chamado Britta Simon no SAP Business ByDesign. Por favor, trabalhe com a equipa de [suporte do CLIENTE SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os utilizadores na plataforma SAP Business ByDesign. 

> [!NOTE]
> Certifique-se de que o valor NameID deve coincidir com o nome de utilizador na plataforma SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SAP Business ByDesign no Painel de Acesso, deverá ser automaticamente inscrito no CONTRATO SAP Business ByDesign para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)