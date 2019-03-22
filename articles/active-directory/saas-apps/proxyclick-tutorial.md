---
title: 'Tutorial: Integração do Active Directory do Azure com Proxyclick | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5ce628716e9e8d0094f678c3d67dcfad9e24ada
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860538"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração do Active Directory do Azure com Proxyclick

Neste tutorial, saiba como integrar Proxyclick com o Azure Active Directory (Azure AD).

Integrar Proxyclick no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Proxyclick.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Proxyclick (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Proxyclick, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Proxyclick logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Proxyclick da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-proxyclick-from-the-gallery"></a>Adicionando Proxyclick da Galeria
Para configurar a integração do Proxyclick com o Azure AD, terá de adicionar Proxyclick a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Proxyclick a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Proxyclick**, selecione **Proxyclick** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Proxyclick na lista de resultados](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Proxyclick com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Proxyclick a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Proxyclick deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Proxyclick, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Proxyclick](#create-a-proxyclick-test-user)**  - para ter um equivalente da Eduarda Almeida na Proxyclick que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Proxyclick.

**Para configurar o Azure AD início de sessão único com Proxyclick, execute os seguintes passos:**

1. No portal do Azure, sobre o **Proxyclick** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. Sobre o **Proxyclick domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Proxyclick domínio e URLs únicas início de sessão em informações](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://saml.proxyclick.com/init/<companyId>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://saml.proxyclick.com/consume/<companyId>`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Proxyclick domínio e URLs únicas início de sessão em informações](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Estes valores não são reais. Atualizar estes valores com o identificador de real, a URL de resposta e o início de sessão no URL, que é explicado mais tarde no tutorial.

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/proxyclick-tutorial/tutorial_general_400.png)

1. Sobre o **Proxyclick configuração** secção, clique em **configurar Proxyclick** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Proxyclick como administrador.

1. Selecione **conta de & definições**.

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/configure1.png)

1. Desloque para baixo para o **INTEGRAÇÕES** e selecione **SAML**.

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/configure2.png)

1. Na **SAML** secção, execute os seguintes passos:

    ![Configuração de Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Cópia **URL de consumidor de SAML** valor e cole-a na **URL de resposta** caixa de texto no **Proxyclick domínio e URLs** secção no portal do Azure.

    b. Cópia **URL de redirecionamento de SSO SAML** valor e cole-a na **iniciar sessão no URL** e **identificador** caixas de texto no **Proxyclick domínio e URLs** secção no portal do Azure.

    c. Selecione **método de pedido SAML** como **redirecionamento de HTTP**.

    d. Na **emissor** caixa de texto, cole o valor de **ID de entidade de SAML** valor, que copiou do portal do Azure.

    e. Na **URL de ponto final do SAML 2.0** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** copiados a partir do portal do Azure.

    f. Abra o ficheiro de certificado transferido a partir do portal do Azure no bloco de notas e, em seguida, cole-o para o **certificado** caixa de texto.

    g. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/proxyclick-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-proxyclick-test-user"></a>Criar um utilizador de teste Proxyclick

Para ativar a utilizadores do Azure AD iniciar sessão no Proxyclick, tem de ser aprovisionados em Proxyclick. No caso de Proxyclick, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Proxyclick como administrador.

1. Clique em **colegas** da barra de navegação superior.

    ![Adicionar o funcionário](./media/proxyclick-tutorial/user1.png)

1. Clique em **adicionar colega**

    ![Adicionar o funcionário](./media/proxyclick-tutorial/user2.png)

1. Na **adicionar um colega** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/proxyclick-tutorial/user3.png)

    a. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador **brittasimon\@contoso.com**.

    b. Na **nome próprio** caixa de texto, tipo, o primeiro nome de utilizador, como a Eduarda.

    c. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon.

    d. Clique em **adicionar utilizador**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Proxyclick.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a Proxyclick, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Proxyclick**.

    ![A ligação de Proxyclick na lista de aplicações](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Proxyclick no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Proxyclick.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

