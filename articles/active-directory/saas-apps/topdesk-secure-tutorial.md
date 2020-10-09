---
title: 'Tutorial: Integração do Azure Ative Directory com a TOPdesk - Secure / Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o TOPdesk - Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: db7f2afa7993c8f8d2fe9fe30e5ecd4f2388b44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524268"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Azure Ative Directory com a TOPdesk - Secure

Neste tutorial, aprende-se a integrar o TOPdesk - Secure with Azure Ative Directory (Azure AD).
Integrar o TOPdesk - Secure with Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a TOPdesk - Secure.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TOPdesk - Secure (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a TOPdesk - Secure, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - Secure single sign-on enabled subscription

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TOPdesk - Suportes seguros **SP** iniciado SSO

## <a name="adding-topdesk---secure-from-the-gallery"></a>Adicionar TOPdesk - Seguro a partir da galeria

Para configurar a integração do TOPdesk - Secure in Azure AD, precisa de adicionar TOPdesk - Proteja da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar TOPdesk - Proteja-se da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TOPdesk - Secure**, selecione **TOPdesk - Fixe** a partir do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![TOPdesk - Garantir na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com topdesk - Secure com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em TOPdesk - Secure.

Para configurar e testar o Azure AD com topdesk - Seguro, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar o TOPdesk - Secure Single Sign-On](#configure-topdesk---secure-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie TOPdesk - Utilizador de teste seguro](#create-topdesk---secure-test-user)** - para ter uma contraparte de Britta Simon em TOPdesk - Secure que está ligada à representação AD Ad do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sinal de inscrição com TOPdesk - Secure, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **TOPdesk - Secure** application integration, selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![TOPdesk - Secure Domain e URLs informações únicas de súpr no sinal](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net`

    b. Na caixa URL do **Identificador,** preencha o URL de metadados TOPdesk que pode obter a partir da configuração TOPdesk. Deve utilizar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a TOPdesk - Equipa de suporte ao Cliente Seguro](https://www.topdesk.com/us/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na **secção Configuração TOPdesk - Secção Segura,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-topdesk---secure-single-sign-on"></a>Configurar TOPdesk - Secure Single Sign-On

1. Inscreva-se no seu **SITE TOPdesk - Secure** company como administrador.

2. No menu **TOPdesk,** clique em **Definições**.

    ![Definições](./media/topdesk-secure-tutorial/ic790598.png "Definições")

3. Clique em **Definições de Início**de Sessão .

    ![Definições de início de sessão](./media/topdesk-secure-tutorial/ic790599.png "Definições de início de sessão")

4. Expandir o menu **'Definições de início de sessão'** e, em seguida, clicar em **Geral**.

    ![Geral](./media/topdesk-secure-tutorial/ic790600.png "Geral")

5. Na secção **Secure** da secção de configuração de **login SAML,** execute os seguintes passos:

    ![Definições Técnicas](./media/topdesk-secure-tutorial/ic790855.png "Definições Técnicas")

    a. Clique **em Baixar** para descarregar o ficheiro de metadados públicos e guarde-o localmente no seu computador.

    b. Abra o ficheiro de metadados e, em seguida, localize o nó **'Serviço de Afirmação'.**

    ![Serviço de Apoio ao Consumidor de Afirmação](./media/topdesk-secure-tutorial/ic790856.png "Serviço de Apoio ao Consumidor de Afirmação")

    c. Copie o valor **AfirmaçãoConsumerService,** cole este valor na caixa de texto URL answer em **TOPdesk - Domínio Seguro e URLs.**

6. Para criar um ficheiro de certificado, execute os seguintes passos:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "Certificado")

    a. Abra o ficheiro de metadados descarregado do portal Azure.

    b. Expandir o nó **RoleDescriptor** que tem um **xsi:tipo** de **fed:ApplicationServiceType**.

    c. Copie o valor do nó **X509Certificado.**

    d. Guarde o valor **X509Certificado** copiado localmente no seu computador num ficheiro.

7. Na secção **Público,** clique **em Adicionar**.

    ![Adicionar](./media/topdesk-secure-tutorial/ic790607.png "Adicionar")

8. Na página de diálogo **do assistente de configuração SAML,** execute os seguintes passos:

    ![Assistente de Configuração SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de Configuração SAML")

    a. Para fazer o upload do seu ficheiro de metadados descarregado a partir do portal Azure, no portal **Federation Metadas,** clique em **Procurar**.

    b. Para fazer o upload do seu arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.

    c. Para **tecla privada (RSA, PKCS8, DER)**, pode carregar a sua própria chave privada ou pode contactar [a equipa de suporte do CLIENTE SEGURO TOPdesk - Secure Client](https://www.topdesk.com/us/support) para obter a chave privada.

    d. Para fazer o upload do ficheiro de logotipo que obteve da equipa de suporte TOPdesk, no **ícone do logotipo,** clique em **Procurar**.

    e. Na caixa de texto do **nome do utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    f. Na caixa de texto **do nome do Visor,** escreva um nome para a sua configuração.

    exemplo, Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize a Azure um único sinal de acesso, permitindo o acesso ao TOPdesk - Secure.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **TOPdesk - Secure**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **TOPdesk - Secure**.

    ![O TOPdesk - Link seguro na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-topdesk---secure-test-user"></a>Criar TOPdesk - Utilizador de teste seguro

Para permitir que os utilizadores de Azure AD entrem no TOPdesk - Secure, devem ser a provisionados em TOPdesk - Secure.  
No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento do utilizador, execute os seguintes passos:

1. Inscreva-se no seu **TOPdesk - Proteja** o site da empresa como administrador.

2. No menu em cima, clique no **TOPdesk \> New Support Files \> \> Operator**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "Operador")

3. No diálogo do **Novo Operador,** execute os seguintes passos:

    ![Novo Operador](./media/topdesk-secure-tutorial/ic790611.png "Novo Operador")

    a. Clique no separador **Geral**.

    b. Na caixa de texto **sobrenome,** escreva sobrenome do utilizador como **Simon**.

    c. Selecione um **Site** para a conta na secção **Localização.**

    d. Na caixa de texto 'Nome de Início de **Sessão'** da secção **TOPdesk,** digite um nome de login para o seu utilizador.

    e. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador seguras ou APIs fornecidas pela TOPdesk - Secure para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no TOPdesk - Secure tile no Painel de Acesso, deverá ser automaticamente inscrito no TOPdesk - Seguro para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

