---
title: 'Tutorial: Integração de Diretório Ativo Azure com TOPdesk - Secure / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TOPdesk - Secure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 393557531fe69a494a16e0f4c49ac07891e490ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74233406"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração de Diretório Ativo Azure com TOPdesk - Seguro

Neste tutorial, aprende-se a integrar o TOPdesk - Secure with Azure Ative Directory (Azure AD).
Integrando toPdesk - Secure with Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a TOPdesk - Secure.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TOPdesk - Secure (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com toPdesk - Secure, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk - Garantir uma subscrição ativada por um único sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* TOPdesk - Suportes seguros **SP** iniciado SSO

## <a name="adding-topdesk---secure-from-the-gallery"></a>Adicionar TOPdesk - Seguro da galeria

Para configurar a integração do TOPdesk - Secure in Azure AD, você precisa adicionar TOPdesk - Secure da galeria para a sua lista de aplicações saaS geridas.

**Para adicionar TOPdesk - Proteja a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TOPdesk - Secure**, selecione **TOPdesk - Fixe** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![TOPdesk - Seguro na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com TOPdesk - Secure baseado num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no TOPdesk - Secure.

Para configurar e testar o único sinal de Azure AD com TOPdesk - Secure, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure TOPdesk - Secure Single Sign-On](#configure-topdesk---secure-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Criar toPdesk - utilizador](#create-topdesk---secure-test-user)** de teste seguro - para ter uma contrapartida de Britta Simon no TOPdesk - Secure que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com toPdesk - Secure, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **toPdesk - Secure** application integration page, selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![TOPdesk - Secure Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.topdesk.net`

    b. Na caixa **URL do Identificador,** preencha o URL de metadados TOPdesk que pode recuperar da configuração TOPdesk. Deve utilizar o seguinte padrão:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte [a TOPdesk - Equipa](https://www.topdesk.com/us/support/) de suporte ao Cliente Seguro para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na **secção TOPdesk - Secure,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-topdesk---secure-single-sign-on"></a>Configure TOPdesk - Secure Single Sign-On

1. Inscreva-se no seu **TOPdesk - Secure** site da empresa como administrador.

2. No menu **TOPdesk,** clique em **Definições**.

    ![Definições](./media/topdesk-secure-tutorial/ic790598.png "Definições")

3. Clique **em Definições de Login**.

    ![Definições de login](./media/topdesk-secure-tutorial/ic790599.png "Definições de login")

4. Expanda o menu definições de **login** e, em seguida, clique em **General**.

    ![General](./media/topdesk-secure-tutorial/ic790600.png "Geral")

5. Na secção **'Secure'** da secção de configuração de **login SAML,** execute os seguintes passos:

    ![Definições Técnicas](./media/topdesk-secure-tutorial/ic790855.png "Definições Técnicas")

    a. Clique em **Baixar** para descarregar o ficheiro de metadados públicos e, em seguida, guarde-o localmente no seu computador.

    b. Abra o ficheiro de metadados e, em seguida, localize o nó **Do Serviço de CréditoConsumerService.**

    ![Serviço de Consumidores de Afirmação](./media/topdesk-secure-tutorial/ic790856.png "Serviço de Consumidores de Afirmação")

    c. Copie o valor **Do Serviço de CréditoconsumerService,** cole este valor na caixa de texto URL de resposta na **toPdesk - Domínio seguro e urLs.**

6. Para criar um ficheiro de certificado, execute os seguintes passos:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "Certificado")

    a. Abra o ficheiro de metadados descarregado do portal Azure.

    b. Expanda o nó **RoleDescriptor** que tem um **xsi:tipo** de **fed:ApplicationServiceType**.

    c. Copie o valor do nó **X509Certificate.**

    d. Guarde o valor **copiado x509Certificado** localmente no seu computador num ficheiro.

7. Na secção **Pública,** clique em **Adicionar**.

    ![Adicionar](./media/topdesk-secure-tutorial/ic790607.png "Adicionar")

8. Na página de diálogo do assistente de **configuração SAML,** execute os seguintes passos:

    ![Assistente de Configuração SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de Configuração SAML")

    a. Para fazer o upload do ficheiro de metadados descarregado sabotado do portal Azure, sob **os Metadados da Federação,** clique em **Navegar**.

    b. Para fazer upload do seu ficheiro de certificado, em **Certificado (RSA)**, clique em **Navegar**.

    c. Para **teclas privadas (RSA, PKCS8, DER)** pode carregar a sua própria chave privada ou pode contactar a EQUIPA de suporte do [TOPdesk - Secure Client](https://www.topdesk.com/us/support) para obter a chave privada.

    d. Para fazer upload do ficheiro de logotipo que obteve da equipa de suporte toPdesk, sob **o ícone logotipo,** clique em **Navegar**.

    e. No **nome utilizador atributo** caixa `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`de texto, escreva .

    f. Na caixa de texto do **nome Display,** escreva um nome para a sua configuração.

    g. Clique em **Guardar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao TOPdesk - Secure.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **TOPdesk - Secure**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **TOPdesk - Secure**.

    ![O TOPdesk - Ligação segura na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-topdesk---secure-test-user"></a>Criar TOPdesk - Utilizador de teste seguro

Para permitir que os utilizadores de Anúncios Azure entrem em TOPdesk - Secure, devem ser aprovisionados em TOPdesk - Secure.  
No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o fornecimento do utilizador, execute os seguintes passos:

1. Inscreva-se no seu **TOPdesk - Secure** site da empresa como administrador.

2. No menu em cima, clique em **TOPdesk \> New \> Support Files \> Operator**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "Operador")

3. No diálogo **New Operator,** efetue os seguintes passos:

    ![Novo Operador](./media/topdesk-secure-tutorial/ic790611.png "Novo Operador")

    a. Clique no separador **Geral.**

    b. Na caixa de texto **sobrenome,** escreva sobrenome do utilizador como **Simon**.

    c. Selecione um **Site** para a conta na secção **Localização.**

    d. Na caixa de texto **sino** da secção **TOPdesk Login,** digite um nome de login para o utilizador.

    e. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar qualquer outra TOPdesk - Ferramentas de criação de conta de utilizador seguras ou APIs fornecidas pela TOPdesk - Secure para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no TOPdesk - Secure tile in the Access Panel, deve ser automaticamente inscrito no TOPdesk - Secure para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

