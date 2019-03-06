---
title: 'Tutorial: Integração do Active Directory do Azure com o Suite de CX previr | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e previr Suite de CX.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104caa001f773c48a708879f3f9b50b74190fc74
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449228"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Tutorial: Integração do Active Directory do Azure com o Suite de CX previr

Neste tutorial, saiba como integrar o conjunto de CX previr com o Azure Active Directory (Azure AD).

Integrar o conjunto de CX previr no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao conjunto de CX previr.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Suite de CX previr (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Suite de CX previr, terá dos seguintes itens:

- Uma subscrição do Azure
- Um conjunto de CX previr logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando previr Suite de CX da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Adicionando previr Suite de CX da Galeria
Para configurar a integração do conjunto de CX previr com o Azure AD, terá de adicionar previr Suite de CX a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar previr Suite de CX a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Suite de CX previr**, selecione **previr Suite de CX** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Previr CX Suite na lista de resultados](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Suite CX previr com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no conjunto de CX previr a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no pacote de CX previr deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Suite de CX previr, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste previr Suite de CX](#create-a-foresee-cx-suite-test-user)**  - para ter um equivalente da Eduarda Almeida no c++ /CX Suite previr que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo previr Suite de CX.

**Para configurar o Azure AD início de sessão único com o Suite de CX previr, execute os seguintes passos:**

1. No portal do Azure, sobre o **Suite de CX previr** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

1. Sobre o **previr o CX Suite domínio e URLs** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    ![Previr CX Suite domínio e URLs únicas início de sessão em informações](./media/foreseecxsuite-tutorial/upload.png)

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Previr CX Suite domínio e URLs únicas início de sessão em informações](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Após a conclusão com êxito de carregamento **ficheiro de metadados do fornecedor de serviços** a **identificador** automática de get do valor povoadas na **previr o CX Suite domínio e URLs** secção caixa de texto como mostrado abaixo:

    ![Previr CX Suite domínio e URLs únicas início de sessão em informações](./media/foreseecxsuite-tutorial/urlupload.png)

1. Se não tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    ![Previr CX Suite domínio e URLs únicas início de sessão em informações](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva o URL: `https://cxsuite.foresee.com/`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o identificador real. Contacte [equipa de suporte de cliente de Suite CX previr](mailto:support@foresee.com) para obter este valor.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Suite de CX previr** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte de conjunto de CX previr](mailto:support@foresee.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Criar um utilizador de teste previr Suite de CX

Nesta secção, vai criar um usuário chamado Eduarda Almeida no previr Suite de CX. Trabalhar com [equipa de suporte de conjunto de CX previr](mailto:support@foresee.com) para adicionar os utilizadores ou o domínio que é necessário para estar incluído na plataforma previr Suite de CX. Se o domínio for adicionado pela equipe, os utilizadores serão automaticamente aprovisionados para a plataforma de previr Suite de CX. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso ao previr Suite de CX.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida previr Suite do c++ /CX, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **Suite de CX previr**.

    ![A ligação de conjunto de CX previr na lista de aplicações](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico previr Suite de CX no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação previr Suite de CX.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

