---
title: 'Tutorial: Integração do Active Directory do Azure com Silverback | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5d48cbeb1714a4a3cd645fd65264aec0baf2c05
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871772"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutorial: Integração do Active Directory do Azure com Silverback

Neste tutorial, saiba como integrar Silverback com o Azure Active Directory (Azure AD).

Integrar Silverback no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Silverback.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Silverback (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Silverback, terá dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição ativa do Silverback

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Silverback da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-silverback-from-the-gallery"></a>Adicionando Silverback da Galeria
Para configurar a integração do Silverback com o Azure AD, terá de adicionar Silverback a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Silverback a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Silverback**, selecione **Silverback** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Silverback na lista de resultados](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Silverback com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Silverback a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Silverback deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Silverback, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Silverback](#create-a-silverback-test-user)**  - para ter um equivalente da Eduarda Almeida na Silverback que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Silverback.

**Para configurar o Azure AD início de sessão único com Silverback, execute os seguintes passos:**

1. No portal do Azure, sobre o **Silverback** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. Sobre o **Silverback domínio e URLs** secção, execute os seguintes passos:

    ![Silverback domínio e URLs únicas início de sessão em informações](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `<YOURSILVERBACKURL>.com`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente Silverback](mailto:helpdesk@matrix42.com) obter esses valores. 

4. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas.

    ![O link de download de certificado](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/silverback-tutorial/tutorial_general_400.png)

6.  Início de sessão ao seu servidor Silverback como administrador e execute os seguintes passos:

    a.  Navegue para **administrador** > **fornecedor de autenticação**.

    b. Sobre o **definições do fornecedor de autenticação** página, execute os seguintes passos:

    ![O administrador](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Clique em **importação a partir do URL**.
    
    d.  Cole o URL de metadados copiado e clique em **OK**.
    
    e.  Confirme com **OK** , em seguida, os valores serão preenchidos automaticamente.
    
    f.  Ativar **Mostrar na página de início de sessão**.
    
    g.  Ativar **criação dinâmica de utilizador** se pretender adicionar automaticamente utilizadores do Azure AD autorizado (opcional).
    
    h.  Criar uma **Title** do botão sobre o Portal Self-Service.

    i.  Carregar uma **ícone** clicando em **Escolher ficheiro**.
    
    j.  Selecione o plano de fundo **cor** do botão.
    
    k.  Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/silverback-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/silverback-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/silverback-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/silverback-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-silverback-test-user"></a>Criar um utilizador de teste Silverback

Para ativar a utilizadores do Azure AD iniciar sessão no Silverback, tem de ser aprovisionados em Silverback. Silverback, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu servidor Silverback como administrador.

2. Navegue para **usuários** e **adicionar um novo utilizador do dispositivo**.

3. Sobre o **básica** página, execute os seguintes passos:

    ![O utilizador](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Na **nome de utilizador** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    d. Na **endereço de email** texto, introduza o e-mail do utilizador, como **Brittasimon@contoso.com**.

    e. Na **palavra-passe** texto, introduza a palavra-passe.
    
    f. Na **Confirmar palavra-passe** caixa de texto, volte a introduzir a palavra-passe e confirme.

    g. Clique em **Guardar**.

>[!NOTE]
>Se não quiser criar manualmente a cada utilizador ativar a **criação dinâmica de utilizador** caixa de verificação sob **administração** > **fornecedor de autenticação**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Silverback.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Silverback, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Silverback**.

    ![A ligação de Silverback na lista de aplicações](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Silverback no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Silverback.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

