---
title: 'Tutorial: Integração do Diretório Ativo Azure com a IBM Kenexa Survey Enterprise [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a IBM Kenexa Survey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: c649b966b3e210f6b026b06a9654761e0f97aea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099062"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integração de Diretório Ativo Azure com a IBM Kenexa Survey Enterprise

Neste tutorial, aprende-se a integrar a IBM Kenexa Survey Enterprise com o Azure Ative Directory (Azure AD).
Integrar a IBM Kenexa Survey Enterprise com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à IBM Kenexa Survey Enterprise.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na IBM Kenexa Survey Enterprise (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da Azure AD com a IBM Kenexa Survey Enterprise, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Ibm Kenexa Survey Enterprise assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* IBM Kenexa Survey Enterprise apoia **IDP** iniciado SSO

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Adicionando a IBM Kenexa Survey Enterprise da galeria

Para configurar a integração da IBM Kenexa Survey Enterprise em Azure AD, você precisa adicionar IBM Kenexa Survey Enterprise da galeria à sua lista de aplicações geridas saaS.

**Para adicionar a IBM Kenexa Survey Enterprise a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite A **IBM Kenexa Survey Enterprise,** selecione **IBM Kenexa Survey Enterprise** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![IBM Kenexa Survey Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure ad com a IBM Kenexa Survey Enterprise com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na IBM Kenexa Survey Enterprise.

Para configurar e testar o único sign-on da Azure AD com a IBM Kenexa Survey Enterprise, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On da IBM Kenexa Survey Enterprise](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste IBM Kenexa Survey Enterprise](#create-ibm-kenexa-survey-enterprise-test-user)** - para ter uma contrapartida da Britta Simon na IBM Kenexa Survey Enterprise que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com a IBM Kenexa Survey Enterprise, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **IBM Kenexa Survey Enterprise,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![IBM Kenexa Survey Enterprise Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://surveys.kenexa.com/<companycode>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de apoio ao cliente da [IBM Kenexa Survey Enterprise para](https://www.ibm.com/support/home/?lnk=fcw) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A aplicação IBM Kenexa Survey Enterprise espera receber as afirmações de Linguagem markup de afirmações de segurança (SAML) num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração dos seus atributos de token SAML. O valor da alegação do utilizador identificador na resposta deve corresponder ao ID SSO configurado no sistema Kenexa. Para mapear o identificador de utilizador apropriado na sua organização como SSO Internet Datagram Protocol (IDP), trabalhe com a equipa de suporte da [IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    Por predefinição, a Azure AD define o identificador do utilizador como o valor principal do utilizador (UPN). Pode alterar este valor no separador **Atributos** do Utilizador, como mostra a seguinte imagem. A integração só funciona depois de ter concluído o mapeamento corretamente.

    ![image](common/edit-attribute.png)

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção **set Up IBM Kenexa Survey Enterprise,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Configure IBM Kenexa Survey Enterprise Single Sign-On

Para configurar um único sign-on no lado da **IBM Kenexa Survey Enterprise,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte IBM [Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à IBM Kenexa Survey Enterprise.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **IBM Kenexa Survey Enterprise**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **IBM Kenexa Survey Enterprise**.

    ![A ligação IBM Kenexa Survey Enterprise na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Criar o utilizador de teste IBM Kenexa Survey Enterprise

Nesta secção, cria-se uma utilizadora chamada Britta Simon na IBM Kenexa Survey Enterprise.

Para criar utilizadores no sistema IBM Kenexa Survey Enterprise e mapear o ID SSO para eles, você pode trabalhar com a equipe de suporte IBM [Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Este valor de ID SSO também deve ser mapeado para o valor do identificador de utilizador a partir de Azure AD. Pode alterar esta definição predefinida no separador **Atributo.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IBM Kenexa Survey Enterprise no Painel de Acesso, deve ser automaticamente inscrito na IBM Kenexa Survey Enterprise para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

