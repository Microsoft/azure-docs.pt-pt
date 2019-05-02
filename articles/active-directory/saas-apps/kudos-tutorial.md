---
title: 'Tutorial: Integração do Active Directory do Azure com Parabéns pelo artigo | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Parabéns pelo artigo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 00492ba379a4b4d8ae7437d18f9b2880dae03e44
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917455"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integração do Active Directory do Azure com Parabéns pelo artigo

Neste tutorial, saiba como integrar Parabéns pelo artigo com o Azure Active Directory (Azure AD).
Parabéns pelo artigo a integração com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Parabéns pelo artigo.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Parabéns pelo artigo (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Parabéns, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Parabéns pelo artigo logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Parabéns pelo artigo suporta **SP** iniciada SSO

## <a name="adding-kudos-from-the-gallery"></a>Adicionar Parabéns pelo artigo a partir da Galeria

Para configurar a integração de parabéns pelo artigo no Azure AD, terá de adicionar Parabéns pelo artigo a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Parabéns pelo artigo a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **parabéns**, selecione **Parabéns pelo artigo** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Parabéns pelo artigo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Parabéns pelo artigo com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Parabéns pelo artigo deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Parabéns, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Parabéns pelo artigo Single Sign-On](#configure-kudos-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de parabéns](#create-kudos-test-user)**  - para ter um equivalente da Eduarda Almeida na Parabéns pelo artigo que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Parabéns pelo artigo, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Parabéns pelo artigo** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Parabéns pelo artigo domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<company>.kudosnow.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de parabéns pelo artigo](http://success.kudosnow.com/home) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Parabéns pelo artigo** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-kudos-single-sign-on"></a>Configurar Parabéns pelo artigo início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Parabéns pelo artigo como administrador.

1. No menu na parte superior, clique em **ícone de definições**.

    ![As definições](./media/kudos-tutorial/ic787806.png "definições")

1. Clique em **integrações > SSO** e execute os seguintes passos:

    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")

    a. Na **iniciar sessão no URL** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    b. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto

    c. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    d. Na **seu URL de parabéns pelo artigo** caixa de texto, escreva o nome da sua empresa.

    e. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Parabéns pelo artigo.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Parabéns pelo artigo**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Parabéns pelo artigo**.

    ![A ligação de parabéns pelo artigo na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-kudos-test-user"></a>Criar utilizador de teste de parabéns pelo artigo

Para habilitar os utilizadores do Azure AD iniciar sessão no Parabéns pelo artigo, tem de ser aprovisionados em Parabéns pelo artigo. No caso de parabéns, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Parabéns pelo artigo** site da empresa como administrador.

1. No menu na parte superior, clique em **ícone de definições**.

   ![As definições](./media/kudos-tutorial/ic787806.png "definições")

1. Clique em **utilizador administrador**.

1. Clique nas **usuários** separador e, em seguida, clique em **adicionar um utilizador**.

   ![Utilizador administrador](./media/kudos-tutorial/ic787809.png "utilizador administrador")

1. Na **adicionar um utilizador** secção, execute os seguintes passos:

    ![Adicionar um usuário](./media/kudos-tutorial/ic787810.png "adicionar um utilizador")

    a. Tipo de **nome próprio**, **Apelido**, **E-Mail** e outros detalhes de uma conta válida do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas.

    b. Clique em **criar utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras Parabéns pelo artigo utilizador conta criação ferramentas ou APIs fornecidas pelo Parabéns pelo artigo para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de parabéns pelo artigo no painel de acesso, deve ser automaticamente sessão iniciada no Parabéns para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
