---
title: 'Tutorial: Integração do Active Directory do Azure com Appraisd | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5bcabd68735c22a245d102f0e55937ff11ce76
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404914"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Tutorial: Integração do Active Directory do Azure com Appraisd

Neste tutorial, saiba como integrar Appraisd com o Azure Active Directory (Azure AD).
Integrar Appraisd no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Appraisd.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Appraisd (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Appraisd, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Appraisd logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Appraisd **SP e IDP** iniciada SSO

## <a name="adding-appraisd-from-the-gallery"></a>Adicionando Appraisd da Galeria

Para configurar a integração do Appraisd com o Azure AD, terá de adicionar Appraisd a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Appraisd a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Appraisd**, selecione **Appraisd** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Appraisd na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Appraisd com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Appraisd deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Appraisd, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Appraisd Single Sign-On](#configure-appraisd-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Appraisd](#create-appraisd-test-user)**  - para ter um equivalente da Eduarda Almeida na Appraisd que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Appraisd, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Appraisd** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **configuração de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Appraisd domínio e URLs únicas início de sessão em informações](common/both-preintegrated-advanced-urls.png)

    a. Clique em **definir URLs adicionais**.

    b. Na **estado de reencaminhamento** caixa de texto, escreva um URL: `<TENANTCODE>`

    c. Se desejar configurar a aplicação no **SP** iniciadas pelo modo, na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Obter o valor de URL de início de sessão e estado de reencaminhamento real na página de configuração de SSO Appraisd do que é explicada mais tarde no tutorial.

5. Aplicação de Appraisd espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name |  Atributo de origem|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar Appraisd** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-appraisd-single-sign-on"></a>Configurar Appraisd Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Appraisd como um administrador de segurança.

2. No canto superior direito da página, clique em **definições** ícone, em seguida, navegue até à **configuração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. Do lado esquerdo do menu, clique em **SAML início de sessão único**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. Sobre o **SAML 2.0 Single Sign-On configuração** página, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Cópia a **estado de reencaminhamento do padrão** valor e cole-o na **estado de reencaminhamento** caixa de texto no **configuração básica de SAML** no portal do Azure.

    b. Copiar o **URL de início de sessão iniciadas pelo serviço** valor e cole-o na **URL de início de sessão** caixa de texto no **configuração básica de SAML** no portal do Azure.

5. Desloque para baixo na mesma página sob **identificar utilizadores**, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Na **URL fornecedor de identidade única Sign-On** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure e clique em **guardar**.

    b. Na **URL de emissor do fornecedor de identidade** caixa de texto, cole o valor de **identificador do Azure Ad**, que copiou do portal do Azure e clique em **guardar**.

    c. No bloco de notas, abra o certificado com codificação base 64 que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **certificado X.509** caixa e clique em **guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Appraisd.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Appraisd**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Appraisd**.

    ![A ligação de Appraisd na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-appraisd-test-user"></a>Criar utilizador de teste Appraisd

Para ativar o Azure AD os utilizadores iniciam sessão Appraisd, eles têm de ser aprovisionados em Appraisd. Appraisd, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Appraisd como um administrador de segurança.

2. No canto superior direito da página, clique em **definições** ícone, em seguida, navegue até à **Centro de administração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas na parte superior da página, clique em **pessoas**, em seguida, navegue até à **adicionar um novo utilizador**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Sobre o **adicionar um novo utilizador** página, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **Apelido** texto, digite o apelido do utilizador, como **simon**.

    c. Na **E-Mail** texto, introduza o e-mail do utilizador, como **Brittasimon@contoso.com**.

    d. Clique em **Adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Appraisd no painel de acesso, deve ser automaticamente sessão iniciada no Appraisd para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
