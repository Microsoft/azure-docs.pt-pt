---
title: 'Tutorial: Integração do Active Directory do Azure com ScreenSteps | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3a67bb2b258d5376c997d4858bea4d094e850127
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997142"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração do Active Directory do Azure com ScreenSteps

Neste tutorial, saiba como integrar ScreenSteps com o Azure Active Directory (Azure AD).
Integrar ScreenSteps no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ScreenSteps.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para ScreenSteps (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ScreenSteps, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta ScreenSteps **SP** iniciada SSO

## <a name="adding-screensteps-from-the-gallery"></a>Adicionando ScreenSteps da Galeria

Para configurar a integração do ScreenSteps com o Azure AD, terá de adicionar ScreenSteps a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ScreenSteps a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ScreenSteps**, selecione **ScreenSteps** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ScreenSteps na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ScreenSteps com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ScreenSteps deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com ScreenSteps, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ScreenSteps Single Sign-On](#configure-screensteps-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ScreenSteps](#create-screensteps-test-user)**  - para ter um equivalente da Eduarda Almeida na ScreenSteps que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ScreenSteps, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **ScreenSteps** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![ScreenSteps domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o início de sessão no URL real, que é explicado posteriormente neste tutorial.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar ScreenSteps** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-screensteps-single-sign-on"></a>Configurar ScreenSteps Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa ScreenSteps como um administrador.

1. Clique em **definições da conta**.

    ![Gerenciamento de contas](./media/screensteps-tutorial/ic778523.png "gerenciamento de contas")

1. Clique em **início de sessão único**.

    ![Autenticação remota](./media/screensteps-tutorial/ic778524.png "autenticação remota")

1. Clique em **criar o ponto final de início de sessão único**.

    ![Autenticação remota](./media/screensteps-tutorial/ic778525.png "autenticação remota")

1. Na **ponto final de início de sessão único criar** secção, execute os seguintes passos:

    ![Criar um ponto de final de autenticação](./media/screensteps-tutorial/ic778526.png "criar um ponto de final de autenticação")

    a. Na **Title** caixa de texto, escreva um título.

    b. Partir do **modo** lista, selecione **SAML**.

    c. Clique em **Criar**.

1. **Editar** o novo ponto final.

    ![Editar o ponto final](./media/screensteps-tutorial/ic778528.png "editar o ponto final")

1. Na **ponto final de início de sessão único editar** secção, execute os seguintes passos:

    ![Ponto final de autenticação remota](./media/screensteps-tutorial/ic778527.png "ponto final de autenticação remota")

    a. Clique em **carregamento de novo o ficheiro de certificado SAML**em seguida, carregue o certificado que transferiu a partir do portal do Azure.

    b. Colar **URL de início de sessão** valor, que copiou do portal do Azure para o **URL de início de sessão remoto** caixa de texto.

    c. Colar **URL de fim de sessão** valor, que copiou do portal do Azure para o **URL para terminar sessão** caixa de texto.

    d. Selecione um **grupo** para atribuir utilizadores a quando são aprovisionados.

    e. Clique em **Atualizar**.

    f. Cópia a **URL de consumidor de SAML** para a área de transferência e colar para o **URL de início de sessão** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    g. Retorno para o **editar o ponto final de início de sessão único**.

    h. Clique nas **predefinido para a conta** botão utilizem este ponto final para todos os utilizadores que iniciem sessão em ScreenSteps. Em alternativa, pode clicar a **adicionar ao Site** botão utilizem este ponto final para sites específicos de **ScreenSteps**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ScreenSteps.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ScreenSteps**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ScreenSteps**.

    ![A ligação de ScreenSteps na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-screensteps-test-user"></a>Criar utilizador de teste ScreenSteps

Nesta secção, vai criar um usuário chamado Eduarda Almeida no ScreenSteps. Trabalhar com [equipa de suporte de cliente ScreenSteps](https://www.screensteps.com/contact) para adicionar os utilizadores na plataforma ScreenSteps. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico ScreenSteps no painel de acesso, deve ser automaticamente sessão iniciada no ScreenSteps para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)