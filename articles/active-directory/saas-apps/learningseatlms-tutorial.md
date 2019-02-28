---
title: 'Tutorial: Integração do Active Directory do Azure com aprendizagem posto de trabalho LMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e aprendizagem postos de trabalho de LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bb056fcf-4135-478e-85b1-5015d1f07b85
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3051dc44890c14c2bb94c16a0b6de67ff3302521
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985092"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-seat-lms"></a>Tutorial: Integração do Active Directory do Azure com a aprendizagem de LMS posto de trabalho

Neste tutorial, saiba como integrar a aprendizagem de LMS posto de trabalho com o Azure Active Directory (Azure AD).
A integração de aprendizagem de LMS posto de trabalho com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Learning postos de trabalho de LMS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para aprendizagem posto de trabalho LMS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com aprendizagem posto de trabalho LMS, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Aprendizagem LMS postos de trabalho de início de sessão único ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a aprendizagem posto de trabalho LMS **SP e IDP** iniciada SSO

## <a name="adding-learning-seat-lms-from-the-gallery"></a>Adicionando LMS posto de trabalho de aprendizagem da Galeria

Para configurar a integração de LMS posto de trabalho de aprendizagem para o Azure AD, terá de adicionar LMS posto de trabalho de Learning a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar LMS posto de trabalho de aprendizagem da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Learning Seat LMS**, selecione **Learning Seat LMS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Aprendizagem LMS posto de trabalho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com LMS posto de trabalho de Aprendizado com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na aprendizagem posto de trabalho LMS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com aprendizagem posto de trabalho LMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Learning Seat LMS início de sessão único](#configure-learning-seat-lms-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de aprendizagem posto de trabalho LMS](#create-learning-seat-lms-test-user)**  - para ter um equivalente da Eduarda Almeida na aprendizagem LMS de postos de trabalho que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com aprendizagem posto de trabalho LMS, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Learning Seat LMS** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Domínio de LMS posto de trabalho e URLs únicas início de sessão em informações de aprendizagem](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.learningseatlms.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.learningseatlms.com/Account/AssertionConsumerService`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Domínio de LMS posto de trabalho e URLs únicas início de sessão em informações de aprendizagem](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<subdomain>.learningseatlms.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de LMS posto de trabalho de aprendizagem](http://help.learningseatlms.com/help) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar LMS posto de trabalho de aprendizagem** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-learning-seat-lms-single-sign-on"></a>Configurar a aprendizagem posto de trabalho LMS início de sessão único

Para configurar o início de sessão único em **Learning Seat LMS** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [LMS posto de trabalho de aprendizagem equipa de suporte](http://help.learningseatlms.com/help). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à aprendizagem postos de trabalho de LMS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Learning Seat LMS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Learning Seat LMS**.

    ![A ligação de LMS posto de trabalho de Aprendizado na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-learning-seat-lms-test-user"></a>Criar utilizador de teste de LMS posto de trabalho de aprendizagem

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Learning postos de trabalho de LMS. Trabalhar com [equipa de suporte de aprendizagem posto de trabalho LMS](http://help.learningseatlms.com/help) para adicionar os utilizadores da plataforma de aprendizagem posto de trabalho LMS. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de aprendizagem de LMS posto de trabalho no painel de acesso, deve ser automaticamente conectado para a aprendizagem de LMS de posto de trabalho para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)