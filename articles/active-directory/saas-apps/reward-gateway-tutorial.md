---
title: 'Tutorial: Integração do Active Directory do Azure com o Gateway de recompensa | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Gateway de recompensa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85c2dcfb6ff1ba0bb122a0bf9aae5f01a4085f29
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817053"
---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Tutorial: Integração do Active Directory do Azure com o Gateway de recompensa

Neste tutorial, saiba como integrar o Gateway de recompensa com o Azure Active Directory (Azure AD).

Integrar o Gateway de recompensa no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Gateway de recompensa
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Gateway de recompensa (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Gateway de recompensa, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Gateway de recompensa logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Gateway de recompensa a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-reward-gateway-from-the-gallery"></a>Adicionar o Gateway de recompensa a partir da Galeria
Para configurar a integração do Gateway de recompensa com o Azure AD, terá de adicionar o Gateway de recompensa a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Gateway de recompensa da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Gateway de recompensa**.

    ![Criar um utilizador de teste do Azure AD](./media/reward-gateway-tutorial/tutorial_rewardgateway_search.png)

1. No painel de resultados, selecione **Gateway de recompensa**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/reward-gateway-tutorial/tutorial_rewardgateway_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o Gateway de recompensa com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Gateway de recompensa a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Gateway de recompensa deve ser estabelecido.

No Gateway de recompensa, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Gateway de recompensa, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do Gateway de recompensa](#creating-a-reward-gateway-test-user)**  - para ter um equivalente da Eduarda Almeida no Gateway de recompensa que está ligada à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de Gateway de recompensa.

**Para configurar o Azure AD início de sessão único com o Gateway de recompensa, execute os seguintes passos:**

1. No portal do Azure, sobre o **Gateway de recompensa** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/reward-gateway-tutorial/tutorial_rewardgateway_samlbase.png)

1. Sobre o **URLs de domínio de Gateway de recompensa e** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/reward-gateway-tutorial/tutorial_rewardgateway_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.rewardgateway.com` |
    | `https://<companyname>.rewardgateway.co.uk/` |
    | `https://<companyname>.rewardgateway.co.nz/` |
    | `https://<companyname>.rewardgateway.com.au/` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    |  `https://<companyname>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<companyname>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<companyname>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<companyname>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Para obter estes valores começar a configurar uma integração no Portal do Gestor de recompensa. Podem encontrar detalhes na https://success.rewardgateway.com/authentication-integrations/microsoft-azure-for-authentication
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/reward-gateway-tutorial/tutorial_rewardgateway_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/reward-gateway-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Gateway de recompensa** lado, a começar a configurar uma integração no Portal do Gestor de recompensa. Utilize os metadados baixado para obter o seu certificado de assinatura e carregar que durante a configuração. Podem encontrar detalhes na https://success.rewardgateway.com/authentication-integrations/microsoft-azure-for-authentication

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/reward-gateway-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/reward-gateway-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/reward-gateway-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/reward-gateway-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-reward-gateway-test-user"></a>Criar um utilizador de teste do Gateway de recompensa

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Gateway de recompensa. Trabalhar com o Gateway de recompensa [equipa de suporte](mailto:clientsupport@rewardgateway.com) para adicionar os utilizadores da plataforma de recompensa Gateway.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Gateway de recompensa.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a recompensa Gateway, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Gateway de recompensa**.

    ![Configurar o início de sessão único](./media/reward-gateway-tutorial/tutorial_rewardgateway_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de recompensa Gateway no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de Gateway de recompensa.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/reward-gateway-tutorial/tutorial_general_01.png
[2]: ./media/reward-gateway-tutorial/tutorial_general_02.png
[3]: ./media/reward-gateway-tutorial/tutorial_general_03.png
[4]: ./media/reward-gateway-tutorial/tutorial_general_04.png

[100]: ./media/reward-gateway-tutorial/tutorial_general_100.png

[200]: ./media/reward-gateway-tutorial/tutorial_general_200.png
[201]: ./media/reward-gateway-tutorial/tutorial_general_201.png
[202]: ./media/reward-gateway-tutorial/tutorial_general_202.png
[203]: ./media/reward-gateway-tutorial/tutorial_general_203.png

