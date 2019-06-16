---
title: 'Tutorial: Integração do Active Directory do Azure com FirmPlay - defesa do funcionário para Recruiting | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e FirmPlay - defesa do funcionário para Recruiting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2bdc3a13582f079cc9325ef1c6949c3ae10138eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102529"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Integração do Active Directory do Azure com FirmPlay - defesa do funcionário para Recruiting

Neste tutorial, saiba como integrar FirmPlay - defesa do funcionário para Recruiting com o Azure Active Directory (Azure AD).
Integrar FirmPlay - defesa do funcionário para Recruiting com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao FirmPlay - defesa do funcionário para Recruiting.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para FirmPlay - defesa do funcionário para Recruiting (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FirmPlay - defesa do funcionário para Recruiting, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* FirmPlay - defesa do funcionário para Recruiting início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a FirmPlay - defesa do funcionário para Recruiting **SP** iniciada SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Adicionar FirmPlay - defesa do funcionário para Recruiting a partir da Galeria

Para configurar a integração do FirmPlay - defesa do funcionário para Recruiting com o Azure AD, terá de adicionar FirmPlay - defesa do funcionário para Recruiting partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar FirmPlay - defesa do funcionário para Recruiting a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **FirmPlay - defesa do funcionário para Recruiting**, selecione **FirmPlay - defesa do funcionário para Recruiting** no painel de resultados, em seguida, clique em **Add** botão para Adicione a aplicação.

     ![FirmPlay - defesa do funcionário para Recruiting na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com FirmPlay - defesa do funcionário para Recruiting com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para o trabalho, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no FirmPlay - defesa do funcionário para Recruiting deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com FirmPlay - defesa do funcionário para Recruiting, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar FirmPlay - defesa de funcionário de recrutamento início de sessão único](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar FirmPlay - defesa do funcionário para o utilizador de teste Recruiting](#create-firmplay---employee-advocacy-for-recruiting-test-user)**  - para ter um equivalente da Eduarda Almeida na FirmPlay - defesa de funcionário de recrutamento que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com FirmPlay - defesa do funcionário para Recruiting, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **FirmPlay - defesa do funcionário para Recruiting** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![FirmPlay - defesa do funcionário para o domínio de recrutamento e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [FirmPlay - defesa de funcionários para a equipa de suporte do cliente de recrutamento](mailto:engineering@firmplay.com) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar FirmPlay - defesa do funcionário para Recruiting** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Configurar FirmPlay - defesa de funcionário de recrutamento início de sessão único

Para configurar o início de sessão único num **FirmPlay - defesa do funcionário para Recruiting** lado, terá de enviar o transferido **certificado (Base64)** e copiados URLs partir do portal do Azure para [ FirmPlay - defesa de funcionários para a equipa de suporte Recruiting](mailto:engineering@firmplay.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para FirmPlay - defesa do funcionário para Recruiting.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **FirmPlay - defesa do funcionário para Recruiting**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **FirmPlay - defesa do funcionário para Recruiting**.

    ![FirmPlay - defesa do funcionário para ligação de Recruiting na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Criar FirmPlay - defesa do funcionário para o utilizador de teste Recruiting

Nesta secção, vai criar um usuário chamado Eduarda Almeida no FirmPlay - defesa do funcionário para Recruiting. Trabalhar com [FirmPlay - defesa de funcionários para a equipa de suporte Recruiting](mailto:engineering@firmplay.com) para adicionar os utilizadores no FirmPlay - defesa de funcionários para a plataforma de Recruiting. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em FirmPlay - defesa do funcionário para Recruiting mosaico no painel de acesso, deve ter automaticamente sessão iniciada no FirmPlay - defesa do funcionário para Recruiting para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

