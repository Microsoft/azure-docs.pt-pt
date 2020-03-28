---
title: 'Tutorial: Integração do Diretório Ativo Azure com a AirWatch Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231997"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrar o AirWatch com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar o AirWatch com o Azure Ative Directory (Azure AD). Quando integrar o AirWatch com o Azure AD, pode:

* Controle em Azure AD que tem acesso ao AirWatch.
* Ative que os seus utilizadores sejam automaticamente inscritos no AirWatch com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A subscrição ativada por um único sinal (SSO) do AirWatch( SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. AirWatch suporta **SP** iniciado SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Adicionando AirWatch da galeria

Para configurar a integração do AirWatch em Azure AD, precisa de adicionar airWatch da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **AirWatch** na caixa de pesquisa.
1. Selecione **O AirWatch** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com AirWatch utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no AirWatch.

Para configurar e testar o Azure AD SSO com o AirWatch, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o AirWatch SSO](#configure-airwatch-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. **[Crie o utilizador](#create-airwatch-test-user)** de teste AirWatch - para ter uma contrapartida de Britta Simon no AirWatch que esteja ligada à representação do utilizador da AD Azure.
5. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do AirWatch,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    1. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na caixa de texto **identificador (Id entidade),** digite o valor como:`AirWatch`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de início de sinal real. Contacte a equipa de suporte ao [Cliente Da AirWatch](https://www.air-watch.com/company/contact-us/) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação AirWatch espera as afirmações do SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na configuração do Open Sign-On com a página **SAML,** clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

1. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, edite as reclamações utilizando o **ícone Editar** ou adicione as reclamações utilizando a adição de **nova alegação** para configurar o atributo token SAML, como mostrado na imagem acima e executar os seguintes passos:

    | Nome |  Atributo fonte|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Download** para descarregar os Metadados XML e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **AirWatch,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configure AirWatch SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa AirWatch como administrador.

1. Na página de definições. Selecione **Definições > Serviços**de Diretório de Integração Empresarial > .

   ![Definições](./media/airwatch-tutorial/ic791921.png "Definições")

1. Clique no separador **Utilizador,** na caixa de texto **DN base,** digite o seu nome de domínio e, em seguida, clique em **Guardar**.

   ![Utilizador](./media/airwatch-tutorial/ic791922.png "Utilizador")

1. Clique no separador **Servidor.**

   ![Servidor](./media/airwatch-tutorial/ic791923.png "Server")

1. Efetue os seguintes passos na secção **LDAP:**

    ![Carregar](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Como **Tipo de Diretório,** selecione **Nenhum**.

    b. Selecione **Use O SAML para autenticação**.

1. Na secção **SAML 2.0,** para fazer o upload do certificado descarregado, clique em **Upload**.

    ![Carregar](./media/airwatch-tutorial/ic791932.png "Carregar")

1. Na secção **Pedido,** execute os seguintes passos:

    ![Pedido](./media/airwatch-tutorial/ic791925.png "Pedir")  

    a. Como **Pedido Tipo de Encadernação,** selecione **POST**.

    b. No portal Azure, na página de diálogo de **configuração única da AirWatch,** copie o valor URL de **Login** e, em seguida, cole-o no single sign do Fornecedor de Identidade na caixa de texto **URL.**

    c. Como **formato NameID,** selecione **Endereço de e-mail**.

    d. Como **Segurança de Pedido de Autenticação,** selecione **Nenhum**.

    e. Clique em **Guardar**.

1. Volte a clicar no separador **Utilizador.**

    ![Utilizador](./media/airwatch-tutorial/ic791926.png "Utilizador")

1. Na secção **Atributo,** execute os seguintes passos:

    ![Atributo](./media/airwatch-tutorial/ic791927.png "Atributo")

    a. Na caixa de texto do `http://schemas.microsoft.com/identity/claims/objectidentifier`identificador de **objetos,** escreva .

    b. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **username,** escreva .

    c. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` **'Nome de exibição',** escreva .

    d. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`De Primeiro **Nome,** escreva .

    e. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` **De Apelido,** escreva .

    f. Na caixa de texto `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **e-mail,** escreva .

    g. Clique em **Guardar**.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao AirWatch.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AirWatch**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-airwatch-test-user"></a>Criar o utilizador de teste AirWatch

Para permitir que os utilizadores de Anúncios Azure assinem no AirWatch, devem ser aprovisionados no AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **AirWatch** como administrador.

2. No painel de navegação do lado esquerdo, clique em **Contas,** e clique em **Utilizadores**.
  
   ![Utilizadores](./media/airwatch-tutorial/ic791929.png "Utilizadores")

3. No menu **Utilizadores,** clique em **Lista de Visualização**, e, em seguida, clique em **Adicionar > Adicionar Utilizador**.
  
   ![Adicionar Utilizador](./media/airwatch-tutorial/ic791930.png "Adicionar Utilizador")

4. No diálogo **Adicionar / Editar Utilizador,** execute os seguintes passos:

   ![Adicionar Utilizador](./media/airwatch-tutorial/ic791931.png "Adicionar Utilizador")

   a. Digite o **nome**de utilizador , **Palavra-passe,** **Confirme palavra-passe,** **Primeiro Nome,** **Apelido,** Endereço de **E-mail** de uma conta de Diretório Ativo Azure válida que pretende fornecer nas caixas de texto relacionadas.

   b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do AirWatch ou APIs fornecidas pelo AirWatch para fornecer contas de utilizador da AD Azure.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo AirWatch no Painel de Acesso, deve ser automaticamente inscrito no AirWatch para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
