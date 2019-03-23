---
title: 'Tutorial: Integração do Active Directory do Azure com Sprinklr | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: bf87764367580a2842fd856856bd517482a19ff2
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361409"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Active Directory do Azure com Sprinklr

Neste tutorial, saiba como integrar Sprinklr com o Azure Active Directory (Azure AD).
Integrar Sprinklr no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Sprinklr.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Sprinklr (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Sprinklr, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Sprinklr logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Sprinklr **SP** iniciada SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Adicionando Sprinklr da Galeria

Para configurar a integração do Sprinklr com o Azure AD, terá de adicionar Sprinklr a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Sprinklr a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Sprinklr**, selecione **Sprinklr** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Sprinklr na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Sprinklr com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Sprinklr deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Sprinklr, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Sprinklr](#create-sprinklr-test-user)**  - para ter um equivalente da Eduarda Almeida na Sprinklr que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Sprinklr, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Sprinklr** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Sprinklr domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.sprinklr.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Sprinklr](https://www.sprinklr.com/contact-us/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Sprinklr** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-sprinklr-single-sign-on"></a>Configurar Sprinklr Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Sprinklr como administrador.

1. Aceda a **Administration \> definições**.

    ![Administração](./media/sprinklr-tutorial/ic782907.png "administração")

1. Aceda a **gerir parceiro \> início de sessão único** no painel à esquerda.

    ![Gerir parceiro](./media/sprinklr-tutorial/ic782908.png "gerir parceiro")

1. Clique em **+ adicionar inícios de sessão único**.

    ![Único inícios de sessão](./media/sprinklr-tutorial/ic782909.png "único inícios de sessão")

1. Sobre o **início de sessão único** página, execute os seguintes passos:

    ![Único inícios de sessão](./media/sprinklr-tutorial/ic782910.png "único inícios de sessão")

    a. Na **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: *WAADSSOTest*).

    b. Selecione **ativada**.

    c. Selecione **utilizar o novo certificado SSO**.

    d. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.

    e. Colar o **do Azure AD identificador** valor que copiou do Portal do Azure para o **Id de entidade** caixa de texto.

    f. Colar o **URL de início de sessão** valor que copiou do Portal do Azure para o **URL de início de sessão do fornecedor de identidade** caixa de texto.

    g. Colar o **URL de fim de sessão** valor que copiou do Portal do Azure para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.

    h. Como **tipo de ID de utilizador de SAML**, selecione **asserção contém o nome de utilizador do utilizador sprinklr.com**.

    i. Como **localização do ID de utilizador de SAML**, selecione **ID de utilizador é no elemento de identificador de nome da declaração de assunto**.

    j. Clique em **Guardar**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Sprinklr.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Sprinklr**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Sprinklr**.

    ![A ligação de Sprinklr na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sprinklr-test-user"></a>Criar utilizador de teste Sprinklr

1. Inicie sessão no site da sua empresa Sprinklr como administrador.

1. Aceda a **Administration \> definições**.

    ![Administração](./media/sprinklr-tutorial/ic782907.png "administração")

1. Aceda a **gerir o cliente \> utilizadores** no painel à esquerda.

    ![As definições](./media/sprinklr-tutorial/ic782914.png "definições")

1. Clique em **adicionar utilizador**.

    ![As definições](./media/sprinklr-tutorial/ic782915.png "definições")

1. Sobre o **Editar utilizador** caixa de diálogo, execute os seguintes passos:

    ![Editar utilizador](./media/sprinklr-tutorial/ic782916.png "Editar utilizador")

    a. Na **E-Mail**, **FirstName** e **Apelido** caixas de texto, escreva as informações de uma conta de utilizador do Azure AD que pretende aprovisionar.

    b. Selecione **palavra-passe desativadas**.

    c. Selecione **linguagem**.

    d. Selecione **tipo de utilizador**.

    e. Clique em **Atualizar**.

    > [!IMPORTANT]
    > **Palavra-passe desativadas** tem de ser selecionado para permitir que um utilizador iniciar sessão através de um fornecedor de identidade. 

1. Aceda a **função**e, em seguida, execute os seguintes passos:

    ![Funções de parceiros](./media/sprinklr-tutorial/ic782917.png "funções de parceiros")

    a. Partir do **Global** lista, selecione **ALL_Permissions**.  

    b. Clique em **Atualizar**.

> [!NOTE]
> Pode utilizar quaisquer outras Sprinklr utilizador conta criação ferramentas ou APIs fornecidas pelo Sprinklr para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Sprinklr no painel de acesso, deve ser automaticamente sessão iniciada no Sprinklr para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
