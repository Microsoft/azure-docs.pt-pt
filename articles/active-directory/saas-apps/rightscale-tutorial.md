---
title: 'Tutorial: Integração do Active Directory do Azure com Rightscale | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c99bc4ef23ee2d8f4478bbaab591e7b36c7ea75
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109836"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Tutorial: Integração do Active Directory do Azure com Rightscale

Neste tutorial, saiba como integrar Rightscale com o Azure Active Directory (Azure AD).

Integrar Rightscale no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Rightscale
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Rightscale (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Rightscale, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Rightscale logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Rightscale da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-rightscale-from-the-gallery"></a>Adicionando Rightscale da Galeria
Para configurar a integração do Rightscale com o Azure AD, terá de adicionar Rightscale a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Rightscale a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Rightscale**.

    ![Criar um utilizador de teste do Azure AD](./media/rightscale-tutorial/tutorial_rightscale_search.png)

1. No painel de resultados, selecione **Rightscale**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Rightscale com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Rightscale a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Rightscale deve ser estabelecido.

Rightscale, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Rightscale, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Rightscale](#creating-a-rightscale-test-user)**  - para ter um equivalente da Eduarda Almeida na Rightscale que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Rightscale.

**Para configurar o Azure AD início de sessão único com Rightscale, execute os seguintes passos:**

1. No portal do Azure, sobre o **Rightscale** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

1. Sobre o **Rightscale domínio e URLs** secção, se desejar configurar a aplicação no **IDP iniciada pelo modo** não é necessário efetuar outros passos de dado que a aplicação já está pré-integrado com o Azure.

    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_url.png)

1. Sobre o **Rightscale domínio e URLs** secção, se desejar configurar a aplicação no **SP iniciada pelo modo**, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Clique nas **Mostrar definições de URL avançadas**.

    b. Na **URL de início de sessão** caixa de texto, escreva o URL: `https://login.rightscale.com/`

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_general_400.png)

1. Sobre o **Rightscale configuração** secção, clique em **configurar Rightscale** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 
   <CS>
1. Para obter SSO configurado para a sua aplicação, terá de iniciar sessão com o seu inquilino RightScale como administrador.

    a. No menu na parte superior, clique nas **configurações** separador e selecione **Single Sign-On**.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Clique em de "**novos**" botão para adicionar **seus fornecedores de identidade de SAML**.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. Na caixa de texto de **nome a apresentar**, introduza o nome da sua empresa.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Selecione **SSO iniciado por permitir RightScale utilizando uma sugestão de deteção** e entrada sua **nome de domínio** na abaixo da caixa de texto.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure em **ponto final de SSO SAML** no RightScale.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Cole o valor de **ID de entidade de SAML** que copiou do portal do Azure em **SAML EntityID** no RightScale.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Clique em **Browser** botão para carregar o certificado que transferiu a partir do portal do Azure.
   
    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Clique em **Guardar**.
   <CE>
   > [!TIP]
   > Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
   > 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/rightscale-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/rightscale-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/rightscale-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-rightscale-test-user"></a>Criar um utilizador de teste Rightscale

Nesta secção, vai criar um usuário chamado Eduarda Almeida no RightScale. Trabalhar com [equipa de suporte de cliente Rightscale](mailto:support@rightscale.com) para adicionar os utilizadores na plataforma RightScale.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Rightscale.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Rightscale, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Rightscale**.

    ![Configurar o início de sessão único](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.  

Quando clica no mosaico RightScale no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo RightScale.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

