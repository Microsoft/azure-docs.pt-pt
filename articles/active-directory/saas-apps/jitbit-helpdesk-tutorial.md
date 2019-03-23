---
title: 'Tutorial: Integração do Active Directory do Azure com o suporte técnico de Jitbit | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o suporte técnico de Jitbit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 71b7cfb92b4caded75e0723564a09fae9f10858c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359778"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Active Directory do Azure com o suporte técnico de Jitbit

Neste tutorial, saiba como integrar o suporte técnico de Jitbit com o Azure Active Directory (Azure AD).
Integrar o suporte técnico de Jitbit com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao suporte técnico de Jitbit.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o suporte técnico de Jitbit (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o suporte técnico de Jitbit, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Suporte técnico de Jitbit logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a assistência técnica de Jitbit **SP** iniciada SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Adicionando suporte técnico de Jitbit da Galeria

Para configurar a integração do suporte técnico de Jitbit com o Azure AD, terá de adicionar suporte técnico de Jitbit a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar suporte técnico de Jitbit a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **suporte técnico de Jitbit**, selecione **Jitbit Helpdesk** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Suporte técnico de Jitbit na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o suporte técnico de Jitbit com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no suporte técnico de Jitbit deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o suporte técnico de Jitbit, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Jitbit suporte técnico início de sessão único](#configure-jitbit-helpdesk-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de suporte técnico de Jitbit](#create-jitbit-helpdesk-test-user)**  - para ter um equivalente da Eduarda Almeida no suporte técnico Jitbit que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o suporte técnico de Jitbit, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Jitbit Helpdesk** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de suporte técnico Jitbit e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de suporte técnico Jitbit](https://www.jitbit.com/support/) para obter este valor.

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL seguinte: `https://www.jitbit.com/web-helpdesk/`

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na **configurar a assistência técnica de Jitbit** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Configurar a assistência técnica de Jitbit início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Jitbit Helpdesk como um administrador.

1. Na barra de ferramentas na parte superior, clique em **administração**.

    ![Administração](./media/jitbit-helpdesk-tutorial/ic777681.png "administração")

1. Clique em **definições gerais**.

    ![Os utilizadores, empresas e permissões](./media/jitbit-helpdesk-tutorial/ic777680.png "utilizadores, as empresas e permissões")

1. Na **definições de autenticação** configuração secção, execute os seguintes passos:

    ![Definições de autenticação](./media/jitbit-helpdesk-tutorial/ic777683.png "definições de autenticação")

    a. Selecione **ativar SAML 2.0 único início de sessão**, para iniciar sessão com o único início de sessão (SSO), com **OneLogin**.

    b. Na **URL de ponto final** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Abra sua **base 64** codificado de certificado no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto

    d. Clique em **guardar alterações**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao suporte técnico de Jitbit.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Jitbit Helpdesk**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **suporte técnico de Jitbit**.

    ![A ligação de suporte técnico de Jitbit na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-jitbit-helpdesk-test-user"></a>Criar utilizador de teste de suporte técnico de Jitbit

Para habilitar os utilizadores do Azure AD iniciar sessão no suporte técnico de Jitbit, tem de ser aprovisionados no suporte técnico de Jitbit. No caso de suporte técnico de Jitbit, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **suporte técnico de Jitbit** inquilino.

1. No menu na parte superior, clique em **administração**.

    ![Administração](./media/jitbit-helpdesk-tutorial/ic777681.png "administração")

1. Clique em **utilizadores, as empresas e permissões**.

    ![Os utilizadores, empresas e permissões](./media/jitbit-helpdesk-tutorial/ic777682.png "utilizadores, as empresas e permissões")

1. Clique em **Adicionar utilizador**.

    ![Adicionar utilizador](./media/jitbit-helpdesk-tutorial/ic777685.png "adicionar utilizador")

1. Na secção de criar, escreva os dados da conta do Azure AD que pretende aprovisionar da seguinte forma:

    ![Crie](./media/jitbit-helpdesk-tutorial/ic777686.png "criar")

   a. Na **nome de utilizador** caixa de texto, como o tipo o nome de utilizador do utilizador **BrittaSimon**.

   b. Na **E-Mail** caixa de texto, como o tipo de e-mail do utilizador **BrittaSimon@contoso.com**.

   c. Na **nome próprio** caixa de texto, nome do utilizador, como o tipo **Eduarda**.

   d. Na **sobrenome** caixa de texto, último nome de tipo do utilizador, como **Simon**.

   e. Clique em **Criar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de suporte técnico de Jitbit ou APIs fornecidas pelo suporte técnico de Jitbit para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de suporte técnico de Jitbit no painel de acesso, deve ser automaticamente conectado para a assistência técnica de Jitbit para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
