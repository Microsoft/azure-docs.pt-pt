---
title: 'Tutorial: Integração do Active Directory do Azure com Replicon | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ade40287bd38580a1e3f6377e54017bfe92bf452
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863411"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Tutorial: Integração do Active Directory do Azure com Replicon

Neste tutorial, saiba como integrar Replicon com o Azure Active Directory (Azure AD).

Integrar Replicon no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Replicon.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Replicon (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Replicon, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Replicon logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Replicon da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-replicon-from-the-gallery"></a>Adicionando Replicon da Galeria
Para configurar a integração do Replicon com o Azure AD, terá de adicionar Replicon a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Replicon a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Replicon**, selecione **Replicon** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Replicon na lista de resultados](./media/replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Replicon com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Replicon a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Replicon deve ser estabelecido.

Replicon, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Replicon, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Replicon](#create-a-replicon-test-user)**  - para ter um equivalente da Eduarda Almeida na Replicon que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Replicon.

**Para configurar o Azure AD início de sessão único com Replicon, execute os seguintes passos:**

1. No portal do Azure, sobre o **Replicon** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/replicon-tutorial/tutorial_replicon_samlbase.png)

3. Sobre o **Replicon domínio e URLs** secção, execute os seguintes passos:

    ![Replicon domínio e URLs únicas início de sessão em informações](./media/replicon-tutorial/tutorial_replicon_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://global.replicon.com/<companyname>`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente Replicon](https://www.replicon.com/customerzone/contact-support) obter esses valores. 

4. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/replicon-tutorial/tutorial_replicon_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/replicon-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no site da sua empresa Replicon como um administrador.

7. Para configurar SAML 2.0, execute os seguintes passos:

    ![Ativar a autenticação SAML](./media/replicon-tutorial/ic777805.png "autenticação ativar SAML")

    a. Para apresentar os **EnableSAML Authentication2** caixa de diálogo, anexe o seguinte ao seu URL, após a sua chave de acesso da empresa: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * O código a seguir mostra o esquema do URL completa: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Clique nas **+** para expandir a **v20Configuration** secção.

   c. Clique nas **+** para expandir a **metaDataConfiguration** secção.

   d. Clique em **Escolher ficheiro**para selecionar o ficheiro XML de metadados de fornecedor de identidade e clique em **submeter**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/replicon-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/replicon-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/replicon-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/replicon-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-replicon-test-user"></a>Criar um utilizador de teste Replicon

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Replicon.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Numa janela do browser web, inicie sessão no site da sua empresa Replicon como um administrador.

2. Aceda a **Administration \> utilizadores**.

    ![Os utilizadores](./media/replicon-tutorial/ic777806.png "utilizadores")

3. Clique em **+ adicionar utilizador**.

    ![Adicionar utilizador](./media/replicon-tutorial/ic777807.png "adicionar utilizador")

4. Na **perfil de utilizador** secção, execute os seguintes passos:

    ![Perfil de usuário](./media/replicon-tutorial/ic777808.png "perfil de utilizador")

    a. Na **nome de início de sessão** caixa de texto, introduzir o Azure AD o endereço de e-mail do utilizador do Azure AD que pretende aprovisionar como **BrittaSimon\@contoso.com**.

    b. Como **tipo de autenticação**, selecione **SSO**.

    c. Na **departamento** caixa de texto, digite departamento do utilizador.

    d. Como **tipo de funcionário**, selecione **administrador**.

    e. Clique em **guardar o perfil de utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras Replicon utilizador conta criação ferramentas ou APIs fornecidas pelo Replicon para aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Replicon.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a Replicon, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Replicon**.

    ![A ligação de Replicon na lista de aplicações](./media/replicon-tutorial/tutorial_replicon_app.png)

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Replicon no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Replicon.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
[4]: ./media/replicon-tutorial/tutorial_general_04.png

[100]: ./media/replicon-tutorial/tutorial_general_100.png

[200]: ./media/replicon-tutorial/tutorial_general_200.png
[201]: ./media/replicon-tutorial/tutorial_general_201.png
[202]: ./media/replicon-tutorial/tutorial_general_202.png
[203]: ./media/replicon-tutorial/tutorial_general_203.png
