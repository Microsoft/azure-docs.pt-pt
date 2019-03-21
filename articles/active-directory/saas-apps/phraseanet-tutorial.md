---
title: 'Tutorial: Integração do Active Directory do Azure com Phraseanet | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Phraseanet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59e60f4b89bb1d579c9a3e11ddb65b33af81b0e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848852"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Tutorial: Integração do Active Directory do Azure com Phraseanet

Neste tutorial, saiba como integrar Phraseanet com o Azure Active Directory (Azure AD).

Integrar Phraseanet no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Phraseanet.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Phraseanet (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Phraseanet, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Phraseanet logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Phraseanet da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-phraseanet-from-the-gallery"></a>Adicionando Phraseanet da Galeria
Para configurar a integração do Phraseanet com o Azure AD, terá de adicionar Phraseanet a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Phraseanet a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![image](./media/phraseanet-tutorial/selectazuread.png)

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![image](./media/phraseanet-tutorial/a_select_app.png)
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![image](./media/phraseanet-tutorial/a_new_app.png)

4. Na caixa de pesquisa, escreva **Phraseanet**, selecione **Phraseanet** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![image](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Phraseanet com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Phraseanet a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Phraseanet deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Phraseanet, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Phraseanet](#create-a-phraseanet-test-user)**  - para ter um equivalente da Eduarda Almeida na Phraseanet que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Phraseanet.

**Para configurar o Azure AD início de sessão único com Phraseanet, execute os seguintes passos:**

1. Na [portal do Azure](https://portal.azure.com/), na **Phraseanet** página de integração de aplicações, selecione **início de sessão único**.

    ![image](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![image](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![image](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    Na **URL de início de sessão** caixa de texto, escreva um URL como:  `https://<SUBDOMAIN>.alchemyasp.com`

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > O valor de URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de Phraseanet](mailto:support@alchemy.fr) para obter o valor.
 
5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  e guarde-o no seu computador.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Para configurar o início de sessão único num **Phraseanet** lado, terá de enviar o transferido **XML de metadados de Federação** para [equipa de suporte de Phraseanet](mailto:support@alchemy.fr). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![image](./media/phraseanet-tutorial/d_users_and_groups.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![image](./media/phraseanet-tutorial/d_adduser.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![image](./media/phraseanet-tutorial/d_userproperties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-a-phraseanet-test-user"></a>Criar um utilizador de teste Phraseanet

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Phraseanet. Trabalhar com [equipa de suporte de Phraseanet](mailto:support@alchemy.fr) para adicionar os utilizadores na plataforma Phraseanet. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Phraseanet.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![image](./media/phraseanet-tutorial/d_all_applications.png)

2. Na lista de aplicações, selecione **Phraseanet**.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![image](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Selecione o **Add** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![image](./media/phraseanet-tutorial/d_assign_user.png)

4. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

5. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Phraseanet no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Phraseanet.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


