---
title: 'Tutorial: Integração do Active Directory do Azure com a percepção dos Estados Unidos (não-UltiPro) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a percepção dos Estados Unidos (não-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcc93254b50da9bff6f687034d39d0058aa514e1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765960"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Tutorial: Integração do Active Directory do Azure com a percepção dos Estados Unidos (não-UltiPro)

Neste tutorial, saiba como integrar a percepção dos Estados Unidos (não-UltiPro) com o Azure Active Directory (Azure AD).

Integrar a percepção dos Estados Unidos (não-UltiPro) no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso a percepção dos Estados Unidos (não-UltiPro).
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada a percepção dos Estados Unidos (não-UltiPro) (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com percepção dos Estados Unidos (não-UltiPro), terá dos seguintes itens:

- Uma subscrição do Azure
- Um percepção dos Estados Unidos (não-UltiPro) início de sessão único de subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição de percepção dos Estados Unidos (não-UltiPro) da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Adição de percepção dos Estados Unidos (não-UltiPro) da Galeria
Para configurar a integração de percepção dos Estados Unidos (não-UltiPro) para o Azure AD, terá de adicionar a percepção dos Estados Unidos (não-UltiPro) da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar percepção dos Estados Unidos (não-UltiPro) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **percepção dos Estados Unidos (não-UltiPro)**, selecione **percepção dos Estados Unidos (não-UltiPro)** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Percepção dos Estados Unidos (não-UltiPro) na lista de resultados](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com percepção dos Estados Unidos (não-UltiPro) com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente na percepção dos Estados Unidos (não-UltiPro) a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na percepção dos Estados Unidos (não-UltiPro) deve ser estabelecido.

Na percepção dos Estados Unidos (não-UltiPro), atribuir o valor do **nome de utilizador** no Azure AD como o valor da **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com percepção dos Estados Unidos (não-UltiPro), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de percepção dos Estados Unidos (não-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  - para ter um equivalente da Eduarda Almeida na percepção dos Estados Unidos (não-UltiPro) que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de percepção dos Estados Unidos (não-UltiPro).

**Para configurar o Azure AD início de sessão único com percepção dos Estados Unidos (não-UltiPro), execute os seguintes passos:**

1. No portal do Azure, sobre o **percepção dos Estados Unidos (não-UltiPro)** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

1. Sobre o **percepção dos Estados Unidos (não-UltiPro) de domínio e URLs** secção, execute os seguintes passos:

    ![Informações de início de sessão de único de percepção dos Estados Unidos (não-UltiPro) domínio e URLs](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. Na **identificador** caixa de texto, escreva o URL: `https://perception.kanjoya.com/sp`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > O valor não é real. Atualizar o valor com o URL de resposta real, o que é explicado mais tarde no tutorial.
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

1. Sobre o **percepção dos Estados Unidos (não-UltiPro) configuração** secção, clique em **configurar percepção dos Estados Unidos (não-UltiPro)** para abrir **configurar início de sessão** janela . Cópia a **ID de entidade de SAML** partir o **secção de referência rápida.**

    a. O **percepção dos Estados Unidos (não-UltiPro)** aplicação requer o **ID de entidade de SAML** valor, que copiou, para ser codificada por uri. Para obter o valor codificado por uri, utilize a seguinte ligação:**http://www.url-encode-decode.com/**.

    b. Depois de obter o uri valor codificado combiná-la com o **URL de resposta** conforme mencionado abaixo -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Cole o valor acima no **URL de resposta** na caixa de texto **percepção dos Estados Unidos (não-UltiPro) de domínio e URLs** secção.

    ![Configuração do percepção dos Estados Unidos (não UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

1. Na outra janela do browser, inicie sessão site da sua empresa percepção dos Estados Unidos (não-UltiPro) como um administrador.

1. Na barra de ferramentas principal, clique em **definições de conta**.

    ![Utilizador do percepção dos Estados Unidos (não-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

1. Sobre o **definições de conta** página, execute os seguintes passos:

    ![Utilizador do percepção dos Estados Unidos (não-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Na **nome da empresa** caixa de texto, escreva o nome da **empresa**.
    
    b. Na **nome da conta** caixa de texto, escreva o nome da **conta**.

    c. Na **predefinido responder a E-Mail** texto, escreva o válido **E-Mail**.

    d. Selecione **fornecedor de identidade SSO** como **SAML 2.0**.

1. Sobre o **SSO configuração** página, execute os seguintes passos:

    ![Percepção dos Estados Unidos (não UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecione **tipo de NameID SAML** como **E-Mail**.

    b. Na **nome da configuração de SSO** caixa de texto, escreva o nome do seu **configuração**.
    
    c. Na **nome do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 

    d. Na **caixa de texto do domínio de SAML**, introduza o domínio, como  **\@contoso.com**.

    e. Clique em **carregar novamente** para carregar o **XML de metadados** ficheiro.

    f. Clique em **Atualizar**.


> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Criar um utilizador de teste de percepção dos Estados Unidos (não-UltiPro)

Nesta secção, vai criar um usuário chamado Eduarda Almeida na percepção dos Estados Unidos (não-UltiPro). Trabalhar com [equipa de suporte de percepção dos Estados Unidos (não-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) para adicionar os utilizadores na plataforma percepção dos Estados Unidos (não-UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a percepção dos Estados Unidos (não-UltiPro).

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a percepção dos Estados Unidos (não-UltiPro), execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **percepção dos Estados Unidos (não-UltiPro)**.

    ![A ligação de percepção dos Estados Unidos (não-UltiPro) na lista de aplicações](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de percepção dos Estados Unidos (não-UltiPro) no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo de percepção dos Estados Unidos (não-UltiPro).
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

