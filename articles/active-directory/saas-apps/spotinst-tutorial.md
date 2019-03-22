---
title: 'Tutorial: Integração do Active Directory do Azure com Spotinst | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bffdf439a192fb10fe695fbfa18e8c7abf8077
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57846689"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Tutorial: Integração do Active Directory do Azure com Spotinst

Neste tutorial, saiba como integrar Spotinst com o Azure Active Directory (Azure AD).

Integrar Spotinst no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Spotinst.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Spotinst (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Spotinst, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Spotinst logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Spotinst da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-spotinst-from-the-gallery"></a>Adicionando Spotinst da Galeria
Para configurar a integração do Spotinst com o Azure AD, terá de adicionar Spotinst a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Spotinst a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Spotinst**, selecione **Spotinst** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Spotinst na lista de resultados](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Spotinst com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Spotinst a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Spotinst deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Spotinst, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Spotinst](#create-a-spotinst-test-user)**  - para ter um equivalente da Eduarda Almeida na Spotinst que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Spotinst.

**Para configurar o Azure AD início de sessão único com Spotinst, execute os seguintes passos:**

1. No portal do Azure, sobre o **Spotinst** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Sobre o **Spotinst domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no modo de IDP iniciado:

    ![Spotinst domínio e URLs únicas início de sessão em informações](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Verifique **Mostrar definições de URL avançadas**.

    b. Na **estado de reencaminhamento** caixa de texto, escreva um valor: `<ID>`

    c. Se desejar configurar a aplicação no **SP** iniciadas pelo modo, na **iniciar sessão no URL** caixa de texto, escreva o URL: `https://console.spotinst.com`

    > [!NOTE]
    > O valor de estado de reencaminhamento não é real. Atualizar o valor de estado de reencaminhamento com o valor de estado de reencaminhamento real, o que é explicado mais tarde no tutorial.

4. Aplicação de Spotinst espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Captura de ecrã seguinte mostra um exemplo para o mesmo.

    ![Configurar o início de sessão único](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Nome do Atributo | Valor do Atributo |
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d. Deixe o **espaço de nomes** em branco.

    e. Clique em **Ok**

6. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/spotinst-tutorial/tutorial_general_400.png)

8. Numa janela do browser web diferente, início de sessão para Spotinst como um administrador de segurança.

9. Clique nas **ícone de utilizador** no canto superior direito do ecrã e clique em **definições**.

    ![Definições de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Clique nas **SECURITY** separador na parte superior e, em seguida, selecione **fornecedores de identidade** e execute os seguintes passos:

    ![Segurança de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Cópia a **estado de reencaminhamento** para a sua instância de valor e cole-o na **estado de reencaminhamento** caixa de texto no **Spotinst domínio e URLs** secção no portal do Azure.

    b. Clique em **procurar** para carregar o ficheiro xml de metadados que transferiu a partir do portal do Azure

    c. Clique em **GUARDAR**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/spotinst-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/spotinst-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/spotinst-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/spotinst-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-spotinst-test-user"></a>Criar um utilizador de teste Spotinst

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Spotinst.

1. Se tiver configurado o aplicativo no **SP** intiated modo, execute os seguintes passos:

   a. Numa janela do browser web diferente, início de sessão para Spotinst como um administrador de segurança.

   b. Clique nas **ícone de utilizador** no canto superior direito do ecrã e clique em **definições**.

    ![Definições de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique em **usuários** e selecione **adicionar utilizador**.

    ![Definições de Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Na secção Adicionar utilizador, execute os seguintes passos:

    ![Definições de Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Na **FullName** caixa de texto, introduza o nome completo do utilizador, como **BrittaSimon**.

    * Na **E-Mail** caixa de texto, introduza o endereço de e-mail do utilizador, como **brittasimon\@contoso.com**.

    * Selecione os detalhes da sua organização específicas para o **função de organização, a função de conta e contas**.

2. Se tiver configurado o aplicativo no **IDP** intiated modo, aí não é nenhum item de ação para nesta secção. Spotinst suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Um novo utilizador é criado durante uma tentativa de aceder Spotinst se não existir ainda.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Spotinst.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Spotinst, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Spotinst**.

    ![A ligação de Spotinst na lista de aplicações](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Spotinst no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Spotinst.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

