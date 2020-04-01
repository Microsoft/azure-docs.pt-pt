---
title: 'Tutorial: Integração de Diretório Ativo Azure com Portal de Gestão de Nuvem para o Microsoft Azure Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cloud Management Portal para o Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e73a1f80e9fbca66bd58c65c45b299950315309d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161253"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutorial: Integração de Diretório Ativo Azure com Portal de Gestão de Nuvem para o Microsoft Azure

Neste tutorial, aprende-se a integrar o Portal de Gestão de Cloud para o Microsoft Azure com o Azure Ative Directory (Azure AD).
Integrar o Portal de Gestão de Nuvem para o Microsoft Azure com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Portal de Gestão de Cloud para o Microsoft Azure.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Portal de Gestão de Cloud para o Microsoft Azure (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Portal de Gestão de Nuvem para o Microsoft Azure, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Portal de Gestão de Nuvem para subscrição ativada por sinal único do Microsoft Azure

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Portal de Gestão de Cloud para o Microsoft Azure suporta **SP** iniciado

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Adicionar portal de gestão de nuvem para o Microsoft Azure a partir da galeria

Para configurar a integração do Portal de Gestão de Cloud para o Microsoft Azure em Azure AD, é necessário adicionar o Portal de Gestão de Nuvem para o Microsoft Azure da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar o Portal de Gestão de Nuvem para o Microsoft Azure a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o Portal de Gestão de **Cloud para o Microsoft Azure,** selecione Cloud Management Portal para o Microsoft **Azure** a partir do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Portal de Gestão de Nuvem para o Microsoft Azure na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on do Azure AD com o Cloud Management Portal para o Microsoft Azure com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Portal de Gestão de Cloud para o Microsoft Azure.

Para configurar e testar o único sign-on do Azure AD com o Portal de Gestão de Nuvem para o Microsoft Azure, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. Configure o Portal de Gestão de **[Nuvem para o Microsoft Azure Single Sign-On](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie](#create-cloud-management-portal-for-microsoft-azure-test-user)** o Portal de Gestão de Nuvem para o utilizador de testes do Microsoft Azure - para ter uma contrapartida da Britta Simon no Portal de Gestão de Nuvem para o Microsoft Azure que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o Single Sign-on azure com o Cloud Management Portal para o Microsoft Azure, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no Portal de Gestão de Nuvem para página de integração de aplicações **do Microsoft Azure,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Portal de Gestão de Nuvem para domínio Microsoft Azure e urLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte o Portal de [Gestão de Cloud para](mailto:jczernuszka@newsignature.com) a equipa de suporte ao Cliente Microsoft Azure para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. No Portal de Gestão de Nuvem configurado para a secção **Microsoft Azure,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>Configure Cloud Management Portal para Microsoft Azure Single Sign-On

Para configurar um único sinal no Portal de Gestão de Cloud para o lado do **Microsoft Azure,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para cloud management portal para a equipa de suporte do [Microsoft Azure](mailto:jczernuszka@newsignature.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único sign-on do Azure, concedendo acesso ao Portal de Gestão de Cloud para o Microsoft Azure.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Cloud Management Portal para microsoft Azure**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cloud Management Portal para o Microsoft Azure**.

    ![O Portal de Gestão de Nuvem para o link Microsoft Azure na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Crie portal de gestão de nuvem para o utilizador de teste Microsoft Azure

Nesta secção, cria-se um utilizador chamado Britta Simon no Cloud Management Portal para o Microsoft Azure. Trabalhe com o Portal de [Gestão de Nuvem para](mailto:jczernuszka@newsignature.com) a equipa de suporte do Microsoft Azure para adicionar os utilizadores no Portal de Gestão de Nuvem para a plataforma Microsoft Azure. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no Portal de Gestão de Nuvem para azulejos Microsoft Azure no Painel de Acesso, deverá ser automaticamente inscrito no Portal de Gestão de Nuvem para o Microsoft Azure para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

