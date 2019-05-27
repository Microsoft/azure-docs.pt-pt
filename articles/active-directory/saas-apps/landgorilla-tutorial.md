---
title: "Tutorial: Integração do Active Directory do Azure com o cliente de Gorila ' s Land | Documentos da Microsoft"
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Gorilla da terra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12519a2bd0a5e3951d0ca9ae51a54bd728a782b
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987902"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Tutorial: Integração do Active Directory do Azure com o cliente de Gorila terra

Neste tutorial, saiba como integrar ' s Land Gorilla cliente com o Azure Active Directory (Azure AD).

Integrar ' s Land Gorilla cliente com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao cliente de Gorila terra
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o cliente de Gorila ' s Land (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – o portal de gestão do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o cliente de Gorila ' s Land, terá dos seguintes itens:

- Uma subscrição do Azure
- Um cliente de Gorila ' s Land início de sessão único na subscrição ativado


> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isso é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar ' s Land Gorilla cliente a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Adicionar ' s Land Gorilla cliente a partir da Galeria
Para configurar a integração do cliente de Gorila terra para o Azure AD, terá de adicionar ' s Land Gorilla cliente a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ' s Land Gorilla cliente a partir da galeria, execute os seguintes passos:**

1. Na  **[do Portal de gestão do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **terra Gorilla cliente**.

    ![Criar um utilizador de teste do Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. No painel de resultados, selecione **terra Gorilla cliente**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com ' s Land Gorilla cliente com base num utilizador de teste chamado "B. Simon".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de equivalente na terra Gorilla cliente a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na terra Gorilla cliente tem de ser estabelecida.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **nome de utilizador** no cliente de Gorila da terra.

Para configurar e testar o Azure AD início de sessão único com o cliente de Gorila ' s Land, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com o grupo limitado.
1. **[Criar um utilizador de teste ' s Land Gorilla](#creating-a-land-gorilla-test-user)**  - para testar o Azure AD início de sessão único com Simon B.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Simon B. utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão único no portal de gestão do Azure e configurar o início de sessão único na aplicação de cliente de Gorila terra.

**Para configurar o Azure AD início de sessão único com o cliente de Gorila ' s Land, execute os seguintes passos:**

1. No portal de gestão do Azure, sobre o **'s Land Gorilla cliente** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, como **modo** selecione **baseado em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. Sobre o **terra Gorilla cliente domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. Na **identificador** caixa de texto, digite o valor utilizando um dos seguinte padrão: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL através de um dos seguinte padrão:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Tenha em atenção que estes não são os valores reais. Terá de atualizar estes valores com o identificador real e o URL de resposta. Aqui iremos sugerir-lhe utilizar o valor único de cadeia de caracteres no identificador de. Contacte [equipe ' s Land Gorilla Client](https://www.landgorilla.com/support/) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML em seu computador.

    ![Configurar o início de sessão único](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Para obter a configuração de SSO completa para a sua aplicação no final de Gorila ' s Land, contacte [equipa de suporte de cliente de Gorila ' s Land](https://www.landgorilla.com/support/) e forneça-los com o transferido **"XML de metadados** ficheiro.


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure Management chamado Simon B.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **Simon B.**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de Simon B.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-land-gorilla-test-user"></a>Criar um utilizador de teste de Gorila de terra

Trabalhe em conjunto com [equipa de suporte de ' s Land Gorilla](https://www.landgorilla.com/support/) para adicionar os utilizadores na plataforma terra Gorilla.
    
### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Simon B. utilizar o Azure início de sessão único, concedendo-lhes acesso para cliente de Gorila ' s Land.

![Atribuir utilizador][200] 

**Para atribuir Simon B. a ' s Land Gorilla cliente, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **terra Gorilla cliente**.

    ![Configurar o início de sessão único](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de ' s Land Gorilla cliente no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação cliente de Gorila da terra.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
