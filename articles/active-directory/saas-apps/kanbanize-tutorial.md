---
title: 'Tutorial: Integração do Active Directory do Azure com Kanbanize | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60264241"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Tutorial: Integração do Active Directory do Azure com Kanbanize

Neste tutorial, saiba como integrar Kanbanize com o Azure Active Directory (Azure AD).

Integrar Kanbanize no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Kanbanize.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Kanbanize (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Kanbanize, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Kanbanize logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Kanbanize da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-kanbanize-from-the-gallery"></a>Adicionando Kanbanize da Galeria
Para configurar a integração do Kanbanize com o Azure AD, terá de adicionar Kanbanize a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Kanbanize a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Kanbanize**, selecione **Kanbanize** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Kanbanize na lista de resultados](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Kanbanize com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Kanbanize a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kanbanize deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Kanbanize, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Kanbanize](#create-a-kanbanize-test-user)**  - para ter um equivalente da Eduarda Almeida na Kanbanize que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Kanbanize.

**Para configurar o Azure AD início de sessão único com Kanbanize, execute os seguintes passos:**

1. No portal do Azure, sobre o **Kanbanize** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Sobre o **Kanbanize domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Kanbanize domínio e URLs únicas início de sessão em informações](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.kanbanize.com/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Verifique **Mostrar definições de URL avançadas**.

    d.  Na **estado de reencaminhamento** caixa de texto, escreva um URL: `/ctrl_login/saml_login`

    e. Se desejar configurar a aplicação no **SP** iniciado modo, no **URL de início de sessão** textbox escreva um URL com o seguinte padrão: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente Kanbanize](mailto:support@ms.kanbanize.com) obter esses valores. 

5. Aplicação de Kanbanize espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra um exemplo disso. O valor predefinido **identificador de utilizador** é **user.userprincipalname** mas Kanbanize espera que isso seja mapeado com o endereço de e-mail do utilizador. Para que pode usar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na sua configuração de organização
    
    ![Configurar o início de sessão único](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Sobre o **Kanbanize configuração** secção, clique em **configurar Kanbanize** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. Numa janela do browser web diferente, início de sessão para Kanbanize como um administrador de segurança. 

10. Vá para o canto superior direito da página, clique em **definições** logótipo.

    ![Definições de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Na página de painel de administração do lado esquerdo do menu de clique **integrações** e, em seguida, ative **Single Sign-On**. 

    ![Integrações de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Na secção de integrações, clique em **configurar** para abrir **integração de início de sessão único** página.

    ![Configuração de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Sobre o **integração de início de sessão único** página sob **configurações**, execute os seguintes passos:

    ![Integrações de Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. Na **Id de entidade do Idp** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    b. Na **ponto final de início de sessão do Idp** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    c. Na **ponto final de fim de sessão do Idp** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Na **nome de atributo de correio eletrónico** caixa de texto, introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Na **nome de atributo para o nome próprio** caixa de texto, introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Na **nome de atributo para Apelido** caixa de texto, introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Pode obter estes valores através da combinação de valores de espaço de nomes e o nome do respetivo atributo da secção de atributos de utilizador no portal do Azure.

    g. No bloco de notas, abra o certificado com codificação base 64 que transferiu a partir do portal do Azure, copie o seu conteúdo (sem os marcadores de início e de fim) e, em seguida, cole-o para o **certificado X.509 de Idp** caixa.

    h. Verifique **ativar início de sessão com o SSO e Kanbanize**.
    
    i. Clique em **guardar as definições de**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/kanbanize-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-kanbanize-test-user"></a>Criar um utilizador de teste Kanbanize

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Kanbanize. Kanbanize suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Kanbanize se não existir ainda.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de cliente Kanbanize](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Kanbanize.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Kanbanize, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Kanbanize**.

    ![A ligação de Kanbanize na lista de aplicações](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Kanbanize no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Kanbanize.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

