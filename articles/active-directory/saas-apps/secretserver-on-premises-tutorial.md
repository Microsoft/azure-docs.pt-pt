---
title: 'Tutorial: Integração do Active Directory do Azure com o segredo do servidor (no local) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o segredo do servidor (no local).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9167a5ed72e6fec2ca03cc97d1d41dd6cd4aaba6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104578"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Tutorial: Integração do Active Directory do Azure com o segredo do servidor (no local)

Neste tutorial, saiba como integrar o segredo do servidor (no local) no Azure Active Directory (Azure AD).

Integrar o segredo do servidor (no local) no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao segredo do servidor (no local).
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o segredo do servidor (no local) (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com segredo do servidor (no local), terá dos seguintes itens:

- Uma subscrição do Azure
- Um segredo de servidor (no local) início de sessão único de subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o segredo do servidor (no local) da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Adicionando o segredo do servidor (no local) da Galeria
Para configurar a integração do segredo do servidor (no local) para o Azure AD, terá de adicionar segredo do servidor (no local) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar segredo do servidor (no local) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **segredo do servidor (no local)**, selecione **segredo do servidor (no local)** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Servidor secreta (no local), na lista de resultados](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o segredo do servidor (no local), com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no segredo do servidor (no local) para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no segredo do servidor (no local) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o segredo do servidor (no local), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de segredo de servidor (no local)](#create-a-secret-server-on-premises-test-user)**  - para ter um equivalente da Eduarda Almeida no segredo do servidor (no local) que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de servidor de segredo (no local).

**Para configurar o Azure AD início de sessão único com o segredo do servidor (no local), execute os seguintes passos:**

1. No portal do Azure, sobre o **segredo do servidor (no local)** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. Na **segredo de servidor (no local) de domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação na **IDP** iniciada pelo modo:

    ![Secreta URLs de domínio do servidor (no local) e single informações de início de sessão](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Na **identificador** caixa de texto, introduza o utilizador escolhido o valor como um exemplo: `https://secretserveronpremises.azure`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > O ID de entidade mostrado acima é apenas um exemplo e é livre para escolher qualquer valor exclusivo que identifica a instância do servidor de segredo no Azure AD. Terá de enviar este ID de entidade ao [equipa de suporte de cliente de segredo de servidor (no local)](https://thycotic.force.com/support/s/) e eles configurá-lo no seu lado. Para obter mais detalhes, leia [este artigo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Secreta URLs de domínio do servidor (no local) e single informações de início de sessão](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Contacte [equipa de suporte de cliente de segredo de servidor (no local)](https://thycotic.force.com/support/s/) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Verifique **Mostrar definições de assinatura de certificado avançadas** e selecione **opção assinatura** como **asserção e resposta SAML de início de sessão**.

    ![Opções de assinatura](./media/secretserver-on-premises-tutorial/signing.png)

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. Sobre o **configuração do servidor de segredo (no local)** secção, clique em **configurar o segredo do servidor (no local)** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração do servidor secreta (no local)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Para configurar o início de sessão único num **segredo do servidor (no local)** lado, terá de enviar o transferido **Certificate(Base64), URL de fim de sessão, SAML único início de sessão no URL do serviço**, e **entidade de SAML ID** para [equipa de suporte do segredo de servidor (no local)](https://thycotic.force.com/support/s/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Criar um utilizador de teste de segredo de servidor (no local)

Nesta secção, vai criar um usuário chamado Eduarda Almeida no segredo do servidor (no local). Trabalhar com [equipa de suporte do segredo de servidor (no local)](https://thycotic.force.com/support/s/) para adicionar os utilizadores na plataforma do servidor de segredo (no local). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao servidor de segredo (no local).

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida ao segredo do servidor (no local), execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **segredo do servidor (no local)**.

    ![A ligação de segredo de servidor (no local) na lista de aplicações](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de segredo de servidor (no local) no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de servidor de segredo (no local).
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

