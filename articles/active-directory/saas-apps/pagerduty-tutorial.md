---
title: 'Tutorial: Integração do Active Directory do Azure com PagerDuty | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a48b9e70fe3373b9bc87788ff11fc0a8edc7bdf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439661"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração do Active Directory do Azure com PagerDuty

Neste tutorial, saiba como integrar o PagerDuty no Azure Active Directory (Azure AD).

Integrar o PagerDuty no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao PagerDuty
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para PagerDuty (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PagerDuty, terá dos seguintes itens:

- Uma subscrição do Azure
- Um PagerDuty logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar PagerDuty a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-pagerduty-from-the-gallery"></a>Adicionar PagerDuty a partir da Galeria
Para configurar a integração do PagerDuty no Azure AD, terá de adicionar PagerDuty a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar PagerDuty a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **PagerDuty**, selecione **PagerDuty** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com PagerDuty com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no PagerDuty a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PagerDuty deve ser estabelecido.

No PagerDuty, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com PagerDuty, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do PagerDuty](#create-a-pagerduty-test-user)**  - para ter um equivalente da Eduarda Almeida no PagerDuty que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação do PagerDuty.

**Para configurar o Azure AD início de sessão único com PagerDuty, execute os seguintes passos:**

1. No portal do Azure, sobre o **PagerDuty** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

1. Sobre o **PagerDuty domínio e URLs** secção, execute os seguintes passos:

    ![PagerDuty domínio e URLs únicas início de sessão em informações](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente do PagerDuty](https://www.pagerduty.com/support/) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/pagerduty-tutorial/tutorial_general_400.png)

1. Na **configuração do PagerDuty** secção, clique em **configurar PagerDuty** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração do PagerDuty](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Pagerduty como um administrador.

1. No menu na parte superior, clique em **definições de conta**.

    ![Definições da conta](./media/pagerduty-tutorial/ic778535.png "definições da conta")

1. Clique em **início de sessão único**.

    ![Início de sessão único](./media/pagerduty-tutorial/ic778536.png "início de sessão único")

1. Sobre o **ativar o início de sessão único (SSO)** página, execute os seguintes passos:

    ![Ativar o início de sessão único](./media/pagerduty-tutorial/ic778537.png "ativar início de sessão único")

    a. Abra o seu certificado codificado de base 64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto
  
    b. Na **URL de início de sessão** caixa de texto, colar **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.
  
    c. Na **URL de fim de sessão** caixa de texto, colar **URL de fim de sessão** que copiou do portal do Azure.

    d. Selecione **início de sessão de nome de utilizador/palavra-passe de permitir**.

    e. Selecione **comparação de contexto de autenticação exigir EXATA** caixa de verificação.

    f. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/pagerduty-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/pagerduty-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/pagerduty-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-pagerduty-test-user"></a>Criar um utilizador de teste do PagerDuty

Para ativar a utilizadores do Azure AD iniciar sessão no PagerDuty, tem de ser aprovisionados no PagerDuty.  
No caso do PagerDuty, aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras Pagerduty utilizador conta criação ferramentas ou APIs fornecidas pelo Pagerduty para aprovisionar o Azure Active Directory contas de utilizador.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Pagerduty** inquilino.

1. No menu na parte superior, clique em **utilizadores**.

1. Clique em **adicionar utilizadores**.
   
    ![Adicionar utilizadores](./media/pagerduty-tutorial/ic778539.png "adicionar utilizadores")

1.  Sobre o **convidar sua equipe** caixa de diálogo, execute os seguintes passos:
   
    ![Convidar sua equipe](./media/pagerduty-tutorial/ic778540.png "convidar sua equipe")

    a. Tipo de **primeiro e último nome** do utilizador, como **Eduarda Almeida**. 
   
    b. Introduza **E-Mail** como o endereço do utilizador **brittasimon@contoso.com**.
   
    c. Clique em **Add**e, em seguida, clique em **enviar convites**.
   
    >[!NOTE]
    >Todos os utilizadores adicionados irão receber um convite para criar uma conta do PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso para o PagerDuty.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a PagerDuty, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **PagerDuty**.

    ![A ligação do PagerDuty na lista de aplicações](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico do PagerDuty no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação do PagerDuty.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
