---
title: 'Tutorial: Integração do Diretório Ativo Azure com Infogix Data3Sixty Govern | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Infogix Data3Sixty Govern.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 7ca566c554c02d20d21bd7eba87dfbcbf1f746da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606492"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Tutorial: Integração do Azure Ative Directory com Infogix Data3Sixty Govern

Neste tutorial, aprende-se a integrar o Infogix Data3Sixty Govern com o Azure Ative Directory (Azure AD).
A integração do Infogix Data3Sixty Governe com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Infogix Data3Sixty Govern.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Infogix Data3Sixty Govern (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Infogix Data3Sixty Govern, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Infogix Data3Sixty Reger subscrição única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Infogix Data3Sixty Govern suporta **SP e IDP** iniciado SSO
* Infogix Data3Sixty Govern suporta **provisão do** utilizador just in time

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Adicionar Infogix Data3Sixty Governe a partir da galeria

Para configurar a integração do Infogix Data3Sixty Govern em AD Azure, é necessário adicionar Infogix Data3Sixty Govern da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar Infogix Data3Sixty Governe da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Infogix Data3Sixty Govern**, selecione **Infogix Data3Sixty Govern** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Infogix Data3Sixty Governe na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com infogix Data3Sixty Govern com base num utilizador de teste chamado **Britta Simon**.
Para um único login no trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado no Infogix Data3Sixty Govern.

Para configurar e testar o Azure AD com o Infogix Data3Sixty Govern, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure infogix Data3Sixty Regem o Único Sinal](#configure-infogix-data3sixty-govern-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Infogix Data3Sixty Govern utilizador](#create-infogix-data3sixty-govern-test-user)** de teste - para ter uma contraparte de Britta Simon em Infogix Data3Sixty Govern que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com Infogix Data3Sixty Govern, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Infogix Data3Sixty Govern,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `https://data3sixty.com/ui`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.data3sixty.com/sso/acs`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.data3sixty.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Contacte a [equipa de suporte do Cliente Infogix Data3Sixty Para](mailto:data3sixtysupport@infogix.com) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A aplicação Infogix Data3Sixty Govern espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

7. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Name | Atributo de origem|
    | -----------| -------------- |
    | nome de primeiro nome  | user.givenname |
    | último nome | utilizador.sobrenome |
    | nome de utilizador | user.mail |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

9. Na secção **Configurar Infogix Data3Sixty Govern,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-infogix-data3sixty-govern-single-sign-on"></a>Configure Infogix Data3Sixty Govern single Sign-On

Para configurar um único sign-on no lado **infogix Data3Sixty Govern,** é necessário enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipa de [suporte Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.

    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, concedendo acesso ao Infogix Data3Sixty Govern.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Infogix Data3Sixty Govern**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Infogix Data3Sixty Govern**.

    ![O link Infogix Data3Sixty Govern na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-infogix-data3sixty-govern-test-user"></a>Criar infogix Data3Sixty Reger utilizador de teste

Nesta secção, um utilizador chamado Britta Simon é criado no Infogix Data3Sixty Govern. Infogix Data3Sixty Govern suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Infogix Data3Sixty Govern, um novo é criado após a autenticação.

> [!Note]
> Se necessitar de criar um utilizador manualmente, contacte a equipa de [suporte Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Infogix Data3Sixty Govern no Painel de Acesso, deverá ser automaticamente inscrito no Infogix Data3Sixty Re para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)