---
title: 'Tutorial: Integração do Active Directory do Azure com Reviewsnap | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Reviewsnap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b00fb373-2b31-4dcf-84ce-abc29e4c639c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f4983f2ba1567241a6dfc6231a4925805947cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878439"
---
# <a name="tutorial-azure-active-directory-integration-with-reviewsnap"></a>Tutorial: Integração do Active Directory do Azure com Reviewsnap

Neste tutorial, saiba como integrar Reviewsnap com o Azure Active Directory (Azure AD).

Integrar Reviewsnap no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Reviewsnap.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Reviewsnap (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Reviewsnap, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Reviewsnap logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Reviewsnap da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-reviewsnap-from-the-gallery"></a>Adicionando Reviewsnap da Galeria
Para configurar a integração do Reviewsnap com o Azure AD, terá de adicionar Reviewsnap a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Reviewsnap a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Reviewsnap**, selecione **Reviewsnap** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Reviewsnap na lista de resultados](./media/reviewsnap-tutorial/tutorial_reviewsnap_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Reviewsnap com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Reviewsnap a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Reviewsnap deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Reviewsnap, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Reviewsnap](#create-a-reviewsnap-test-user)**  - para ter um equivalente da Eduarda Almeida na Reviewsnap que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Reviewsnap.

**Para configurar o Azure AD início de sessão único com Reviewsnap, execute os seguintes passos:**

1. No portal do Azure, sobre o **Reviewsnap** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/reviewsnap-tutorial/tutorial_reviewsnap_samlbase.png)

1. Sobre o **Reviewsnap domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Reviewsnap domínio e URLs únicas início de sessão em informações](./media/reviewsnap-tutorial/tutorial_reviewsnap_url.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://app.reviewsnap.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://app.reviewsnap.com/auth/saml/callback?namespace=<CUSTOMER_NAMESPACE>`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Reviewsnap domínio e URLs únicas início de sessão em informações](./media/reviewsnap-tutorial/tutorial_reviewsnap_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL: `https://app.reviewsnap.com/login`
     
    > [!NOTE] 
    > O valor de URL de resposta não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente Reviewsnap](mailto:support@reviewsnap.com) para obter o valor. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/reviewsnap-tutorial/tutorial_reviewsnap_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/reviewsnap-tutorial/tutorial_general_400.png)
    
1. Sobre o **Reviewsnap configuração** secção, clique em **configurar Reviewsnap** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Reviewsnap](./media/reviewsnap-tutorial/tutorial_reviewsnap_configure.png) 

1. Para configurar o início de sessão único num **Reviewsnap** lado, terá de enviar o transferido **certificado (Base64), o URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** para [Reviewsnap equipa de suporte](mailto:support@reviewsnap.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/reviewsnap-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/reviewsnap-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/reviewsnap-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/reviewsnap-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-reviewsnap-test-user"></a>Criar um utilizador de teste Reviewsnap

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Reviewsnap. Trabalhar com [equipa de suporte de Reviewsnap](mailto:support@reviewsnap.com) para adicionar os utilizadores na plataforma Reviewsnap. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Reviewsnap.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Reviewsnap, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Reviewsnap**.

    ![A ligação de Reviewsnap na lista de aplicações](./media/reviewsnap-tutorial/tutorial_reviewsnap_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Reviewsnap no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Reviewsnap.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/reviewsnap-tutorial/tutorial_general_01.png
[2]: ./media/reviewsnap-tutorial/tutorial_general_02.png
[3]: ./media/reviewsnap-tutorial/tutorial_general_03.png
[4]: ./media/reviewsnap-tutorial/tutorial_general_04.png

[100]: ./media/reviewsnap-tutorial/tutorial_general_100.png

[200]: ./media/reviewsnap-tutorial/tutorial_general_200.png
[201]: ./media/reviewsnap-tutorial/tutorial_general_201.png
[202]: ./media/reviewsnap-tutorial/tutorial_general_202.png
[203]: ./media/reviewsnap-tutorial/tutorial_general_203.png

