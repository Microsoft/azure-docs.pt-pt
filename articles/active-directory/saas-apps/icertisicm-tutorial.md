---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Plataforma de Gestão de Contratos Icertis | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Icertis Contract Management Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: f6b1ea00684495333ba7b04dbf146aa6104fa589
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460517"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Tutorial: Integração do Diretório Ativo Azure com a Plataforma de Gestão de Contratos Icertis

Neste tutorial, aprende-se a integrar a Plataforma de Gestão de Contratos Icertis com o Azure Ative Directory (Azure AD).
Integrar a Plataforma de Gestão de Contratos Icertis com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Icertis Contract Management Platform.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Plataforma de Gestão de Contratos Icertis (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a Plataforma de Gestão de Contratos Icertis, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Icertis Contract Management Platform única assinatura ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Plataforma de Gestão de Contratos Icertis suporta SSO iniciado **pela SP**

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Adicionar plataforma de gestão de contratos Icertis da galeria

Para configurar a integração da Plataforma de Gestão de Contratos Icertis em AD Azure, é necessário adicionar a Plataforma de Gestão de Contratos Icertis da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar a Plataforma de Gestão de Contratos Icertis da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **Icertis Contract Management Platform**, selecione **Icertis Contract Management Platform** a partir do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

     ![Plataforma de Gestão de Contratos Icertis na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com a Plataforma de Gestão de Contratos Icertis com base num utilizador de teste chamado **Britta Simon.**
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma de Gestão de Contratos Icertis.

Para configurar e testar o Azure AD com a Plataforma de Gestão de Contratos Icertis, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a plataforma de gestão de contratos Icertis Single Sign-On](#configure-icertis-contract-management-platform-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Icertis Contract Management Platform test user](#create-icertis-contract-management-platform-test-user)** - para ter uma contraparte de Britta Simon na Icertis Contract Management Platform que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Plataforma de Gestão de Contratos Icertis, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Plataforma de Gestão de Contratos Icertis,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Icertis Contract Management Platform Domain e URLs informações únicas de acesso](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<company name>.icertis.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<company name>.icertis.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a plataforma de [suporte do cliente da Plataforma de Gestão de Contratos Icertis](https://www.icertis.com/company/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Plataforma de Gestão de Contratos Icertis,** copie os URL(s) apropriados de acordo com o seu requisito. Para **URL de início de sessão,** utilize o valor com o seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é o id de inquilino da assinatura AD Azure.

    ![URLs de configuração de cópia](media/icertisicm-tutorial/configurls.png)

    a. Identificador de Azure Ad

    b. Logout URL

### <a name="configure-icertis-contract-management-platform-single-sign-on"></a>Configure plataforma de gestão de contratos Icertis Sign-On

Para configurar um único sign-on na **plataforma de gestão de contratos Icertis,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte da Plataforma de Gestão de Contratos Icertis](https://www.icertis.com/company/contact/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com.

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, concedendo acesso à Plataforma de Gestão de Contratos Icertis.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **plataforma de gestão de contratos Icertis**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **plataforma de gestão de contratos Icertis.**

    ![A plataforma de gestão de contratos Icertis liga na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-icertis-contract-management-platform-test-user"></a>Criar utilizador de teste da Plataforma de Gestão de Contratos Icertis

Nesta secção, cria-se um utilizador chamado Britta Simon na Plataforma de Gestão de Contratos Icertis. Trabalhe com a equipa de [suporte da Plataforma de Gestão de Contratos Icertis](https://www.icertis.com/company/contact/) para adicionar os utilizadores na plataforma Icertis Contract Management Platform. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na Plataforma de Gestão de Contratos Icertis no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma de Gestão de Contratos Icertis para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)