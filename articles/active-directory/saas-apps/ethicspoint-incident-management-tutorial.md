---
title: 'Tutorial: Integração do Active Directory do Azure com gestão de incidentes EthicsPoint (EPIM) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e gestão de incidentes EthicsPoint (EPIM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e50cdd19510f0bf160c075c763e645120622674
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835701"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Tutorial: Integração do Active Directory do Azure com gestão de incidentes EthicsPoint (EPIM)

Neste tutorial, saiba como integrar a gestão de incidentes EthicsPoint (EPIM) com o Azure Active Directory (Azure AD).
Integrar a gestão de incidentes EthicsPoint (EPIM) no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à gestão de incidentes EthicsPoint (EPIM).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para gestão de incidentes de EthicsPoint (EPIM) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com gestão de incidentes EthicsPoint (EPIM), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Gestão de incidentes EthicsPoint (EPIM) início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Gestão de incidentes EthicsPoint (EPIM) suporta **SP** iniciada SSO

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Adicionando a gestão de incidentes EthicsPoint (EPIM) da Galeria

Para configurar a integração da gestão de incidentes EthicsPoint (EPIM) para o Azure AD, terá de adicionar EthicsPoint incidente gestão (EPIM) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar gerenciamento de incidentes EthicsPoint (EPIM) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **gestão de incidentes EthicsPoint (EPIM)**, selecione **gestão de incidentes EthicsPoint (EPIM)** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

     ![Gestão de incidentes EthicsPoint (EPIM) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a gestão de incidentes de EthicsPoint (EPIM) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na gestão de incidentes EthicsPoint (EPIM) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com gestão de incidentes EthicsPoint (EPIM), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a gestão de incidentes EthicsPoint (EPIM) início de sessão único](#configure-ethicspoint-incident-management-epim-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de gestão de incidentes EthicsPoint (EPIM)](#create-ethicspoint-incident-management-epim-test-user)**  - para ter um equivalente da Eduarda Almeida na gestão de incidentes EthicsPoint (EPIM), que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com gestão de incidentes EthicsPoint (EPIM), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **gestão de incidentes EthicsPoint (EPIM)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de gestão de incidentes EthicsPoint (EPIM) e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.navexglobal.com`|
    | `https://<companyname>.ethicspointvp.com`|

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://<companyname>.navexglobal.com/adfs/services/trust`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de gestão de incidentes EthicsPoint (EPIM)](https://www.navexglobal.com/company/contact-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **definir a gestão de incidentes EthicsPoint (EPIM)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-ethicspoint-incident-management-epim-single-sign-on"></a>Configurar o incidente EthicsPoint gestão (EPIM) início de sessão único

Para configurar o início de sessão único num **gestão de incidentes EthicsPoint (EPIM)** lado, terá de enviar o transferido **XML de metadados de Federação** e copiados URLs partir do portal do Azure para [ Equipa de suporte de gestão de incidentes EthicsPoint (EPIM)](https://www.navexglobal.com/company/contact-us). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à gestão de incidentes EthicsPoint (EPIM).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **EthicsPoint incidente gestão (EPIM)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **gestão de incidentes EthicsPoint (EPIM)**.

    ![A ligação de gestão de incidentes EthicsPoint (EPIM) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ethicspoint-incident-management-epim-test-user"></a>Criar utilizador de teste de gestão de incidentes EthicsPoint (EPIM)

Nesta secção, vai criar um usuário chamado Eduarda Almeida na gestão de incidentes EthicsPoint (EPIM). Trabalhar com [equipa de suporte de gestão de incidentes EthicsPoint (EPIM)](https://www.navexglobal.com/company/contact-us) para adicionar os utilizadores da plataforma de gestão de incidentes EthicsPoint (EPIM). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de gestão de incidentes EthicsPoint (EPIM) no painel de acesso, deve ser automaticamente conectado para o EthicsPoint incidente gestão (EPIM) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

