---
title: 'Tutorial: Integração do Active Directory do Azure com Five9 adaptador Plus (CTI, contacte o Centro de agentes) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Five9 adaptador Plus (CTI, contacte o Centro de agentes).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102457"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Tutorial: Integração do Active Directory do Azure com Five9 adaptador Plus (CTI, contacte o Centro de agentes)

Neste tutorial, saiba como integrar Five9 adaptador Plus (CTI, agentes do Centro de contacto) com o Azure Active Directory (Azure AD).
Integrar Five9 adaptador Plus (CTI, agentes do Centro de contacto) com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Five9 adaptador Plus (CTI, contacte o Centro de agentes).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Five9 adaptador Plus (CTI, contacte o Centro de agentes) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Five9 adaptador Plus (CTI, contacte o Centro de agentes), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Five9 adaptador Plus (CTI, contacte o Centro de agentes) início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Five9 adaptador Plus (CTI, contacte o Centro de agentes) suporta **IDP** iniciada SSO

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Adicionando Five9 adaptador Plus (CTI, contacte o Centro de agentes) da Galeria

Para configurar a integração do Five9 adaptador Plus (CTI, agentes do Centro de contacto) com o Azure AD, terá de adicionar Five9 placa Plus (CTI, contacte o Centro de agentes) da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Five9 adaptador Plus (CTI, contacte o Centro de agentes) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** , selecione **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Five9 Plus adaptador (CTI, contacte o Centro de agentes) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com Five9 adaptador Plus (CTI, agentes do Centro de contacto) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Five9 adaptador Plus (CTI, contacte o Centro de agentes) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Five9 adaptador Plus (CTI, contacte o Centro de agentes), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Five9 adaptador Plus (CTI, contacte o Centro de agentes) início de sessão único](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Five9 adaptador Plus (CTI, contacte o Centro de agentes)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)**  - para ter um equivalente da Eduarda Almeida na Five9 Plus adaptador (CTI, contacte o Centro de agentes) que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Five9 adaptador Plus (CTI, contacte o Centro de agentes), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![Five9 adaptador Plus (CTI, contacte o Centro de agentes) de domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    
    |    Ambiente      |       do IdP      |
    | :-- | :-- |
    | Para "Five9 Plus adaptador para o Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Para "Five9 Plus adaptador para Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Para "Five9 Plus adaptador para o Toolkit de área de trabalho do agente" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    |      Ambiente     |      do IdP      |
    | :--                  | :--           |
    | Para "Five9 Plus adaptador para o Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Para "Five9 Plus adaptador para Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Para "Five9 Plus adaptador para o Toolkit de área de trabalho do agente" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Five9 adaptador Plus (CTI, contacte o Centro de agentes)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Configurar Five9 Plus adaptador (CTI, agentes do contacto Center) início de sessão único

1. Para configurar o início de sessão único num **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** lado, terá de enviar o transferido **Certificate(Base64)** e adequadas URLs copiados para [Five9 Plus A equipa de suporte do adaptador (CTI, contacte o Centro de agentes)](https://www.five9.com/about/contact). Também, além disso, para configurar o SSO ainda mais, siga os passos, de acordo com o adaptador abaixo:

    a. "Five9 Plus adaptador para o Toolkit de área de trabalho do agente" Guia do administrador: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adaptador para o Microsoft Dynamics CRM" Guia do administrador: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adaptador para Zendesk" Guia do administrador: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a Five9 adaptador Plus (CTI, contacte o Centro de agentes).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** .

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** .

    ![A ligação de Five9 adaptador Plus (CTI, contacte o Centro de agentes) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Criar utilizador de teste Five9 adaptador Plus (CTI, contacte o Centro de agentes)

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Five9 adaptador Plus (CTI, contacte o Centro de agentes). Trabalhar com [equipa de suporte de Five9 adaptador Plus (CTI, contacte o Centro de agentes)](https://www.five9.com/about/contact) para adicionar os utilizadores na plataforma Five9 adaptador Plus (CTI, contacte o Centro de agentes). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no adaptador de Plus Five9 (CTI, contacte o Centro de agentes mosaico no painel de acesso, deve estar automaticamente conectado ao Five9 Plus adaptador (CTI, contacte o Centro de agentes) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

