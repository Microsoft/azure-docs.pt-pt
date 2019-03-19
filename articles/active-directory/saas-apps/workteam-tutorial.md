---
title: 'Tutorial: Integração do Active Directory do Azure com Workteam | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd986544dfb1472f5cc8a013fec951dca42a59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898657"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Tutorial: Integração do Active Directory do Azure com Workteam

Neste tutorial, saiba como integrar Workteam com o Azure Active Directory (Azure AD).

Integrar Workteam no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Workteam.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Workteam (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Workteam, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Workteam logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Workteam da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-workteam-from-the-gallery"></a>Adicionando Workteam da Galeria
Para configurar a integração do Workteam com o Azure AD, terá de adicionar Workteam a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Workteam a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Workteam**, selecione **Workteam** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Workteam na lista de resultados](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Workteam com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Workteam a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Workteam deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Workteam, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Workteam](#create-a-workteam-test-user)**  - para ter um equivalente da Eduarda Almeida na Workteam que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Workteam.

**Para configurar o Azure AD início de sessão único com Workteam, execute os seguintes passos:**

1. No portal do Azure, sobre o **Workteam** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. Sobre o **Workteam domínio e URLs** secção, o utilizador não tem de efetuar outros passos de como o aplicativo já está pré-integrado com o Azure.

    ![Workteam domínio e URLs únicas início de sessão em informações](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Workteam domínio e URLs únicas início de sessão em informações](./media/workteam-tutorial/tutorial_workteam_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL: `https://app.workte.am`
     
5. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/workteam-tutorial/tutorial_general_400.png)
    
7. Sobre o **Workteam configuração** secção, clique em **configurar Workteam** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Workteam](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. Numa janela do browser web diferente, inicie sessão no Workteam como um administrador de segurança.

9. No canto superior direito, clique em **logótipo de perfil** e, em seguida, clique em **definições de organização**. 

    ![Definições de Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. Sob **autenticação** secção, clique em **logótipo definições**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. Sobre o **definições de SAML** página, execute os seguintes passos:

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione **SAML IdP** como **AD Azure**.

    b. Na **SAML único início de sessão no URL do serviço** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    c. Na **ID de entidade de SAML** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    d. No bloco de notas, abra a **certificado com codificação base 64** que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **certificado de assinatura de SAML (Base64)** caixa.

    e. Clique em **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/workteam-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/workteam-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/workteam-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/workteam-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-workteam-test-user"></a>Criar um utilizador de teste Workteam

Para ativar a utilizadores do Azure AD ao iniciar sessão Workteam, tem de ser aprovisionados em Workteam. Workteam, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Início de sessão para Workteam como um administrador de segurança.

2. Na parte superior ao centro do **definições de organização** página, clique em **utilizadores** e, em seguida, clique em **novo utilizador**.

    ![Utilizador Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Sobre o **novo funcionário** página, execute os seguintes passos:

    ![Workteam newuser](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na **Name** texto, introduza o nome de utilizador, como **Brittasimon**.

    b. Na **E-Mail** texto, introduza o e-mail do utilizador, como **Brittasimon\@contoso.com**.

    c. Clique em **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Workteam.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Workteam, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Workteam**.

    ![A ligação de Workteam na lista de aplicações](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Workteam no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Workteam.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

