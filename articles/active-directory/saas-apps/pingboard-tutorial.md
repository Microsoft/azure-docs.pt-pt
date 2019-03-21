---
title: 'Tutorial: Integração do Active Directory do Azure com Pingboard | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a26418a5159781eab96cf7730d669ccb65742d86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861490"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integração do Active Directory do Azure com Pingboard

Neste tutorial, saiba como integrar Pingboard com o Azure Active Directory (Azure AD).

Integrar Pingboard no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Pingboard
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Pingboard (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Pingboard, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Pingboard logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Pingboard da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-pingboard-from-the-gallery"></a>Adicionando Pingboard da Galeria
Para configurar a integração do Pingboard com o Azure AD, terá de adicionar Pingboard a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Pingboard a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações empresariais][2]

1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Pingboard**, selecione **Pingboard** do painel de resultados e clique em **Add** botão para adicionar a aplicação.

    ![Pingboard na lista de resultados](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Pingboard com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Pingboard a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Pingboard deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no Pingboard.

Para configurar e testar o Azure AD início de sessão único com Pingboard, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Pingboard](#create-a-pingboard-test-user)**  - para ter um equivalente da Eduarda Almeida na Pingboard que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Pingboard.

**Para configurar o Azure AD início de sessão único com Pingboard, execute os seguintes passos:**

1. No portal do Azure, sobre o **Pingboard** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1.  Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. Sobre o **Pingboard domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Pingboard domínio e URLs únicas início de sessão informações IDP](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. Na **identificador** caixa de texto, digite o valor como: `http://app.pingboard.com/sp`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<entity-id>.pingboard.com/auth/saml/consume`

1. Verifique **Mostrar definições de URL avançadas**, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Pingboard domínio e URLs únicas início de sessão informações SP](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     Na **URL de início de sessão** caixa de texto, digite a URL usando o seguinte padrão: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Tenha em atenção que estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Contacte [equipa de suporte de cliente Pingboard](https://support.pingboard.com/) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML em seu computador.

    ![Xml de metadados de Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/pingboard-tutorial/tutorial_general_400.png)

1. Para configurar o SSO no lado de Pingboard, abra uma nova janela do browser e inicie sessão na sua conta de Pingboard. Tem de ser um administrador de Pingboard para configurar o início de sessão único.

1. No menu superior, selecione **aplicações > integrações**

    ![Configurar o início de sessão único](./media/pingboard-tutorial/Pingboard_integration.png)

1. Sobre o **integrações** página, encontre o **"Azure Active Directory"** mosaico e clique no mesmo.

    ![Integração de início de sessão único de Pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

1. No modal que se segue clique **"Configurar"**

    ![Botão de configuração de Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

1. Na página seguinte, repare que "integração de SSO do Azure está ativada". Abra o arquivo XML de metadados baixado num bloco de notas e cole o conteúdo **metadados de IDP**.

    ![Ecrã de configuração de Pingboard SSO](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. O ficheiro é validado, e se tudo estiver correto, início de sessão único irá agora ser ativada.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/pingboard-tutorial/create_aaduser_02.png)

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.

    ![Botão Adicionar](./media/pingboard-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/pingboard-tutorial/create_aaduser_04.png)

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.

### <a name="create-a-pingboard-test-user"></a>Criar um utilizador de teste Pingboard

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Pingboard. Pingboard suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](pingboard-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Pingboard como administrador.

1. Clique em **"Adicionar Employee"** botão **Directory** página.

    ![Adicionar o funcionário](./media/pingboard-tutorial/create_testuser_add.png)

1. Sobre o **"Adicionar Employee"** caixa de diálogo página, execute os seguintes passos:

    ![Convidar pessoas](./media/pingboard-tutorial/create_testuser_name.png)

    a. Na **FullName** caixa de texto, como o tipo o nome completo do utilizador **Eduarda Almeida**.

    b. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador **brittasimon\@contoso.com**.

    c. Na **cargo** caixa de texto, escreva o cargo da Eduarda Almeida.

    d. Na **localização** lista pendente, selecione a localização da Eduarda Almeida.

    e. Clique em **Adicionar**.

1. Um ecrã de confirmação é exibido para confirmar a adição do utilizador.

    ![Confirmar](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Pingboard.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Pingboard, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Pingboard**.

    ![A ligação de Pingboard na lista de aplicações](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

Quando clica no mosaico Pingboard no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Pingboard.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o aprovisionamento do utilizador](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png
