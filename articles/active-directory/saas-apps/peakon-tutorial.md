---
title: 'Tutorial: Integração do Active Directory do Azure com Peakon | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: bf291f22f523756c868128cbe5595fa56cf7d109
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361681"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutorial: Integração do Active Directory do Azure com Peakon

Neste tutorial, saiba como integrar Peakon com o Azure Active Directory (Azure AD).
Integrar Peakon no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Peakon.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Peakon (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Peakon, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Peakon logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Peakon **SP** e **IDP** iniciada SSO

## <a name="adding-peakon-from-the-gallery"></a>Adicionando Peakon da Galeria

Para configurar a integração do Peakon com o Azure AD, terá de adicionar Peakon a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Peakon a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Peakon**, selecione **Peakon** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Peakon com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Peakon deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Peakon, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Peakon Single Sign-On](#configure-peakon-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Peakon](#create-peakon-test-user)**  - para ter um equivalente da Eduarda Almeida na Peakon que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Peakon, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Peakon** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Peakon domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://app.peakon.com/saml/<companyid>/assert`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Peakon domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.peakon.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

7. Sobre o **configurar Peakon** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-peakon-single-sign-on"></a>Configurar Peakon Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Peakon como um administrador.

2. Na barra de menu no lado esquerdo da página, clique em **Configuration**, em seguida, navegue até à **integrações**.

    ![A configuração](./media/peakon-tutorial/tutorial_peakon_config.png)

3. No **integrações** página, clique em **Single Sign-On**.

    ![A única](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Sob **início de sessão único** secção, clique em **ativar**.

    ![A ativar](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Sobre o **início de sessão único para os funcionários através de SAML** secção, execute os seguintes passos:

    ![O saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Na **URL de início de sessão de SSO** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Na **URL de fim de sessão de SSO** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    c. Clique em **Escolher ficheiro** para carregar o certificado que transferiu do portal do Azure, na caixa de certificado.

    d. Clique nas **ícone** para copiar o **ID de entidade** e cole no **identificador** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    e. Clique nas **ícone** para copiar o **URL de resposta (ACS)** e cole no **URL de resposta** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    f. Clicar em **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Peakon.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Peakon**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Peakon**.

    ![A ligação de Peakon na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-peakon-test-user"></a>Criar utilizador de teste Peakon

Para ativar os utilizadores do Azure AD iniciar sessão no Peakon, tem de ser aprovisionados em Peakon.  
No caso de Peakon, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Peakon como um administrador.

2. Na barra de menu no lado esquerdo da página, clique em **Configuration**, em seguida, navegue até à **funcionários**.

    ![O funcionário](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. No canto superior direito da página, clique em **funcionário adicionar**.

      ![O funcionário de adicionar](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Sobre o **novo funcionário** caixa de diálogo página, execute os seguintes passos:

     ![O novo funcionário](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Na **Name** caixa de texto, nome do primeiro tipo como **Eduarda** e o apelido como **simon**.

    b. Na **E-Mail** caixa de texto, escreva o endereço de e-mail, como **Brittasimon\@contoso.com**.

    c. Clique em **funcionário criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Peakon no painel de acesso, deve ser automaticamente sessão iniciada no Peakon para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

