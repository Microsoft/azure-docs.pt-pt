---
title: 'Tutorial: Integração do Active Directory do Azure com meus pontos principais Sub/superior a equipa do prémio | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e meus pontos principais Sub/superior a equipa do prémio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 47e19af1be6d727ca9dd215792899a369fb73694
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57402415"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Tutorial: Integração do Active Directory do Azure com meus pontos principais Sub/superior a equipa do prémio

Neste tutorial, saiba como integrar os meus pontos principais Sub/superior a equipa do prémio com o Azure Active Directory (Azure AD).
Meus pontos principais Sub/superior a equipa do prémio a integração com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao meu pontos principais Sub/superior a equipa do prémio.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para meus pontos superior Sub/superior a equipa do prémio (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com meus pontos principais Sub/superior a equipa do prémio, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Minha equipa do prémio pontos principais Sub/superior logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a minha equipe Sub/parte superior do prémio pontos principais **SP** iniciada SSO

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Adicionando My pontos principais Sub/superior a equipa do prémio da Galeria

Para configurar a integração de meus pontos principais Sub/superior a equipa do prémio para o Azure AD, terá de adicionar meus pontos principais Sub/superior a equipa do prémio a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar os meus pontos principais Sub/superior a equipa do prémio da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **meus pontos principais Sub/superior a equipa do prémio**, selecione **meus pontos principais Sub/superior a equipa do prémio** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Minha equipe do prémio pontos principais Sub/superior na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com meus pontos superior Sub/superior a equipa do prémio com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em meus pontos principais Sub/superior a equipa do prémio deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com meus pontos principais Sub/superior a equipa do prémio, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar meu prémio pontos superior Sub/superior equipe início de sessão único](#configure-my-award-points-top-sub/top-team-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de meus pontos principais Sub/superior a equipa do prémio](#create-my-award-points-top-sub/top-team-test-user)**  - para ter um equivalente da Eduarda Almeida em meus pontos principais Sub/superior a equipa do prémio que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com meus pontos principais Sub/superior a equipa do prémio, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **meus pontos principais Sub/superior a equipa do prémio** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Minhas prémio pontos de início superior/Sub equipe domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > O valor não é real. Obterá o `<Azure AD Identifier>` valor nos passos mais adiante neste tutorial.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar meus pontos principais Sub/superior a equipa do prémio** secção, copie os URLs apropriados de acordo com seus requisitos. 

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    >[!NOTE]
    >Acrescentar a copiado o valor do identificador do Azure AD com o URL em vez de início de sessão `<Azure AD Identifier>` no **configuração básica de SAML** secção no portal do Azure.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Configurar meus pontos de prémio principais Sub/superior equipe início de sessão único

Para configurar o início de sessão único num **meus pontos principais Sub/superior a equipa do prémio** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [prémio My A equipa de suporte de equipe de Sub/parte superior da parte superior de pontos](mailto:myawardpoints@biworldwide.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a meus pontos principais Sub/superior a equipa do prémio.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **meus pontos principais Sub/superior a equipa do prémio**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **meus pontos principais Sub/superior a equipa do prémio**.

    ![A ligação de meus pontos principais Sub/superior a equipa do prémio na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Criar utilizador de teste de meus pontos principais Sub/superior a equipa do prémio

Nesta secção, vai criar um usuário chamado Eduarda Almeida no meus pontos principais Sub/superior a equipa do prémio. Trabalhar com [equipa de suporte de meus pontos principais Sub/superior a equipa do prémio](mailto:myawardpoints@biworldwide.com) para adicionar os utilizadores na plataforma do meus pontos principais Sub/superior a equipa do prémio. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de meus pontos principais Sub/superior a equipa do prémio no painel de acesso, deve ser automaticamente a sessão iniciada meu pontos superior Sub/superior a equipa do prémio para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

