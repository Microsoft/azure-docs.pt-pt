---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de gestão de privacidade OneTrust | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de gestão de privacidade OneTrust.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d907ec5cc40bcf9127fc20fbbc8e2e0aca476c3b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195860"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Tutorial: Integração do Active Directory do Azure com o Software de gestão de privacidade OneTrust

Neste tutorial, saiba como integrar o Software de gestão de privacidade OneTrust com o Azure Active Directory (Azure AD).

Integrar o Software de gestão de privacidade OneTrust no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Software de gestão de privacidade OneTrust.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Software de gestão de privacidade OneTrust (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de gestão de privacidade OneTrust, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Software de gestão de privacidade OneTrust início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Software de gestão de privacidade OneTrust da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Adicionando o Software de gestão de privacidade OneTrust da Galeria
Para configurar a integração do Software de gestão de privacidade OneTrust com o Azure AD, terá de adicionar OneTrust Software de gestão de privacidade a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Software de gestão de privacidade OneTrust a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Software de gestão de privacidade OneTrust**, selecione **OneTrust Software de gestão de privacidade** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Software de gestão de privacidade OneTrust na lista de resultados](./media/onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Software de gestão de privacidade de OneTrust com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Software de gestão de privacidade OneTrust a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Software de gestão de privacidade OneTrust deve ser estabelecido.

Software de gestão da privacidade de OneTrust, atribua o valor do **nome de utilizador** no Azure AD como o valor da **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Software de gestão de privacidade OneTrust, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de Software de gestão de privacidade OneTrust](#create-a-onetrust-privacy-management-software-test-user)**  - para ter um equivalente da Eduarda Almeida no Software de gestão de privacidade OneTrust que está ligada à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de Software de gestão de privacidade OneTrust.

**Para configurar o Azure AD início de sessão único com o Software de gestão de privacidade OneTrust, execute os seguintes passos:**

1. No portal do Azure, sobre o **Software de gestão de privacidade OneTrust** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/onetrust-tutorial/tutorial_onetrust_samlbase.png)

1. Na **OneTrust privacidade gestão Software domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação na **IDP** iniciada pelo modo:

    ![URLs de domínio de Software de gestão de privacidade OneTrust e únicas início de sessão em informações](./media/onetrust-tutorial/tutorial_onetrust_url.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://www.onetrust.com/saml2`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.onetrust.com/auth/consumerservice`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![URLs de domínio de Software de gestão de privacidade OneTrust e únicas início de sessão em informações](./media/onetrust-tutorial/tutorial_onetrust_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.onetrust.com/auth/login`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Contacte [equipa de suporte de cliente de Software de gestão de privacidade OneTrust](mailto:support@onetrust.com) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/onetrust-tutorial/tutorial_onetrust_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/onetrust-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Software de gestão de privacidade OneTrust** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte do Software de gestão de privacidade OneTrust](mailto:support@onetrust.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/onetrust-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/onetrust-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/onetrust-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/onetrust-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Criar um utilizador de teste de Software de gestão de privacidade OneTrust

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Software de gestão de privacidade OneTrust. Software de gestão de privacidade OneTrust suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder a Software de gestão de privacidade OneTrust se não existir ainda.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte do Software de gestão de privacidade OneTrust](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Software de gestão de privacidade OneTrust.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Software de gestão de privacidade OneTrust, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Software de gestão de privacidade OneTrust**.

    ![A ligação de Software de gestão de privacidade OneTrust na lista de aplicações](./media/onetrust-tutorial/tutorial_onetrust_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Software de gestão de privacidade OneTrust no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de Software de gestão de privacidade OneTrust.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/onetrust-tutorial/tutorial_general_01.png
[2]: ./media/onetrust-tutorial/tutorial_general_02.png
[3]: ./media/onetrust-tutorial/tutorial_general_03.png
[4]: ./media/onetrust-tutorial/tutorial_general_04.png

[100]: ./media/onetrust-tutorial/tutorial_general_100.png

[200]: ./media/onetrust-tutorial/tutorial_general_200.png
[201]: ./media/onetrust-tutorial/tutorial_general_201.png
[202]: ./media/onetrust-tutorial/tutorial_general_202.png
[203]: ./media/onetrust-tutorial/tutorial_general_203.png

