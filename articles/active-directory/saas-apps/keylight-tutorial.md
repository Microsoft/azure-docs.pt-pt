---
title: 'Tutorial: Integração do Active Directory do Azure com LockPath Keylight | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc5121f6604fae9a28b52db1bfb308d7cdb968d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60264287"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Integração do Active Directory do Azure com LockPath Keylight

Neste tutorial, saiba como integrar LockPath Keylight com o Azure Active Directory (Azure AD).

Integrar LockPath Keylight no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao LockPath Keylight
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para LockPath Keylight (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LockPath Keylight, terá dos seguintes itens:

- Uma subscrição do Azure
- Um LockPath Keylight início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando LockPath Keylight da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Adicionando LockPath Keylight da Galeria
Para configurar a integração do LockPath Keylight com o Azure AD, terá de adicionar LockPath Keylight a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar LockPath Keylight a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **LockPath Keylight**.

    ![Criar um utilizador de teste do Azure AD](./media/keylight-tutorial/tutorial_keylight_search.png)

1. No painel de resultados, selecione **LockPath Keylight**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com LockPath Keylight com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no LockPath Keylight a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LockPath Keylight deve ser estabelecido.

LockPath Keylight, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com LockPath Keylight, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  - para ter um equivalente da Eduarda Almeida na LockPath Keylight que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo LockPath Keylight.

**Para configurar o Azure AD início de sessão único com LockPath Keylight, execute os seguintes passos:**

1. No portal do Azure, sobre o **LockPath Keylight** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

1. Sobre o **LockPath Keylight domínio e URLs** secção, execute os seguintes passos::

    ![Configurar o início de sessão único](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.keylightgrc.com/`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.keylightgrc.com`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente de Keylight LockPath](https://www.lockpath.com/contact/) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Raw)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/keylight-tutorial/tutorial_general_400.png)
    
1. Sobre o **LockPath Keylight configuração** secção, clique em **configurar Keylight de LockPath** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/keylight-tutorial/tutorial_keylight_configure.png) 

1. Para ativar o SSO no LockPath Keylight, execute os seguintes passos:
   
    a. Início de sessão na sua conta de LockPath Keylight como administrador.
    
    b. No menu na parte superior, clique em **pessoa**e selecione **Keylight configuração**.
   
    ![Configurar o início de sessão único](./media/keylight-tutorial/401.png) 

    c. Na vista de árvore à esquerda, clique em **SAML**.
   
    ![Configurar o início de sessão único](./media/keylight-tutorial/402.png) 

    d. Sobre o **definições de SAML** caixa de diálogo, clique em **editar**.
   
    ![Configurar o início de sessão único](./media/keylight-tutorial/404.png) 

1. Sobre o **editar definições de SAML** caixa de diálogo página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/keylight-tutorial/405.png) 
   
    a. Definir **autenticação SAML** ao **Active Directory**.

    b. Colar o **SAML único início de sessão no URL do serviço** valor que copiou do portal do Azure para o **URL de início de sessão do fornecedor de identidade** caixa de texto.

    c. Colar o **URL de serviço de fim de sessão único** valor que copiou do portal do Azure para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.

    d. Clique em **Escolher ficheiro** para selecionar o seu certificado LockPath Keylight transferido e, em seguida, clique em **aberto** para carregar o certificado.

    e. Definir **localização de Id de utilizador de SAML** ao **NameIdentifier elemento da declaração de assunto**.
    
    f. Forneça o **fornecedor de serviços de Keylight** usando o seguinte padrão: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Definir **os utilizadores de aprovisionamento automático** ao **Active Directory**.

    h. Definir **tipo de conta de aprovisionamento automático** ao **utilizador completo**.

    i. Definir **função de segurança de aprovisionamento automático**, selecione **usuário padrão com o SAML**.
    
    j. Definir **configuração de segurança de aprovisionamento automático**, selecione **configuração de utilizador padrão**.
     
    k. Na **atributo de correio eletrónico** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. Na **atributo de nome próprio** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. Na **atributo de nome de último** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/keylight-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/keylight-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/keylight-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/keylight-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Criar um utilizador de teste LockPath Keylight

Nesta secção, vai criar um usuário chamado Eduarda Almeida no LockPath Keylight. LockPath Keylight suporta o aprovisionamento de just-in-time, que está ativado por predefinição.

Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado ao aceder a LockPath Keylight se o utilizador ainda não existe. 

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte de cliente de Keylight LockPath](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para LockPath Keylight.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a LockPath Keylight, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **LockPath Keylight**.

    ![Configurar o início de sessão único](./media/keylight-tutorial/tutorial_keylight_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico LockPath Keylight no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo LockPath Keylight. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

