---
title: 'Tutorial: Integração do Active Directory do Azure com SpaceIQ | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b3358ad473ede9e8d78a835e8c68e690e5340638
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090097"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Tutorial: Integração do Active Directory do Azure com SpaceIQ

Neste tutorial, saiba como integrar SpaceIQ com o Azure Active Directory (Azure AD).
Integrar SpaceIQ no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SpaceIQ.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SpaceIQ (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SpaceIQ, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* SpaceIQ logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta SpaceIQ **IDP** iniciada SSO

## <a name="adding-spaceiq-from-the-gallery"></a>Adicionando SpaceIQ da Galeria

Para configurar a integração do SpaceIQ com o Azure AD, terá de adicionar SpaceIQ a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SpaceIQ a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SpaceIQ**, selecione **SpaceIQ** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![SpaceIQ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SpaceIQ com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SpaceIQ deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com SpaceIQ, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SpaceIQ Single Sign-On](#configure-spaceiq-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste SpaceIQ](#create-spaceiq-test-user)**  - para ter um equivalente da Eduarda Almeida na SpaceIQ que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com SpaceIQ, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SpaceIQ** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![SpaceIQ domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva o URL: `https://api.spaceiq.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Atualize estes valores com o URL de resposta real e o identificador que é explicado mais tarde no tutorial.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar SpaceIQ** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-spaceiq-single-sign-on"></a>Configurar SpaceIQ Single Sign-On

1. Abra uma nova janela do browser e, em seguida, inicie sessão no seu ambiente de SpaceIQ como administrador.

1. Assim que tiver iniciado a sessão, clique no sinal de quebra-cabeça no canto superior direito, em seguida, clique em **integrações**

    ![Definições da conta](./media/spaceiq-tutorial/setting1.png) 

1. Sob **aprovisionamento todos os & SSO**, clique nas **Azure** mosaico para adicionar uma instância do Azure como o IDP.

    ![Ícone SAML](./media/spaceiq-tutorial/setting2.png)

1. Na **SSO** diálogo caixa, execute os seguintes passos:

    ![Definições de autenticação SAML](./media/spaceiq-tutorial/setting3.png)

    a. Na **URL de emissor de SAML** caixa, cole a **do Azure AD identificador** valor copiados a partir da janela de configuração de aplicação do Azure AD.

    b. Cópia a **URL de ponto final de retorno de chamada SAML (só de leitura)** valor e cole o valor no **URL de resposta** caixa o **configuração básica de SAML** secção no portal do Azure.

    c. Cópia a **URI de audiência de SAML (só de leitura)** valor e cole o valor no **identificador** caixa o **configuração básica de SAML** secção no portal do Azure.

    d. Abra o ficheiro de certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-a no **certificado X.509** caixa.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para SpaceIQ.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SpaceIQ**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SpaceIQ**.

    ![A ligação de SpaceIQ na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-spaceiq-test-user"></a>Criar utilizador de teste SpaceIQ

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SpaceIQ. Trabalho [equipa de suporte de SpaceIQ](mailto:eng@spaceiq.com) para adicionar os utilizadores na plataforma SpaceIQ. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico SpaceIQ no painel de acesso, deve ser automaticamente sessão iniciada no SpaceIQ para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

