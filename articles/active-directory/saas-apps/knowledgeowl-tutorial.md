---
title: 'Tutorial: Integração do Active Directory do Azure com KnowledgeOwl | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c86ad6e4b11f21c648083fac35e15eec7658c1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995503"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Tutorial: Integração do Active Directory do Azure com KnowledgeOwl

Neste tutorial, saiba como integrar KnowledgeOwl com o Azure Active Directory (Azure AD).

Integrar KnowledgeOwl no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao KnowledgeOwl.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para KnowledgeOwl (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com KnowledgeOwl, terá dos seguintes itens:

- Uma subscrição do Azure
- Um KnowledgeOwl logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando KnowledgeOwl da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-knowledgeowl-from-the-gallery"></a>Adicionando KnowledgeOwl da Galeria
Para configurar a integração do KnowledgeOwl com o Azure AD, terá de adicionar KnowledgeOwl a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar KnowledgeOwl a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **KnowledgeOwl**, selecione **KnowledgeOwl** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![KnowledgeOwl na lista de resultados](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com KnowledgeOwl com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no KnowledgeOwl a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no KnowledgeOwl deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com KnowledgeOwl, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste KnowledgeOwl](#create-a-knowledgeowl-test-user)**  - para ter um equivalente da Eduarda Almeida na KnowledgeOwl que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo KnowledgeOwl.

**Para configurar o Azure AD início de sessão único com KnowledgeOwl, execute os seguintes passos:**

1. No portal do Azure, sobre o **KnowledgeOwl** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. Sobre o **KnowledgeOwl domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![KnowledgeOwl domínio e URLs únicas início de sessão em informações](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:

    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![KnowledgeOwl domínio e URLs únicas início de sessão em informações](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Estes valores não são reais. Terá de atualizar esses valor do identificador real, o URL de resposta e URL de início de sessão que é explicado mais tarde no tutorial.

1. O aplicativo KnowledgeOwl espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos da "**atributos de utilizador**" secção na página de integração de aplicações.

    ![Configurar o início de sessão único](./media/knowledgeowl-tutorial/attribute.png)

1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem anterior e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo | Espaço de nomes|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d.From a **espaço de nomes** lista, introduza o valor de espaço de nomes mostrado para essa linha.
    
    e. Clique em **OK**.

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (bruto)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. Sobre o **KnowledgeOwl configuração** secção, clique em **configurar KnowledgeOwl** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa KnowledgeOwl como um administrador.

1. Clique em **configurações** e, em seguida, selecione **segurança**.

     ![Configuração de KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Desloque-se para **SAML SSO integração** e execute os seguintes passos:
    
     ![Configuração de KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

     a. Selecione **ativar o SAML SSO**.

     b. Copiar o **ID de entidade de SP** valor e cole-o no **identificador (ID de entidade)** no **KnowledgeOwl domínio e URLs** secção no portal do Azure.

     c. Copiar o **URL de início de sessão de SP** valor e cole-o no **URL de início de sessão e o URL de resposta** caixas de texto no **KnowledgeOwl domínio e URLs** secção no portal do Azure.

     d. Na **IdP entityID** caixa de texto, colar a **ID de entidade de SAML** valor, que copiou do portal do Azure.

     e. Na **URL de início de sessão do IdP** caixa de texto, colar a **SAML único início de sessão no URL do serviço** valor, que copiou do portal do Azure.

     f. Na **URL de fim de sessão do IdP** caixa de texto, colar a **URL de fim de sessão** valor, que copiou do portal do Azure

     g. Carregar o formato de certificado transferido do portal do Azure ao clicar o **carregar o certificado de IdP**.

     h. Clique em **atributos de SAML do mapa** para mapear os atributos e execute os seguintes passos:
    
     ![Configuração de KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` para o **SSO ID** caixa de texto
    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` para o **nome de utilizador/E-Mail** caixa de texto.
    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` para o **nome próprio** caixa de texto.
    * Introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` para o **Apelido** caixa de texto.
    * Clicar em **Guardar**

      i. Clique em **Guardar** na parte inferior da página.

      ![Configuração de KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Criar um utilizador de teste KnowledgeOwl

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no KnowledgeOwl. KnowledgeOwl suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder KnowledgeOwl se não existir ainda.
>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para KnowledgeOwl.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a KnowledgeOwl, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **KnowledgeOwl**.

    ![A ligação de KnowledgeOwl na lista de aplicações](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico KnowledgeOwl no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo KnowledgeOwl.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

