---
title: 'Tutorial: Integração do Active Directory do Azure com a liquidação de música | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Settling música.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b7dee41b226cdacea1d9c7f1cf581d9f095977e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865242"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Tutorial: Integração do Active Directory do Azure com a liquidação de música

Neste tutorial, saiba como integrar Settling música com o Azure Active Directory (Azure AD).

Integrar Settling música no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD, quem tem acesso a liquidação de música.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a liquidação de música (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a liquidação de música, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Settling música logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Settling música da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-settling-music-from-the-gallery"></a>Adicionando Settling música da Galeria
Para configurar a integração de música Settling com o Azure AD, terá de adicionar Settling música a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Settling música a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **liquidação de música**, selecione **liquidação de música** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Liquidação de música na lista de resultados](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a liquidação de música, com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Settling música para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na liquidação de música deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Settling músicas, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de música Settling](#create-a-settling-music-test-user)**  - para ter um equivalente da Eduarda Almeida na liquidação de músicas que está ligada à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de música Settling.

**Para configurar o Azure AD início de sessão único com a liquidação de música, execute os seguintes passos:**

1. No portal do Azure, sobre o **liquidação de música** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

1. Sobre o **liquidar música domínio e URLs** secção, execute os seguintes passos:

    ![Liquidação de música e URLs de domínio únicas início de sessão em informações](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [liquidação de equipa de suporte de cliente de música](https://rakurakuseisan.jp/) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/settlingmusic-tutorial/tutorial_general_400.png)

1. No **liquidação de música Configuration** secção, clique em **configurar liquidar música** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Liquidação de música configuração](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

1. Numa janela do browser web diferente, início de sessão para se estabelecer música como um administrador de segurança.

1. Na parte superior da página, clique em **gestão** separador.

    ![Liquidação de passos de 1 de música](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Clique em **definição do sistema** separador.

    ![Liquidar step2 de música](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Mude para o **segurança** separador.

    ![Liquidar step3 de música](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. Sobre o **única definição de início de sessão** secção, execute os seguintes passos:

    ![Liquidar step5 de música](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Clique em **para ativar**.

    b. No **URL de início de sessão do fornecedor de ID** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.

    c. Na **URL de fim de sessão do fornecedor de ID** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    d. Clique em **Escolher ficheiro** para carregar o **certificado (Base64)** que transferiu formam o portal do Azure.

    e. Clique no botão **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/settlingmusic-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/settlingmusic-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/settlingmusic-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-settling-music-test-user"></a>Criar um utilizador de teste de música Settling

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Settling música. Trabalhar com [liquidação de equipa de suporte de cliente de música](https://rakurakuseisan.jp/) para adicionar os utilizadores na plataforma de música Settling. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a liquidação de música.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida Settling música, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **liquidação de música**.

    ![A ligação de música Settling na lista de aplicações](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de música Settling no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo de música Settling.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

