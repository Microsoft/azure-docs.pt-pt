---
title: 'Tutorial: Integração do Active Directory do Azure com Sequr | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Sequr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a491e2ce-b4e8-41b8-8f4a-a2e263e462c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 007989d51ad111fb6a3ef21daee6a7c484bd154d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60341827"
---
# <a name="tutorial-azure-active-directory-integration-with-sequr"></a>Tutorial: Integração do Active Directory do Azure com Sequr

Neste tutorial, saiba como integrar Sequr com o Azure Active Directory (Azure AD).

Integrar Sequr no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Sequr.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Sequr (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Sequr, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Sequr logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Sequr da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-sequr-from-the-gallery"></a>Adicionando Sequr da Galeria
Para configurar a integração do Sequr com o Azure AD, terá de adicionar Sequr a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Sequr a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Sequr**, selecione **Sequr** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Sequr na lista de resultados](./media/sequr-tutorial/tutorial_sequr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Sequr com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Sequr a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Sequr deve ser estabelecido.

Sequr, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Sequr, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Sequr](#create-a-sequr-test-user)**  - para ter um equivalente da Eduarda Almeida na Sequr que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Sequr.

**Para configurar o Azure AD início de sessão único com Sequr, execute os seguintes passos:**

1. No portal do Azure, sobre o **Sequr** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/sequr-tutorial/tutorial_sequr_samlbase.png)

1. Sobre o **Sequr domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Sequr domínio e URLs únicas início de sessão em informações](./media/sequr-tutorial/tutorial_sequr_url.png)

    Na **identificador** caixa de texto, escreva o URL: `https://login.sequr.io`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Sequr domínio e URLs únicas início de sessão em informações](./media/sequr-tutorial/tutorial_sequr_url1.png)

    a. Na **URL de início de sessão** caixa de texto, escreva o URL: `https://login.sequr.io`

    b. Na **estado de reencaminhamento** caixa de texto, irá obter este valor, o que é explicado mais tarde no tutorial.
     
1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/sequr-tutorial/tutorial_sequr_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/sequr-tutorial/tutorial_general_400.png)
    
1. Sobre o **Sequr configuração** secção, clique em **configurar Sequr** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Sequr](./media/sequr-tutorial/tutorial_sequr_configure.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Sequr como administrador.

1. Clique nas **integrações** do painel de navegação à esquerda.

    ![Configuração de Sequr](./media/sequr-tutorial/configure1.png)

1. Desloque para baixo para o **início de sessão único** secção e clique em **gerir**.

    ![Configuração de Sequr](./media/sequr-tutorial/configure2.png)

1. Na **gerir o início de sessão único** secção, execute os seguintes passos:

    ![Configuração de Sequr](./media/sequr-tutorial/configure3.png)

    a. Na **URL fornecedor de identidade única Sign-On** caixa de texto, colar a **SAML único início de sessão no URL do serviço** valor, que copiou do portal do Azure.

    b. Arraste e largue os **certificado** arquivo, o que transferiu do portal do Azure ou manualmente, introduza o conteúdo do certificado.

    c. Depois de guardar a configuração, será gerado o valor de estado de reencaminhamento. Copiar o **estado de reencaminhamento** valor e cole-a no **estado de reencaminhamento** caixa de texto de **Sequr domínio e URLs** secção no portal do Azure.

    d. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/sequr-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/sequr-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/sequr-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/sequr-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-sequr-test-user"></a>Criar um utilizador de teste Sequr

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Sequr. Trabalhar com [equipa de suporte de cliente Sequr](mailto:support@sequr.io) para adicionar os utilizadores na plataforma Sequr. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Sequr.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Sequr, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Sequr**.

    ![A ligação de Sequr na lista de aplicações](./media/sequr-tutorial/tutorial_sequr_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Sequr no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Sequr.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/sequr-tutorial/tutorial_general_01.png
[2]: ./media/sequr-tutorial/tutorial_general_02.png
[3]: ./media/sequr-tutorial/tutorial_general_03.png
[4]: ./media/sequr-tutorial/tutorial_general_04.png

[100]: ./media/sequr-tutorial/tutorial_general_100.png

[200]: ./media/sequr-tutorial/tutorial_general_200.png
[201]: ./media/sequr-tutorial/tutorial_general_201.png
[202]: ./media/sequr-tutorial/tutorial_general_202.png
[203]: ./media/sequr-tutorial/tutorial_general_203.png

