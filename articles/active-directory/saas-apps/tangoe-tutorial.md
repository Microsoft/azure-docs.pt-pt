---
title: 'Tutorial: Integração do Active Directory do Azure com o Tangoe comando Premium Mobile | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Tangoe comando Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9f410fa890df7aac3c3bf4d89468b92e69ba38
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883237"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integração do Active Directory do Azure com o Tangoe comando Premium Mobile

Neste tutorial, saiba como integrar Tangoe comando Premium Mobile com o Azure Active Directory (Azure AD).

Integrar Tangoe comando Premium Mobile com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Tangoe comando Premium Mobile
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Tangoe comando Premium Mobile (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Tangoe comando Premium Mobile, precisa do seguinte:

- Uma subscrição do Azure
- Um Tangoe comando Premium Mobile logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Tangoe comando Premium Mobile a partir da Galeria
1. Configurar e testar o Azure AD início de sessão único

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Adicionar Tangoe comando Premium Mobile a partir da Galeria
Para configurar a integração do Tangoe comando Premium Mobile com o Azure AD, terá de adicionar Tangoe comando Premium Mobile a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Tangoe comando Premium Mobile a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Tangoe comando Premium Mobile**, selecione **Tangoe comando Premium Mobile** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Adicionar Tangoe comando Premium Mobile a partir da Galeria](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Tangoe comando Premium Mobile com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte Tangoe comando Premium móvel a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado Tangoe comando Premium móvel deve ser estabelecido.

No Tangoe comando Premium Mobile, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Tangoe comando Premium Mobile, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Tangoe comando Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)**  - para ter um equivalente da Eduarda Almeida Tangoe comando Premium móvel que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Tangoe comando Premium Mobile.

**Para configurar o Azure AD início de sessão único com Tangoe comando Premium Mobile, execute os seguintes passos:**

1. No portal do Azure, sobre o **Tangoe comando Premium Mobile** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Inicio de sessão baseado em SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. Sobre o **Tangoe comando Premium Mobile domínio e URLs** secção, execute os seguintes passos:

    ![Domínio móvel do comando de Tangoe Premium e URLs](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Contacte [equipa de suporte de cliente de Mobile Tangoe comando Premium](https://www.tangoe.com/contact-us/) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Secção do certificado de assinatura SAML](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. Clique em **guardar** botão.

    ![Botão Guardar](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. Sobre o **Tangoe comando Premium Mobile configuração** secção, clique em **Mobile Premium do comando Tangoe configurar** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Secção de configuração de móvel Tangoe comando Premium](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. Para obter SSO configurado para a sua aplicação, contacte o seu [equipa de suporte de cliente de Mobile Tangoe comando Premium](https://www.tangoe.com/contact-us/) e forneça o seguinte:

   - O ficheiro de metadados baixado
   - O **ID de entidade SAML**
   - O **URL do serviço de início de início de sessão único SAML**
   - O **URL de fim de sessão**

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/tangoe-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos -> todos os utilizadores](./media/tangoe-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Adicionar utilizador](./media/tangoe-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Página de diálogo de utilizador](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Criar um utilizador de teste Tangoe comando Premium Mobile

Nesta secção, vai criar um usuário chamado Eduarda Almeida Tangoe comando Premium móvel. 

Aplicativo de Tangoe comando Premium Mobile precisa de todos os utilizadores a ser aprovisionado no aplicativo antes de efetuar o início de sessão único. Pelo que deve trabalho com o [equipa de suporte de cliente de Mobile Tangoe comando Premium](https://www.tangoe.com/contact-us/) para aprovisionar todos esses usuários no aplicativo. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Tangoe comando Premium Mobile.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Tangoe comando Premium Mobile, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Tangoe comando Premium Mobile**.

    ![Tangoe comando Premium Mobile na lista de aplicações](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar suas configurações de SSO do Azure AD utilizando o painel de acesso.

Quando clica no mosaico Tangoe comando Premium Mobile no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação móvel do Tangoe comando Premium. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

