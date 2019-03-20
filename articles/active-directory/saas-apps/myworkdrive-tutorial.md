---
title: 'Tutorial: Integração do Active Directory do Azure com a MyWorkDrive | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: c0557ccc995e591187aae24dfd7078755fec3e9a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885526"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Tutorial: Integração do Active Directory do Azure com a MyWorkDrive

Neste tutorial, saiba como integrar a MyWorkDrive no Azure Active Directory (Azure AD).
Integrar a MyWorkDrive no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD, quem tem acesso a MyWorkDrive.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada a MyWorkDrive (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com MyWorkDrive, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* MyWorkDrive logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a MyWorkDrive **SP** e **IDP** iniciada SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionando a MyWorkDrive da Galeria

Para configurar a integração da MyWorkDrive no Azure AD, terá de adicionar MyWorkDrive a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar MyWorkDrive a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **MyWorkDrive**, selecione **MyWorkDrive** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![MyWorkDrive na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com MyWorkDrive com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no MyWorkDrive deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com MyWorkDrive, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a MyWorkDrive Single Sign-On](#configure-myworkdrive-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste da MyWorkDrive](#create-myworkdrive-test-user)**  - para ter um equivalente da Eduarda Almeida na MyWorkDrive que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com MyWorkDrive, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **MyWorkDrive** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![MyWorkDrive domínio e URLs únicas início de sessão em informações](common/both-replyurl.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![MyWorkDrive domínio e URLs únicas início de sessão em informações](common/both-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Entrada name:e.g de anfitrião do servidor da MyWorkDrive da sua própria empresa.
    > 
    > URL de resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de início de sessão:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Contacte [equipa de suporte da MyWorkDrive](mailto:support@myworkdrive.com) se não souber como configurar o seu próprio nome de anfitrião e o certificado SSL para esses valores.

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

### <a name="configure-myworkdrive-single-sign-on"></a>Configurar a MyWorkDrive Single Sign-On

1. Numa janela do browser web diferente, início de sessão para MyWorkDrive como um administrador de segurança.

2. No servidor MyWorkDrive no painel administração, clique em **ENTERPRISE** e execute os seguintes passos:

    ![O administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Ativar **SSO SAML/ADFS**.

    b. Selecione **SAML - Azure AD**

    c. Na **Url de metadados de Federação de aplicação do Azure** caixa de texto, cole o valor de **Url de metadados de Federação de aplicação** que copiou do portal do Azure.

    d. Clicar em **Guardar**

    >[!NOTE]
    >Para informações adicionais, veja a [artigo de suporte do Azure AD de MyWorkDrive](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a MyWorkDrive.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **MyWorkDrive**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **MyWorkDrive**.

    ![A ligação da MyWorkDrive na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-myworkdrive-test-user"></a>Criar utilizador de teste da MyWorkDrive

Nesta secção, vai criar um usuário chamado Eduarda Almeida no MyWorkDrive. Trabalhar com [equipa de suporte da MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os utilizadores na plataforma da MyWorkDrive. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico da MyWorkDrive no painel de acesso, deve ser automaticamente sessão iniciada no MyWorkDrive para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

