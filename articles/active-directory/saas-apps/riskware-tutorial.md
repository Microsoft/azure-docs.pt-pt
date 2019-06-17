---
title: 'Tutorial: Integração do Active Directory do Azure com Riskware | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b2bfbed33433521fd086d474ea4b754f5435f5e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092910"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Integração do Active Directory do Azure com Riskware

Neste tutorial, saiba como integrar Riskware com o Azure Active Directory (Azure AD).
Integrar Riskware no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Riskware.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Riskware (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Riskware, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Riskware logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Riskware **SP** iniciada SSO

## <a name="adding-riskware-from-the-gallery"></a>Adicionando Riskware da Galeria

Para configurar a integração do Riskware com o Azure AD, terá de adicionar Riskware a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Riskware a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Riskware**, selecione **Riskware** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Riskware na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Riskware com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Riskware deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Riskware, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Riskware Single Sign-On](#configure-riskware-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Riskware](#create-riskware-test-user)**  - para ter um equivalente da Eduarda Almeida na Riskware que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Riskware, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Riskware** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Riskware domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Na **identificador (ID de entidade)** caixa de texto, escreva o URL:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > O valor do URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Riskware](mailto:support@pansoftware.com.au) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Riskware** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-riskware-single-sign-on"></a>Configurar Riskware Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Riskware como um administrador.

1. No canto superior direito, clique em **manutenção** para abrir a página de manutenção.

    ![Manter as configurações de Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **autenticação**.

    ![Configuração de Riskware authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na **configuração da autenticação** página, execute os seguintes passos:

    ![Configuração de Riskware authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecione **tipo** como **SAML** para autenticação.

    b. Na **código** caixa de texto, escreva o seu código, como AZURE_UAT.

    c. Na **Descrição** caixa de texto, escreva a sua descrição, como a configuração do AZURE para SSO.

    d. Na **início de sessão único na página** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    e. Na **termine a página** caixa de texto, colar a **URL de fim de sessão** valor, que copiou do portal do Azure.

    f. Na **campo de formulário de Post** caixa de texto, escreva o nome do campo presente na resposta de postagem que contém o SAML como SAMLResponse

    g. Na **nome da Tag XML identidade** textbox, o atributo de tipo, que contém o identificador exclusivo na resposta, como NameID de SAML.

    h. Abra o transferido **Xml de metadados** partir do portal do Azure no bloco de notas, copie o certificado a partir do ficheiro de metadados e colá-lo no **certificado** caixa de texto

    i. Na **URL de consumidor** caixa de texto, cole o valor de **URL de resposta**, que recebe da equipa de suporte.

    j. Na **emissor** caixa de texto, cole o valor de **identificador**, que recebe da equipa de suporte.

    > [!Note]
    > Contacte [equipa de suporte de cliente Riskware](mailto:support@pansoftware.com.au) obter esses valores

    k. Selecione **utilize POST** caixa de verificação.

    l. Selecione **pedido de SAML utilização** caixa de verificação.

    m. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Riskware.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Riskware**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Riskware**.

    ![A ligação de Riskware na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-riskware-test-user"></a>Criar utilizador de teste Riskware

Para ativar a utilizadores do Azure AD iniciar sessão no Riskware, tem de ser aprovisionados em Riskware. Riskware, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Riskware como um administrador de segurança.

1. No canto superior direito, clique em **manutenção** para abrir a página de manutenção. 

    ![Configuração de Riskware mantém](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **pessoas**.

    ![Pessoas de configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecione **detalhes** separador e execute os seguintes passos:

    ![Detalhes de configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecione **tipo de pessoa** como empregados.

    b. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.

1. Sobre o **segurança** separador, execute os seguintes passos:

    ![Segurança da configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Sob **autenticação** secção, selecione a **autenticação** modo, o que tem a configuração, como a configuração do AZURE para SSO.

    b. Sob **detalhes de início de sessão** secção, além do **ID de utilizador** caixa de texto, introduza o e-mail do utilizador, como `brittasimon@contoso.com`.

    c. Na **palavra-passe** caixa de texto, introduza a palavra-passe do utilizador.

1. Sobre o **organização** separador, execute os seguintes passos:

    ![Configuração de Riskware org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecione a opção como **Level1** organização.

    b. Sob **à área de trabalho principal da pessoa** secção, além do **localização** caixa de texto, escreva a sua localização.

    c. Sob **funcionário** secção, selecione **estado de funcionário** como informais.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Riskware no painel de acesso, deve ser automaticamente sessão iniciada no Riskware para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
