---
title: 'Tutorial: Integração do Active Directory do Azure com o Insights adaptável | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Insights adaptável.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884885"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Tutorial: Integração do Active Directory do Azure com o Insights adaptável

Neste tutorial, saiba como integrar o Insights adaptáveis com o Azure Active Directory (Azure AD).

Integrar o Insights adaptáveis com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso às informações adaptável.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Insights adaptável (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Insights adaptável, terá dos seguintes itens:

- Uma subscrição do Azure
- Um estímulo Insights logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Insights adaptável da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adicionando Insights adaptável da Galeria
Para configurar a integração de informações adaptáveis com o Azure AD, terá de adicionar informações adaptável a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar informações adaptável a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. Na caixa de pesquisa, escreva **Insights adaptável**, selecione **Insights adaptável** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Insights adaptáveis com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no Insights adaptável a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Insights adaptável deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Insights adaptável, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste de Insights adaptável](#create-an-adaptive-insights-test-user)**  - para ter um equivalente da Eduarda Almeida no Insights adaptável, que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo adaptável Insights.

**Para configurar o Azure AD início de sessão único com o Insights adaptável, execute os seguintes passos:**

1. Na [portal do Azure](https://portal.azure.com/), na **Insights adaptável** página de integração de aplicações, selecione **início de sessão único**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML** modo para ativar o início de sessão único.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos, se desejar configurar a aplicação na **IDP** intiated modo:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Pode obter o identificador (ID de entidade) e valores de URL de resposta do insights adaptável **as definições de SSO SAML** página.
 
5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** e guarde-o no seu computador.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Sobre o **configurar Insights adaptável** secção, copie o URL adequado, de acordo com seus requisitos.

    Tenha em atenção que o URL pode dizer o seguinte:

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Insights adaptável como administrador.

8. Aceda a **administrador**.

    ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

9. Na **utilizadores e funções** secção, clique em **gerir as definições de SSO SAML**.

    ![Gerir as definições de SAML SSO](./media/adaptivesuite-tutorial/IC805645.png "gerir as definições de SAML SSO")

10. Sobre o **as definições de SSO SAML** página, execute os seguintes passos:

    ![As definições de SAML SSO](./media/adaptivesuite-tutorial/IC805646.png "as definições de SAML SSO")

    a. Na **nome do fornecedor de identidade** caixa de texto, escreva um nome para a sua configuração.

    b. Colar o **identificador do Azure Ad** valor copiados a partir do portal do Azure para o **ID de entidade do fornecedor de identidade** caixa de texto.

    c. Colar o **URL de início de sessão** valor copiados a partir do portal do Azure para o **URL de SSO de fornecedor de identidade** caixa de texto.

    d. Colar o **URL de fim de sessão** valor copiados a partir do portal do Azure para o **URL de fim de sessão personalizada** caixa de texto.

    e. Para carregar o certificado transferido, clique em **Escolher ficheiro**.

    f. Selecione o seguinte, para:

    * **Id de utilizador SAML**, selecione **nome de utilizador do utilizador Insights adaptável**.

    * **Localização do id de utilizador SAML**, selecione **id de utilizador no NameID de requerente**.

    * **Formato NameID de SAML**, selecione **endereço de E-Mail**.

    * **Ativar SAML**, selecione **permitir que o SAML SSO e início de sessão direto Insights adaptável**.

    g. Cópia **URL de informações do SSO adaptável** e cole o **identificador (ID de entidade)** e **URL de resposta** caixas de texto no **adaptável de informações de domínio e URLs** secção no portal do Azure.

    h. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Criar um utilizador de teste de Insights adaptável

Para ativar a utilizadores do Azure AD iniciar sessão no Insights adaptável, tem de ser aprovisionados em informações adaptável. No caso de Insights adaptável, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:** 

1. Inicie sessão no seu **Insights adaptável** site da empresa como administrador.
2. Aceda a **administrador**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. Na **utilizadores e funções** secção, clique em **adicionar utilizador**.

   ![Adicionar utilizador](./media/adaptivesuite-tutorial/IC805648.png "adicionar utilizador")
   
4. Na **novo utilizador** secção, execute os seguintes passos:

   ![Submeter](./media/adaptivesuite-tutorial/IC805649.png "submeter")

   a. Tipo de **nome**, **início de sessão**, **E-Mail**, **palavra-passe** de um utilizador válido do Azure Active Directory que pretende aprovisionar no relacionados caixas de texto.

   b. Selecione um **função**.

   c. Clique em **Submit** (Submeter).

>[!NOTE]
>Pode utilizar quaisquer outras informações adaptável utilizador conta criação ferramentas ou APIs fornecidas pelo Insights adaptável para aprovisionar contas de utilizador do AAD.
>

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso às informações adaptável.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. Na lista de aplicações, selecione **Insights adaptável**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Selecione o **Add** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

5. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Insights adaptável no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de Insights adaptável.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
