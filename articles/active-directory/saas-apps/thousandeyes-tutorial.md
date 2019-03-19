---
title: 'Tutorial: Integração do Active Directory do Azure com ThousandEyes | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d85c4dbd3cc1c3d827a3f8324e63f75eb942425
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892987"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Active Directory do Azure com ThousandEyes

Neste tutorial, saiba como integrar ThousandEyes com o Azure Active Directory (Azure AD).

Integrar ThousandEyes no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ThousandEyes
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ThousandEyes (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ThousandEyes, terá dos seguintes itens:

- Uma subscrição do Azure
- Um ThousandEyes logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ThousandEyes da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionando ThousandEyes da Galeria
Para configurar a integração do ThousandEyes com o Azure AD, terá de adicionar ThousandEyes a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ThousandEyes a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **ThousandEyes**.

    ![Criar um utilizador de teste do Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

1. No painel de resultados, selecione **ThousandEyes**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com ThousandEyes com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no ThousandEyes a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ThousandEyes deve ser estabelecido.

ThousandEyes, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com ThousandEyes, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste ThousandEyes](#creating-a-thousandeyes-test-user)**  - para ter um equivalente da Eduarda Almeida na ThousandEyes que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo ThousandEyes.

**Para configurar o Azure AD início de sessão único com ThousandEyes, execute os seguintes passos:**

1. No portal do Azure, sobre o **ThousandEyes** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

1. Sobre o **ThousandEyes domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL como: `https://app.thousandeyes.com/login/sso`

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/tutorial_general_400.png)

1. Sobre o **ThousandEyes configuração** secção, clique em **configurar ThousandEyes** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no seu **ThousandEyes** site da empresa como administrador.

1. No menu na parte superior, clique em **definições**.

    ![As definições](./media/thousandeyes-tutorial/ic790066.png "definições")

1. Clique em **conta**

    ![Conta](./media/thousandeyes-tutorial/ic790067.png "conta")

1. Clique nas **segurança e autenticação** separador.

    ![Segurança e autenticação](./media/thousandeyes-tutorial/ic790068.png "segurança e autenticação")

1. Na **configuração do início de sessão único** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/ic790069.png "configurar o início de sessão único")

    a. Selecione **ativar o início de sessão único**.

    b. Na **URL de página de início de sessão** caixa de texto, colar **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    c. Na **URL de página de fim de sessão** caixa de texto, colar **URL de fim de sessão**, que copiou do portal do Azure.

    d. **Emissor do fornecedor de identidade** caixa de texto, colar **ID da entidade de SAML**, que copiou do portal do Azure.

    e. Na **certificado de verificação**, clique em **Escolher ficheiro**em seguida, carregue o certificado que transferiu a partir do portal do Azure.

    f. Clique em **Guardar**.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.

    ![Criar um utilizador de teste do Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.

### <a name="creating-a-thousandeyes-test-user"></a>Criar um utilizador de teste ThousandEyes

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no ThousandEyes. ThousandEyes suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](thousandeyes-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa ThousandEyes como administrador.

1. Clique em **definições**.

    ![As definições](./media/thousandeyes-tutorial/IC790066.png "definições")

1. Clique em **conta**.

    ![Conta](./media/thousandeyes-tutorial/IC790067.png "conta")

1. Clique nas **contas e utilizadores** separador.

    ![Contas e utilizadores](./media/thousandeyes-tutorial/IC790073.png "contas e utilizadores")

1. Na **adicionar utilizadores e contas de** secção, execute os seguintes passos:

    ![Adicionar contas de utilizador](./media/thousandeyes-tutorial/IC790074.png "adicionar contas de utilizador")

    a. Na **Name** como a caixa de texto, escreva o nome de utilizador **Eduarda Almeida**.

    b. Na **E-Mail** caixa de texto, como o tipo de e-mail do utilizador **brittasimon\@contoso.com**.

    b. Clique em **adicionar novo utilizador à conta**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber um e-mail, incluindo uma ligação para confirmar e ativar a conta.

> [!NOTE]
> Pode utilizar quaisquer outras ThousandEyes utilizador conta criação ferramentas ou APIs fornecidas pelo ThousandEyes para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ThousandEyes.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a ThousandEyes, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **ThousandEyes**.

    ![Configurar o início de sessão único](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico ThousandEyes no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo ThousandEyes.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o aprovisionamento do utilizador](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
