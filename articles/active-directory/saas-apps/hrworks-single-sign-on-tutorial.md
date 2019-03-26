---
title: 'Tutorial: Integração do Active Directory do Azure com HRworks Single Sign-On | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e HRworks Single Sign-On.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: df3799111a26afe33cee5f7b6ee1bc3fc6989758
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439722"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Tutorial: Integração do Active Directory do Azure com HRworks início de sessão único

Neste tutorial, saiba como integrar HRworks início de sessão único com o Azure Active Directory (Azure AD).
Integrar HRworks início de sessão único com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para HRworks início de sessão único.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para HRworks início de sessão único (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HRworks início de sessão único, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* HRworks início de sessão único início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* HRworks Single Sign-On suporta **SP** iniciada SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Adicionando HRworks início de sessão único da Galeria

Para configurar a integração de HRworks início de sessão único com o Azure AD, terá de adicionar HRworks início de sessão único a partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar HRworks início de sessão único a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **HRworks Single Sign-On**, selecione **HRworks Single Sign-On** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![HRworks início de sessão único na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com HRworks início de sessão único com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no HRworks início de sessão único deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com HRworks início de sessão único, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar HRworks Sign-On único início de sessão único](#configure-hrworks-single-sign-on-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)**  - para ter um equivalente da Eduarda Almeida na HRworks Single Sign-On que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com HRworks início de sessão único, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **HRworks Single Sign-On** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![HRworks único início de sessão no domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de HRworks único início de sessão no cliente](mailto:nadja.sommerfeld@hrworks.de) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **definir segurança HRworks início de sessão único** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Configurar HRworks início de sessão único início de sessão único

1. Numa janela do browser web diferente, inicie sessão para HRworks Single Sign-On como um administrador.

2. Clique em **administrador** > **Noções básicas** > **segurança** > **início de sessão único** do à esquerda do menu de barras e execute os seguintes passos:

       ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Verifique os **utilização início de sessão único** caixa.

    b. Selecione **metadados XML** como **método de entrada de metadados**.

    c. Selecione **NameID individuais identificador** como **valor para NameID**.

    d. No bloco de notas, abra o XML de metadados que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **metadados** caixa de texto.

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite o nome de utilizador como BrittaSimon@contoso.com.

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para HRworks início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **HRworks Single Sign-On**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **HRworks Single Sign-On**.

    ![A ligação HRworks início de sessão único na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-hrworks-single-sign-on-test-user"></a>Criar HRworks início de sessão único do utilizador de teste

Para ativar os utilizadores do Azure AD, inicie sessão para HRworks início de sessão único, eles têm de ser aprovisionados em HRworks início de sessão único. No HRworks início de sessão único, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no HRworks Single Sign-On como administrador.

2. Clique em **administrador** > **pessoas** > **pessoas** > **nova pessoa** das lado esquerdo da barra de menus.

     ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. No pop-up, clique em **seguinte**.

    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Sobre o **criar nova pessoa com país para termos legais** preenchimento pop-up, como os respectivos detalhes **nome próprio**, **Apelido** e clique em **criar**.
    
    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico HRworks Single Sign-On no painel de acesso, deve ser automaticamente conectado para o HRworks início de sessão único para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

