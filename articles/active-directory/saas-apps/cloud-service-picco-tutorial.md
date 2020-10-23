---
title: 'Tutorial: Integração do Azure Ative Directory com o Cloud Service PICCO Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Cloud Service PICCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.openlocfilehash: fe113575c9ac70efc0afc571758eea668a23d8cb
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455821"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-service-picco"></a>Tutorial: Integração do Azure Ative Directory com o Cloud Service PICCO

Neste tutorial, aprende-se a integrar o Cloud Service PICCO com o Azure Ative Directory (Azure AD).
Integrar o Cloud Service PICCO com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Cloud Service PICCO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Cloud Service PICCO (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Cloud Service PICCO, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pelo Serviço de Cloud PICCO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Cloud Service PICCO suporta **SSO** iniciado sp
* Cloud Service PICCO suporta o fornecimento **de utilizadores just in time**

## <a name="adding-cloud-service-picco-from-the-gallery"></a>Adicionar Cloud Service PICCO da galeria

Para configurar a integração do Cloud Service PICCO em AD Azure, precisa adicionar o Cloud Service PICCO da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Cloud Service PICCO na galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Cloud Service PICCO**, selecione **Cloud Service PICCO** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

     ![Cloud Service PICCO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Cloud Service PICCO com base num utilizador de teste chamado **Britta Simon.**
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Cloud Service PICCO.

Para configurar e testar o Azure AD com o Cloud Service PICCO, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Serviço de Nuvem PICCO Single Sign-On](#configure-cloud-service-picco-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Create Cloud Service PICCO test user](#create-cloud-service-picco-test-user)** - para ter uma contraparte de Britta Simon no Cloud Service PICCO que está ligada à representação AD AD Azure do utilizador.
5. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com cloud service PICCO, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **picco do Serviço de Nuvem,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Cloud Service PICCO Domain e URLs informações únicas de súmis](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<SUB DOMAIN>.cloudservicepicco.com/app`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `<SUB DOMAIN>.cloudservicepicco.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUB DOMAIN>.cloudservicepicco.com/app`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do cliente do Serviço de Nuvem PICCO](mailto:picco.support@est.fujitsu.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-cloud-service-picco-single-sign-on"></a>Configure serviço de nuvem PICCO single Sign-On

Para configurar um único sinal no lado **PICCO do Serviço de Nuvem,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de [suporte PICCO do Serviço de Nuvem](mailto:picco.support@est.fujitsu.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Cloud Service PICCO.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Cloud Service PICCO**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cloud Service PICCO**.

    ![O link PICCO do Serviço cloud na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-cloud-service-picco-test-user"></a>Criar utilizador de teste picco de serviço de nuvem

Nesta secção, um utilizador chamado Britta Simon é criado no Cloud Service PICCO. O Cloud Service PICCO suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Cloud Service PICCO, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PICCO do Serviço de Nuvem no Painel de Acesso, deverá ser automaticamente inscrito no Serviço de Cloud PICCO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)