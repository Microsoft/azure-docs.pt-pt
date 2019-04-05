---
title: 'Tutorial: Integração do Active Directory do Azure com eDigitalResearch | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e eDigitalResearch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: c6b66ea0-16ba-45b4-b550-e81c56262b1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d81797f0d1e7f72842c3a5a127756b99bdc93992
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047811"
---
# <a name="tutorial-azure-active-directory-integration-with-edigitalresearch"></a>Tutorial: Integração do Active Directory do Azure com eDigitalResearch

Neste tutorial, saiba como integrar eDigitalResearch com o Azure Active Directory (Azure AD).

Integrar eDigitalResearch no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao eDigitalResearch.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para eDigitalResearch (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com eDigitalResearch, terá dos seguintes itens:

- Uma subscrição do Azure
- Um eDigitalResearch logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando eDigitalResearch da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-edigitalresearch-from-the-gallery"></a>Adicionando eDigitalResearch da Galeria
Para configurar a integração do eDigitalResearch com o Azure AD, terá de adicionar eDigitalResearch a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar eDigitalResearch a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **eDigitalResearch**, selecione **eDigitalResearch** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![eDigitalResearch na lista de resultados](./media/edigitalresearch-tutorial/tutorial_edigitalresearch_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com eDigitalResearch com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no eDigitalResearch a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no eDigitalResearch deve ser estabelecido.

EDigitalResearch, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com eDigitalResearch, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste eDigitalResearch](#create-an-edigitalresearch-test-user)**  - para ter um equivalente da Eduarda Almeida na eDigitalResearch que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo eDigitalResearch.

**Para configurar o Azure AD início de sessão único com eDigitalResearch, execute os seguintes passos:**

1. No portal do Azure, sobre o **eDigitalResearch** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/edigitalresearch-tutorial/tutorial_edigitalresearch_samlbase.png)

1. Sobre o **eDigitalResearch domínio e URLs** secção, execute os seguintes passos:

    ![eDigitalResearch domínio e URLs únicas início de sessão em informações](./media/edigitalresearch-tutorial/tutorial_edigitalresearch_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<company-name>.edigitalresearch.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<company-name>.edigitalresearch.com/login/consume`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de eDigitalResearch](https://www.maruedr.com/contact) obter esses valores.
 


1. Sobre o **certificado de assinatura SAML** secção, clique em **Base(64) certificado** e, em seguida, guarde o ficheiro de certificado no seu computador.

    !![O link de download de certificado](./media/edigitalresearch-tutorial/tutorial_edigitalresearch_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/edigitalresearch-tutorial/tutorial_general_400.png)

1. Sobre o **eDigitalResearch Configuration** secção, clique em **configurar eDigitalResearch** para abrir **configurar início de sessão** janela. Copiar o **URL de fim de sessão, o ID de entidade de SAML** partir o **secção de referência rápida.**

    ![eDigitalResearch configuração](./media/edigitalresearch-tutorial/tutorial_edigitalresearch_configure.png) 

1. Para configurar o início de sessão único num **eDigitalResearch** lado, terá de enviar o transferido **ficheiro de certificado (Base64)**, **ID de entidade de SAML**, e **fim de sessão URL** para [equipa de suporte de eDigitalResearch](https://www.maruedr.com/contact). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/edigitalresearch-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/edigitalresearch-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/edigitalresearch-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/edigitalresearch-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-an-edigitalresearch-test-user"></a>Criar um utilizador de teste eDigitalResearch

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no eDigitalResearch. 

Trabalhar com o [equipa de suporte de eDigitalResearch](https://www.maruedr.com/contact) de pôr os utilizadores que criou.        
    
 > [!NOTE]
 > O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a eDigitalResearch.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a eDigitalResearch, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **eDigitalResearch**.

    ![A ligação de eDigitalResearch na lista de aplicações](./media/edigitalresearch-tutorial/tutorial_edigitalresearch_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico eDigitalResearch no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo eDigitalResearch.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/edigitalresearch-tutorial/tutorial_general_01.png
[2]: ./media/edigitalresearch-tutorial/tutorial_general_02.png
[3]: ./media/edigitalresearch-tutorial/tutorial_general_03.png
[4]: ./media/edigitalresearch-tutorial/tutorial_general_04.png

[100]: ./media/edigitalresearch-tutorial/tutorial_general_100.png

[200]: ./media/edigitalresearch-tutorial/tutorial_general_200.png
[201]: ./media/edigitalresearch-tutorial/tutorial_general_201.png
[202]: ./media/edigitalresearch-tutorial/tutorial_general_202.png
[203]: ./media/edigitalresearch-tutorial/tutorial_general_203.png

