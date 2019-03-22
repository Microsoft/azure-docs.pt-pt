---
title: 'Tutorial: Integração do Active Directory do Azure com Riskware | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3de8422efdbb7802e0532503e2733518e8050f6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842537"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Integração do Active Directory do Azure com Riskware

Neste tutorial, saiba como integrar Riskware com o Azure Active Directory (Azure AD).

Integrar Riskware no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Riskware.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Riskware (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Riskware, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Riskware logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Riskware da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-riskware-from-the-gallery"></a>Adicionando Riskware da Galeria
Para configurar a integração do Riskware com o Azure AD, terá de adicionar Riskware a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Riskware a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Riskware**, selecione **Riskware** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Riskware na lista de resultados](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Riskware com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Riskware a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Riskware deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Riskware, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Riskware](#create-a-riskware-test-user)**  - para ter um equivalente da Eduarda Almeida na Riskware que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Riskware.

**Para configurar o Azure AD início de sessão único com Riskware, execute os seguintes passos:**

1. No portal do Azure, sobre o **Riskware** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. Sobre o **Riskware domínio e URLs** secção, execute os seguintes passos:

    ![Riskware domínio e URLs únicas início de sessão em informações](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:

    | Ambiente| Padrão de URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > O valor do URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Riskware](mailto:support@pansoftware.com.au) para obter o valor.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/riskware-tutorial/tutorial_general_400.png)

1. Sobre o **Riskware configuração** secção, clique em **configurar Riskware** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Riskware como um administrador.

1. No canto superior direito, clique em **manutenção** para abrir a página de manutenção.

    ![Manter as configurações de Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **autenticação**.

    ![Configuração de Riskware authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na **configuração da autenticação** página, execute os seguintes passos:

    ![Configuração de Riskware authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecione **tipo** como **SAML** para autenticação.

    b. Na **código** caixa de texto, escreva o seu código, como AZURE_UAT.

    c. Na **Descrição** caixa de texto, escreva a sua descrição, como a configuração do AZURE para SSO.

    d. Na **início de sessão único na página** caixa de texto, colar a **SAML único início de sessão no URL do serviço** valor, que copiou do portal do Azure.

    e. Na **termine a página** caixa de texto, colar a **URL de fim de sessão** valor, que copiou do portal do Azure.

    f. Na **campo de formulário de Post** caixa de texto, escreva o nome do campo presente na resposta de postagem que contém o SAML como SAMLResponse

    g. Na **nome da Tag XML identidade** textbox, o atributo de tipo, que contém o identificador exclusivo na resposta, como NameID de SAML.

    h. Abra o transferido **Xml de metadados** partir do portal do Azure no bloco de notas, copie o certificado a partir do ficheiro de metadados e colá-lo no **certificado** caixa de texto

    i. Na **URL de consumidor** caixa de texto, cole o valor de **URL de resposta**, que recebe da equipa de suporte.

    j. Na **emissor** caixa de texto, cole o valor de **identificador**, que recebe da equipa de suporte.

    > [!Note]
    > Contacte [equipa de suporte de cliente Riskware](mailto:support@pansoftware.com.au) obter esses valores

    k. Selecione **utilize POST** caixa de verificação.

    l. Selecione **pedido de SAML utilização** caixa de verificação.

    m. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/riskware-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/riskware-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/riskware-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-riskware-test-user"></a>Criar um utilizador de teste Riskware

Para ativar a utilizadores do Azure AD iniciar sessão no Riskware, tem de ser aprovisionados em Riskware. Riskware, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Riskware como um administrador de segurança.

1. No canto superior direito, clique em **manutenção** para abrir a página de manutenção. 

    ![Configuração de Riskware mantém](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **pessoas**.

    ![Pessoas de configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecione **detalhes** separador e execute os seguintes passos:

    ![Detalhes de configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecione **tipo de pessoa** como empregados.

    b. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.

1. Sobre o **segurança** separador, execute os seguintes passos:

    ![Segurança da configuração de Riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Sob **autenticação** secção, selecione a **autenticação** modo, o que tem a configuração, como a configuração do AZURE para SSO.

    b. Sob **detalhes de início de sessão** secção, além do **ID de utilizador** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    c. Na **palavra-passe** caixa de texto, introduza a palavra-passe do utilizador.

1. Sobre o **organização** separador, execute os seguintes passos:

    ![Configuração de Riskware org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecione a opção como **Level1** organização.

    b. Sob **à área de trabalho principal da pessoa** secção, além do **localização** caixa de texto, escreva a sua localização.

    c. Sob **funcionário** secção, selecione **estado de funcionário** como informais.

1. Clique em **Guardar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Riskware.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a Riskware, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Riskware**.

    ![A ligação de Riskware na lista de aplicações](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Riskware no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Riskware.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

