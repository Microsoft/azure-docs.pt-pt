---
title: 'Tutorial: Integração do Active Directory do Azure com Bonusly | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6c82d6a8cf2281ebd77c9880903f8f20505f52c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901156"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração do Active Directory do Azure com Bonusly

Neste tutorial, saiba como integrar Bonusly com o Azure Active Directory (Azure AD).

Integrar Bonusly no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Bonusly
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Bonusly (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Bonusly, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Bonusly logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Bonusly da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando Bonusly da Galeria
Para configurar a integração do Bonusly com o Azure AD, terá de adicionar Bonusly a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Bonusly a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Bonusly**, selecione **Bonusly** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Bonusly na lista de resultados](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Bonusly com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Bonusly a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Bonusly deve ser estabelecido.

Bonusly, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Bonusly, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Bonusly](#create-a-bonusly-test-user)**  - para ter um equivalente da Eduarda Almeida na Bonusly que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Bonusly.

**Para configurar o Azure AD início de sessão único com Bonusly, execute os seguintes passos:**

1. No portal do Azure, sobre o **Bonusly** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. Sobre o **Bonusly domínio e URLs** secção, execute os seguintes passos:

    ![Bonusly URLs de domínio e única informações de início de sessão](./media/bonus-tutorial/tutorial_bonusly_url.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte Bonusly](https://bonus.ly/contact) para obter o valor.
 
1. Na **certificado de assinatura SAML** secção, copie a **THUMBPRINT** valor a partir do certificado.

    ![O link de download de certificado](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/bonus-tutorial/tutorial_general_400.png)

1. Sobre o **configuração Bonusly** secção, clique em **configurar Bonusly** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração bonusly](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. Numa janela do browser diferente, inicie sessão no seu **Bonusly** inquilino.

1. Na barra de ferramentas na parte superior, clique em **configurações**e, em seguida, selecione **integrações e aplicações**.
   
    ![Seção Social bonusly](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Sob **início de sessão único**, selecione **SAML**.

1. Sobre o **SAML** caixa de diálogo página, execute os seguintes passos:
   
    ![Página de diálogo de Saml bonusly](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. Na **URL de destino de SSO de IdP** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.
   
    b. Na **IdP emissor** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 

    c. Na **URL de início de sessão do IdP** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    d. Colar o **Thumbprint** valor copiados a partir do portal do Azure para o **impressão digital do certificado** caixa de texto.
   
1. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/bonus-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/bonus-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/bonus-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/bonus-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-bonusly-test-user"></a>Criar um utilizador de teste Bonusly

Para habilitar os utilizadores do Azure AD iniciar sessão no Bonusly, tem de ser aprovisionados em Bonusly. No caso de Bonusly, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas do utilizador Bonusly conta criação ou APIs fornecidas pelo Bonusly para aprovisionar contas de utilizador do AAD.
>  

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Numa janela do browser web, inicie sessão no seu inquilino Bonusly.

1. Clique em **definições**.
 
    ![As definições](./media/bonus-tutorial/ic781041.png "definições")

1. Clique nas **utilizadores e os bônus** separador.
   
    ![Os utilizadores e os bônus](./media/bonus-tutorial/ic781042.png "utilizadores e os bônus")

1. Clique em **gerir utilizadores**.
   
    ![Gerir utilizadores](./media/bonus-tutorial/ic781043.png "gerir utilizadores")

1. Clique em **adicionar utilizador**.
   
    ![Adicionar utilizador](./media/bonus-tutorial/ic781044.png "adicionar utilizador")

1. Sobre o **adicionar utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Adicionar utilizador](./media/bonus-tutorial/ic781045.png "adicionar utilizador")  

    a. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.
 
    c. Na **E-Mail** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    d. Clique em **Guardar**.
   
     >[!NOTE]
     >O titular da conta do Azure AD recebe uma mensagem de e-mail que inclui uma ligação para confirmar a conta até se tornar Active Directory.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Bonusly.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Bonusly, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Bonusly**.

    ![O Bonusly ligação na lista de aplicativos](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico Bonusly no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Bonusly.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

