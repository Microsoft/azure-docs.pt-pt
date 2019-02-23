---
title: 'Tutorial: Integração do Active Directory do Azure com Trisotech Digital Enterprise Server | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a1a334c6c7852923da94403352bb7318b241629
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735849"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integração do Active Directory do Azure com Trisotech Digital Enterprise Server

Neste tutorial, saiba como integrar Trisotech Digital Enterprise Server no Azure Active Directory (Azure AD).

Integrar Trisotech Digital Enterprise Server no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Trisotech Digital Enterprise Server.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Trisotech Digital Enterprise Server (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Trisotech Digital Enterprise Server, terá dos seguintes itens:

- Uma subscrição do Azure
- Um servidor de Enterprise Digital Trisotech logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Trisotech Digital Enterprise Server da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adicionando Trisotech Digital Enterprise Server da Galeria
Para configurar a integração do Trisotech Digital Enterprise Server no Azure AD, terá de adicionar Trisotech Digital Enterprise Server a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Trisotech Digital Enterprise Server a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Trisotech Digital Enterprise Server**, selecione **Trisotech Digital Enterprise Server** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Trisotech Digital Enterprise Server na lista de resultados](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Trisotech Digital Enterprise Server com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Trisotech Digital Enterprise Server a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Trisotech Digital Enterprise Server deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Trisotech Digital Enterprise Server, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do servidor de Enterprise Digital Trisotech](#create-a-trisotech-digital-enterprise-server-test-user)**  - para ter um equivalente da Eduarda Almeida na Trisotech Digital Enterprise Server que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Trisotech Digital Enterprise Server.

**Para configurar o Azure AD início de sessão único com Trisotech Digital Enterprise Server, execute os seguintes passos:**

1. No portal do Azure, sobre o **Trisotech Digital Enterprise Server** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. Sobre o **Trisotech Digital Enterprise Server domínio e URLs** secção, execute os seguintes passos:

    ![URLs de domínio do servidor do Enterprise Digital Trisotech e únicas início de sessão em informações](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.trisotech.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de servidor de Enterprise Digital Trisotech](mailto:support@trisotech.com) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas. 

    ![O link de download de certificado](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Trisotech Digital Enterprise Server configuração como administrador.

1. Clique nas **ícone de Menu** e, em seguida, selecione **administração**.

    ![Configurar o início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. Selecione **fornecedor de utilizador**.

    ![Configurar o início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. Na **configurações de fornecedor de utilizador** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selecione **protegida Assertion Markup Language 2 (SAML 2)** na lista pendente na **método de autenticação**.

    b. Na **URL de metadados** caixa de texto, colar a **Url de metadados de Federação de aplicação** formam de valor, que copiou do portal do Azure.

    c. Na **ID da aplicação** caixa de texto, introduza o URL com o seguinte padrão: `https://<companyname>.trisotech.com`.

    d. Clicar em **Guardar**

    e. Introduza o nome de domínio na **permitidos domínios (vazio significa que todas as pessoas)** caixa de texto, ela atribui automaticamente licenças para utilizadores correspondentes os domínios permitido

    f. Clicar em **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Criar um utilizador de teste Trisotech Digital Enterprise Server

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Trisotech Digital Enterprise Server, se não existir ainda.
>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte do Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Trisotech Digital Enterprise Server.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Trisotech Digital Enterprise Server, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Trisotech Digital Enterprise Server**.

    ![A ligação de Trisotech Digital Enterprise Server na lista de aplicações](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Trisotech Digital Enterprise Server no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Trisotech Digital Enterprise Server.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

