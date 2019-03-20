---
title: 'Tutorial: Integração do Active Directory do Azure com a área de trabalho Autotask | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Autotask à área de trabalho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00e838dd3df417bd0b26167d5f316225520e7263
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889595"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Tutorial: Integração do Active Directory do Azure com Autotask à área de trabalho

Neste tutorial, saiba como integrar Autotask à área de trabalho com o Azure Active Directory (Azure AD).
Integrar Autotask à área de trabalho no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à área de trabalho Autotask.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a área de trabalho Autotask (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Autotask à área de trabalho, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Área de trabalho Autotask logon único habilitado subscrição
* Um área de trabalho Autotask início de sessão único na subscrição ativado
* Tem de ser um administrador ou administrador super na área de trabalho.
* Tem de ter uma conta de administrador no Azure AD.
* Os utilizadores que passarão a utilizar esta funcionalidade tem de ter contas no local de trabalho e o Azure AD e respetivos endereços de e-mail para ambos têm de corresponder.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a área de trabalho Autotask **SP e IDP** iniciada SSO

## <a name="adding-autotask-workplace-from-the-gallery"></a>Adicionar Autotask à área de trabalho a partir da Galeria

Para configurar a integração do Autotask à área de trabalho para o Azure AD, terá de adicionar Autotask à área de trabalho a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Autotask à área de trabalho a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **à área de trabalho Autotask**, selecione **à área de trabalho Autotask** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Autotask à área de trabalho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Autotask à área de trabalho com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na área de trabalho Autotask deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Autotask à área de trabalho, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Autotask à área de trabalho início de sessão único](#configure-autotask-workplace-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de área de trabalho Autotask](#create-autotask-workplace-test-user)**  - para ter um equivalente da Eduarda Almeida na área de trabalho de Autotask que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Autotask à área de trabalho, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **à área de trabalho Autotask** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Autotask à área de trabalho domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Autotask à área de trabalho domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de área de trabalho de Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Na **configurar a área de trabalho Autotask** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-autotask-workplace-single-sign-on"></a>Configurar área de trabalho Autotask início de sessão único

1. Numa janela do browser web diferente, inicie sessão à área de trabalho Online com as credenciais de administrador.

    > [!Note]
    > Ao configurar o IdP, um subdomínio terá de ser especificado. Para confirmar o subdomínio correto, o início de sessão para o local de trabalho Online. Depois de iniciar sessão, tome nota para o subdomínio no URL. O subdomínio é a parte entre o "https://" e ".awp.autotask.net/" e deve ser EUA, UE, AC ou au.

2. Aceda a **Configuration** > **Single Sign-On** e execute os seguintes passos:

    ![Configuração do início de sessão único de Autotask](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Selecione o **o ficheiro de metadados XML** opção e, em seguida, carregue o transferido **XML de metadados de Federação** partir do portal do Azure.

    b. Clique em **HABILITAR SSO**.

    ![Autotask Single Sign-on aprovar configuração](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Selecione o **confirmo estas informações estão corretas e devo confiar este IdP** caixa de verificação.

    d. Clique em **aprovar**.

> [!Note]
> Se precisar de ajuda com a configuração Autotask à área de trabalho, consulte [esta página](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter ajuda com sua conta da área de trabalho.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à área de trabalho Autotask.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **à área de trabalho Autotask**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **à área de trabalho Autotask**.

    ![A ligação de área de trabalho Autotask na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-autotask-workplace-test-user"></a>Criar utilizador de teste Autotask à área de trabalho

Nesta secção, vai criar um usuário chamado Eduarda Almeida na área de trabalho Autotask. Trabalhe em conjunto com [equipa de suporte à área de trabalho Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para adicionar os utilizadores na plataforma Autotask à área de trabalho.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Autotask à área de trabalho no painel de acesso, deve ser automaticamente conectado à área de trabalho Autotask para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
