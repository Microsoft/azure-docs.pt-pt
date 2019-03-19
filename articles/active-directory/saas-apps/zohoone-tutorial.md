---
title: 'Tutorial: Integração do Active Directory do Azure com um de Zoho | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Zoho um.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aadb3188969e099a709711ac18916b67c83b5da1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884443"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Active Directory do Azure com Zoho um

Neste tutorial, saiba como integrar Zoho um com o Azure Active Directory (Azure AD).

Integrar Zoho um com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Zoho um.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada uma Zoho (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zoho um, precisa do seguinte:

- Uma subscrição do Azure
- Um Zoho um início de sessão único de subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Zoho um da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-zoho-one-from-the-gallery"></a>Adicionando Zoho um da Galeria
Para configurar a integração do Zoho um com o Azure AD, terá de adicionar Zoho uma galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zoho um partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Zoho um**, selecione **Zoho uma** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Zoho um na lista de resultados](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com um Zoho com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Zoho um a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zoho um deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Zoho um, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Zoho uma](#create-a-zoho-one-test-user)**  - para ter um equivalente da Eduarda Almeida na Zoho um que esteja ligado a representação do Azure AD do utilizador.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Zoho um.

**Para configurar o Azure AD início de sessão único com Zoho um, execute os seguintes passos:**

1. No portal do Azure, sobre o **Zoho um** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. Sobre o **Zoho um domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Zoho um domínio e URLs únicas início de sessão em informações](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. Na **identificador (ID de entidade)** caixa de texto, escreva um URL: `one.zoho.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Verifique **Mostrar definições de URL avançadas**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL:`https://one.zoho.com`

1. Se desejar configurar a aplicação no **SP** iniciado do modo de executar o passo seguinte:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Precedente **URL de resposta** e **URL de início de sessão** valor não é real. Atualizar o valor com o URL de resposta real e o URL de início de sessão que é explicado mais tarde no tutorial. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. Sobre o **Zoho uma configuração** secção, clique em **configurar Zoho uma** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Zoho One Configuration](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoho um como administrador.

1. Sobre o **organização** separador, clique em **configuração** sob **autenticação SAML**.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. Na página do pop-up, execute os seguintes passos:

    ![Sig Zoho um](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Na **URL de início de sessão** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    b. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    c. Clique em **navegue** para carregar o **certificado (Base64)** que transferiu a partir do portal do Azure.

    d. Clique em **Guardar**.

1. Depois de guardar a configuração de autenticação SAML, copie os **SAML Identfier** valor e usar esse valor no **URL de resposta** no portal do Azure, em **Zoho um domínio e URLs** secção.

    ![Zoho um saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Vá para o **domínios** separador e, em seguida, clique em **Adicionar domínio**.

    ![Zoho One domain](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. Sobre o **Adicionar domínio** página, execute os seguintes passos:

    ![Zoho um Adicionar domínio](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Na **nome de domínio** caixa de texto, domínio de tipo como **contoso.com**.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar a siga de domínio [estes](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passos para verificar o seu domínio. Assim que o domínio estiver verfified, utilize o seu nome de domínio no **URL de início de sessão** na **Zoho um domínio e URLs** secção no portal do Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/zohoone-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/zohoone-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/zohoone-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-zoho-one-test-user"></a>Criar um Zoho um utilizador de teste

Para ativar a utilizadores do Azure AD iniciar sessão no Zoho um, tem de ser aprovisionados em Zoho um. Em um Zoho, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Zoho um como um administrador de segurança.

1. Sobre o **os utilizadores** separador, clique em **logótipo de utilizador**.

    ![Zoho One user](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. Sobre o **adicionar utilizador** página, execute os seguintes passos:

    ![Zoho um adicionar utilizador](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Na **Name** texto, introduza o nome de utilizador, como **Eduarda Almeida**.
    
    b. Na **endereço de E-Mail** texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    >[!Note]
    >Selecione o domínio verificado na lista de domínio.

    c. Clique em **Adicionar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso Zoho uma.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida Zoho uma, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Zoho um**.

    ![A ligação Zoho um na lista de aplicações](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico Zoho uma no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Zoho um.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

