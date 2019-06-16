---
title: 'Tutorial: Integração do Active Directory do Azure com o Insights adaptável | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Insights adaptável.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: c217663c5752907e0b3d6372d4522f6aba982b3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107397"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Tutorial: Integração do Active Directory do Azure com o Insights adaptável

Neste tutorial, saiba como integrar o Insights adaptáveis com o Azure Active Directory (Azure AD).
Integrar o Insights adaptáveis com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso às informações adaptável.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Insights adaptável (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Insights adaptável, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Insights adaptáveis único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a Insights adaptável **IDP** iniciada SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adicionando Insights adaptável da Galeria

Para configurar a integração de informações adaptáveis com o Azure AD, terá de adicionar informações adaptável a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar informações adaptável a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Insights adaptável**, selecione **Insights adaptável** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Adaptáveis Insights na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Insights adaptáveis com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Insights adaptável deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Insights adaptável, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar adaptável Insights início de sessão único](#configure-adaptive-insights-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Insights adaptável](#create-adaptive-insights-test-user)**  - para ter um equivalente da Eduarda Almeida no Insights adaptável, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Insights adaptável, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Insights adaptável** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![Informações de início de sessão de único adaptável Insights domínio e URLs](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Pode obter o identificador (ID de entidade) e valores de URL de resposta do insights adaptável **as definições de SSO SAML** página.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Insights adaptável** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-adaptive-insights-single-sign-on"></a>Configurar Insights adaptável início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Insights adaptável como um administrador.

2. Aceda a **administrador**.

    ![Admin](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. Na **utilizadores e funções** secção, clique em **gerir as definições de SSO SAML**.

    ![Gerir as definições de SAML SSO](./media/adaptivesuite-tutorial/ic805645.png "gerir as definições de SAML SSO")

4. Sobre o **as definições de SSO SAML** página, execute os seguintes passos:

    ![As definições de SAML SSO](./media/adaptivesuite-tutorial/ic805646.png "as definições de SAML SSO")

    a. Na **nome do fornecedor de identidade** caixa de texto, escreva um nome para a sua configuração.

    b. Colar o **identificador do Azure Ad** valor copiados a partir do portal do Azure para o **ID de entidade do fornecedor de identidade** caixa de texto.

    c. Colar o **URL de início de sessão** valor copiados a partir do portal do Azure para o **URL de SSO de fornecedor de identidade** caixa de texto.

    d. Colar o **URL de fim de sessão** valor copiados a partir do portal do Azure para o **URL de fim de sessão personalizada** caixa de texto.

    e. Para carregar o certificado transferido, clique em **Escolher ficheiro**.

    f. Selecione o seguinte, para:

     * **Id de utilizador SAML**, selecione **nome de utilizador do utilizador Insights adaptável**.

     * **Localização do id de utilizador SAML**, selecione **id de utilizador no NameID de requerente**.

     * **Formato NameID de SAML**, selecione **endereço de E-Mail**.

     * **Ativar SAML**, selecione **permitir que o SAML SSO e início de sessão direto Insights adaptável**.

    g. Cópia **URL de informações do SSO adaptável** e cole o **identificador (ID de entidade)** e **URL de resposta** caixas de texto no **adaptável de informações de domínio e URLs** secção no portal do Azure.

    h. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo "brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com.

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso às informações adaptável.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Insights adaptável**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Insights adaptável**.

    ![A ligação de adaptável Insights na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-adaptive-insights-test-user"></a>Criar utilizador de teste de Insights adaptável

Para ativar a utilizadores do Azure AD iniciar sessão no Insights adaptável, tem de ser aprovisionados em informações adaptável. No caso de Insights adaptável, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Insights adaptável** site da empresa como administrador.

2. Aceda a **administrador**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. Na **utilizadores e funções** secção, clique em **adicionar utilizador**.

   ![Adicionar utilizador](./media/adaptivesuite-tutorial/IC805648.png "adicionar utilizador")

4. Na **novo utilizador** secção, execute os seguintes passos:

   ![Submeter](./media/adaptivesuite-tutorial/IC805649.png "submeter")

   a. Tipo de **nome**, **início de sessão**, **E-Mail**, **palavra-passe** de um utilizador válido do Azure Active Directory que pretende aprovisionar no relacionados caixas de texto.

   b. Selecione um **função**.

   c. Clique em **Submit** (Submeter).

> [!NOTE]
> Pode utilizar quaisquer outras informações adaptável utilizador conta criação ferramentas ou APIs fornecidas pelo Insights adaptável para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Insights adaptável no painel de acesso, deve ser automaticamente sessão iniciada no Insights adaptável para os quais configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)