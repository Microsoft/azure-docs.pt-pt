---
title: 'Tutorial: Integração do Azure Ative Directory com a AirWatch Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 2baff2ac2af10caf857fd08b0d7e7df5030220e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758154"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrar AirWatch com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar AirWatch com Azure Ative Directory (Azure AD). Quando integrar o AirWatch com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao AirWatch.
* Ativar os seus utilizadores a serem automaticamente inscritos no AirWatch com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição única ativada pelo AirWatch (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. AirWatch suporta SSO iniciado **SP.**

## <a name="adding-airwatch-from-the-gallery"></a>Adicionando AirWatch da galeria

Para configurar a integração do AirWatch no Azure AD, é necessário adicionar AirWatch da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **AirWatch** na caixa de pesquisa.
1. Selecione **AirWatch** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com AirWatch usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no AirWatch.

Para configurar e testar o Azure AD SSO com a AirWatch, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o AirWatch SSO](#configure-airwatch-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Create AirWatch test user](#create-airwatch-test-user)** - para ter uma contraparte de Britta Simon no AirWatch que está ligada à representação AD AD do utilizador.
5. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **AirWatch,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na caixa de texto **identifier (Entity ID),** digite o valor como: `AirWatch`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de inscrição real. Contacte a [equipa de suporte do Cliente AirWatch](https://www.air-watch.com/company/contact-us/) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação AirWatch espera as afirmações SAML num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

1. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Nome |  Atributo de origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o Metadados XML e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar AirWatch,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configure AirWatch SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa AirWatch como administrador.

1. Na página de definições. Selecione **Configurações > Serviços de Diretório de Integração empresarial >**.

   ![Definições](./media/airwatch-tutorial/ic791921.png "Definições")

1. Clique no **separador Utilizador,** na caixa de texto **Base DN,** digite o nome de domínio e, em seguida, clique em **Guardar**.

   ![Screenshot que realça a caixa de texto Base DN.](./media/airwatch-tutorial/ic791922.png "Utilizador")

1. Clique no **separador 'Servidor'.**

   ![Servidor](./media/airwatch-tutorial/ic791923.png "Servidor")

1. Execute os seguintes passos na secção **LDAP:**

    ![Screenshot que mostra as alterações que precisa de fazer na secção LDAP.](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Como **Tipo de Diretório**, selecione **Nenhum**.

    b. Selecione **use SAML para autenticação**.

1. Na secção **SAML 2.0,** para fazer o upload do certificado descarregado, clique em **Upload**.

    ![Carregar](./media/airwatch-tutorial/ic791932.png "Carregar")

1. Na secção **Pedido,** execute os seguintes passos:

    ![Pedir](./media/airwatch-tutorial/ic791925.png "Pedir")  

    a. Como **Tipo de Ligação de Pedido**, selecione **POST**.

    b. No portal Azure, no sinal único de configuração na página de diálogo **AirWatch,** copie o valor URL de **login** e, em seguida, cole-o na caixa de texto **de URL do Fornecedor de Identidade.**

    c. Como **Formato NameID**, selecione **endereço de e-mail**.

    d. Como **Autenticação Pedido De Segurança**, selecione **Nenhum**.

    e. Clique em **Guardar**.

1. Clique novamente no **separador Utilizador.**

    ![Utilizador](./media/airwatch-tutorial/ic791926.png "Utilizador")

1. Na secção **Atributo,** execute os seguintes passos:

    ![Atributo](./media/airwatch-tutorial/ic791927.png "Atributo")

    a. Na caixa de texto do **identificador de objetos,** escreva `http://schemas.microsoft.com/identity/claims/objectidentifier` .

    b. Na caixa de texto do **nome de utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    c. Na caixa de texto **'Mostrar' 'Mostrar',** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    d. Na caixa de texto **name First,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. Na caixa de texto **do último nome,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. Na caixa de texto **por e-mail,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    exemplo, Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao AirWatch.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AirWatch**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-airwatch-test-user"></a>Criar utilizador de teste AirWatch

Para permitir que os utilizadores Azure AD entrem no AirWatch, devem ser adustados ao AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **AirWatch** como administrador.

2. No painel de navegação do lado esquerdo, clique em **Contas**e, em seguida, clique em **Utilizadores**.
  
   ![Utilizadores](./media/airwatch-tutorial/ic791929.png "Utilizadores")

3. No menu **Utilizadores,** clique em **'Ver lista'** e, em seguida, clique **em Adicionar > Adicionar Utilizador**.
  
   ![Screenshot que realça os botões Adicionar e Adicionar Utilizador.](./media/airwatch-tutorial/ic791930.png "Adicionar Utilizador")

4. No diálogo **do utilizador Add / Editar,** execute os seguintes passos:

   ![Adicionar utilizador](./media/airwatch-tutorial/ic791931.png "Adicionar Utilizador")

   a. Digite o **nome de utilizador**, **Password**, **Confirme a Palavra-passe,** **o Primeiro Nome,** o Último **Nome,** o Endereço de **E-mail** de uma conta de Diretório Ativo Azure válida que pretende apresentar nas caixas de texto relacionadas.

   b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador airWatch ou APIs fornecidas pela AirWatch para fornecer contas de utilizadores Azure AD.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo AirWatch no Painel de Acesso, deverá ser automaticamente inscrito no AirWatch para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
