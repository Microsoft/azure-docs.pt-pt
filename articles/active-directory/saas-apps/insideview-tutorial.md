---
title: 'Tutorial: Integração do Active Directory do Azure com InsideView | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a315933dcd438c68a1326d2b656c2353e4185f12
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866798"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Active Directory do Azure com InsideView

Neste tutorial, saiba como integrar InsideView com o Azure Active Directory (Azure AD).

Integrar InsideView no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao InsideView
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para InsideView (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com InsideView, terá dos seguintes itens:

- Uma subscrição do Azure
- Um InsideView logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando InsideView da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-insideview-from-the-gallery"></a>Adicionando InsideView da Galeria
Para configurar a integração do InsideView para o Azure AD, terá de adicionar InsideView a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar InsideView a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **InsideView**.

    ![Criar um utilizador de teste do Azure AD](./media/insideview-tutorial/tutorial_insideview_search.png)

1. No painel de resultados, selecione **InsideView**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com InsideView com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no InsideView a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no InsideView deve ser estabelecido.

InsideView, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com InsideView, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste InsideView](#creating-a-insideview-test-user)**  - para ter um equivalente da Eduarda Almeida na InsideView que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo InsideView.

**Para configurar o Azure AD início de sessão único com InsideView, execute os seguintes passos:**

1. No portal do Azure, sobre o **InsideView** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

1. Sobre o **InsideView domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de resposta real. Contacte [equipa de suporte de InsideView](mailto:support@insideview.com) para obter este valor.
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (bruto)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/insideview-tutorial/tutorial_general_400.png)

1. Sobre o **InsideView configuração** secção, clique em **configurar InsideView** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/insideview-tutorial/tutorial_insideview_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa InsideView como administrador.

1. Na barra de ferramentas na parte superior, clique em **administrador**, **SingleSignOn definições**e, em seguida, clique em **SAML adicionar**.
   
   ![Início de sessão único SAML nas configurações](./media/insideview-tutorial/ic794135.png "início de sessão único SAML nas definições")

1. Na **adicionar uma nova SAML** secção, execute os seguintes passos:

    ![Adicionar um novo SAML](./media/insideview-tutorial/ic794136.png "adicionar um novo SAML")
   
    a. Na **nome do STS** caixa de texto, escreva um nome para a sua configuração.

    b. Na **ponto final não solicitadas de SamlP/WS-Fed** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.
    
    c. Abra o seu certificado com codificação base 64, que transferiu do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado STS** caixa de texto.

    d. Na **mapeamento de Id de utilizador do Crm** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. Na **mapeamento de E-Mail de Crm** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na **mapeamento de nome de Crm** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. Na **Crm lastName mapeamento** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/insideview-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/insideview-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/insideview-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/insideview-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-insideview-test-user"></a>Criar um utilizador de teste InsideView

Para ativar a utilizadores do Azure AD iniciar sessão no InsideView, eles têm de ser aprovisionados para InsideView. No caso de InsideView, o aprovisionamento é uma tarefa manual.

Para obter os utilizadores ou contatos criados no InsideView, contacte [equipa de suporte de InsideView](mailto:support@insideview.com).

>[!NOTE]
>Pode utilizar quaisquer outras InsideView utilizador conta criação ferramentas ou APIs fornecidas pelo InsideView para aprovisionar contas de utilizador do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para InsideView.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a InsideView, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **InsideView**.

    ![Configurar o início de sessão único](./media/insideview-tutorial/tutorial_insideview_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico InsideView no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo InsideView.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png

