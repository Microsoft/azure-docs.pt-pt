---
title: 'Tutorial: Integração do Active Directory do Azure com os laboratórios molho - Mobile e o teste da Web | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o ingrediente laboratórios - Mobile e o teste da Web.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99f794c4eb9db78f50f63f14ccfad08c65ddb07
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442061"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutorial: Integração do Active Directory do Azure com os laboratórios molho - Mobile e o teste da Web

Neste tutorial, saiba como integrar os laboratórios molho - Mobile e o teste Web com o Azure Active Directory (Azure AD).

Integrar o ingrediente laboratórios - Mobile e o teste da Web com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso aos laboratórios molho - Mobile e o teste da Web.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para laboratórios de molho - Mobile e o teste da Web (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com laboratórios molho - Mobile e o teste da Web, precisa do seguinte:

- Uma subscrição do Azure
- Laboratórios um ingrediente - Mobile e o teste de início de sessão único da Web habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar molho laboratórios - Mobile e o teste da Web da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Adicionar molho laboratórios - Mobile e o teste da Web da Galeria
Para configurar a integração de laboratórios de molho - Mobile e o teste da Web no Azure AD, terá de adicionar molho laboratórios - Mobile e o teste da Web da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar molho laboratórios - Mobile e o teste da Web da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **molho laboratórios - Mobile e o teste da Web**, selecione **molho laboratórios - Mobile e o teste da Web** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Laboratórios de molho - Mobile e o teste da Web na lista de resultados](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com laboratórios molho - Mobile e o teste da Web com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no Labs molho - Mobile e o teste da Web para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos laboratórios de molho - Mobile e o teste da Web deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com laboratórios molho - Mobile e o teste da Web, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um ingrediente laboratórios - Mobile e o teste da Web de utilizador de teste](#create-a-sauce-labs---mobile-and-web-testing-test-user)**  - para ter um equivalente da Eduarda Almeida nos laboratórios de molho - Mobile e o teste da Web que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seus laboratórios molho - Mobile e o aplicativo de teste da Web.

**Para configurar o Azure AD início de sessão único com laboratórios molho - Mobile e o teste da Web, execute os seguintes passos:**

1. No portal do Azure, sobre o **molho laboratórios - Mobile e o teste da Web** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Sobre o **molho laboratórios - Mobile e o domínio de teste da Web e URLs** secção, o utilizador não tem de efetuar outros passos de como a aplicação já está pré-integrada com o Azure.

    ![Laboratórios de molho - Mobile e o domínio de teste da Web e URLs únicas início de sessão em informações](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no seus laboratórios molho - Mobile e o site de empresa como um administrador de teste da Web.

7. Clique nas **ícone de utilizador** e selecione **Team Management** separador.

    ![Configurar o início de sessão único](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Introduza o seu **nome de domínio** na caixa de texto.

    ![Configurar o início de sessão único](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Clique em **configurar** separador.

    ![Configurar o início de sessão único](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. Na **configurar o início de sessão único** secção, execute os seguintes passos.

    ![Configurar o início de sessão único](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Clique em **procurar** e carregue o ficheiro de metadados baixado do Azure AD.

    b. Selecione o **permitir o aprovisionamento de ativaram** caixa de verificação.

    c. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Criar um ingrediente laboratórios - Mobile e o teste da Web de utilizador de teste

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida nos laboratórios de molho - Mobile e o teste da Web. Ingrediente laboratórios - Mobile e teste da Web suporta o aprovisionamento just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder a laboratórios molho - Mobile e o teste da Web, se não existir ainda.
>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de laboratórios de molho - Mobile e o teste da Web](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos laboratórios molho - Mobile e o teste da Web.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida aos laboratórios molho - Mobile e o teste da Web, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **molho laboratórios - Mobile e o teste da Web**.

    ![Os laboratórios molho - Mobile e o teste da Web ligação na lista de aplicativos](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em laboratórios molho - Mobile e o teste da Web mosaico no painel de acesso, deve obter automaticamente com sessão iniciada para seus laboratórios molho - Mobile e o aplicativo de teste da Web.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png
