---
title: 'Tutorial: Integração do Azure Ative Directory com a IBM Kenexa Survey Enterprise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a IBM Kenexa Survey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459157"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integração do Azure Ative Directory com a IBM Kenexa Survey Enterprise

Neste tutorial, aprende-se a integrar a IBM Kenexa Survey Enterprise com o Azure Ative Directory (Azure AD).
Integrar a IBM Kenexa Survey Enterprise com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à IBM Kenexa Survey Enterprise.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na IBM Kenexa Survey Enterprise (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a IBM Kenexa Survey Enterprise, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* IBM Kenexa Survey Enterprise única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* IBM Kenexa Survey Enterprise apoia **IDP** iniciado SSO

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Adicionar IBM Kenexa Survey Enterprise da galeria

Para configurar a integração da IBM Kenexa Survey Enterprise em Azure AD, precisa adicionar a IBM Kenexa Survey Enterprise da galeria à sua lista de aplicações geridas pela SaaS.

**Para adicionar a IBM Kenexa Survey Enterprise da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite IBM Kenexa Survey Enterprise**, selecione **IBM Kenexa Survey Enterprise** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![IBM Kenexa Survey Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com IBM Kenexa Survey Enterprise com base em um utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na IBM Kenexa Survey Enterprise.

Para configurar e testar o Azure AD com a IBM Kenexa Survey Enterprise, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure IBM Kenexa Survey Enterprise Single Sign-On](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create IBM Kenexa Survey Enterprise test user](#create-ibm-kenexa-survey-enterprise-test-user)** - para ter uma contraparte de Britta Simon na IBM Kenexa Survey Enterprise que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com a IBM Kenexa Survey Enterprise, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **IBM Kenexa Survey Enterprise,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![IBM Kenexa Survey Enterprise Domain e URLs informações únicas de súbdico](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://surveys.kenexa.com/<companycode>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a [equipa de suporte do cliente da IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação IBM Kenexa Survey Enterprise espera receber as afirmações de Markup Language (SAML) de Afirmações de Segurança num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração dos seus atributos de token SAML. O valor da alegação do identificador de utilizador na resposta deve corresponder ao ID SSO configurado no sistema Kenexa. Para mapear o identificador de utilizador apropriado na sua organização como SSO Internet Datagram Protocol (IDP), trabalhe com a equipa de suporte da [IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    Por predefinição, o Azure AD define o identificador do utilizador como o nome principal do utilizador (UPN). Pode alterar este valor no separador Atributos do **Utilizador,** como mostrado na imagem seguinte. A integração só funciona depois de ter concluído o mapeamento corretamente.

    ![image](common/edit-attribute.png)

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configuração IBM Kenexa Survey Enterprise,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Configurar IBM Kenexa Survey Enterprise Single Sign-On

Para configurar um único sign-on no lado **da IBM Kenexa Survey Enterprise,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte IBM Kenexa Survey Enterprise.](https://www.ibm.com/support/home/?lnk=fcw) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon use a Azure single sign-on, concedendo acesso à IBM Kenexa Survey Enterprise.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **IBM Kenexa Survey Enterprise**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, **selecione IBM Kenexa Survey Enterprise**.

    ![A ligação IBM Kenexa Survey Enterprise na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Criar utilizador de teste IBM Kenexa Survey Enterprise

Nesta secção, cria-se um utilizador chamado Britta Simon na IBM Kenexa Survey Enterprise.

Para criar utilizadores no sistema IBM Kenexa Survey Enterprise e mapear o SSO ID para eles, você pode trabalhar com a equipe de [suporte IBM Kenexa Survey Enterprise.](https://www.ibm.com/support/home/?lnk=fcw) Este valor de ID SSO também deve ser mapeado para o valor do identificador do utilizador a partir de Azure AD. Pode alterar esta definição predefinida no **separador Atributo.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IBM Kenexa Survey Enterprise no Painel de Acesso, deverá ser automaticamente inscrito na IBM Kenexa Survey Enterprise para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)