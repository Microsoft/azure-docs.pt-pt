---
title: 'Tutorial: Integração do Active Directory do Azure com Picturepark | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b458632ddb6e23ab1ddf642752aa0cbfc11f1ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839073"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Active Directory do Azure com Picturepark

Neste tutorial, saiba como integrar Picturepark com o Azure Active Directory (Azure AD).

Integrar Picturepark no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Picturepark
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Picturepark (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Picturepark, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Picturepark logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Picturepark da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-picturepark-from-the-gallery"></a>Adicionando Picturepark da Galeria
Para configurar a integração do Picturepark com o Azure AD, terá de adicionar Picturepark a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Picturepark a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Picturepark**.

    ![Criar um utilizador de teste do Azure AD](./media/picturepark-tutorial/tutorial_picturepark_search.png)

1. No painel de resultados, selecione **Picturepark**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Picturepark com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Picturepark a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Picturepark deve ser estabelecido.

Picturepark, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Picturepark, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Picturepark](#creating-a-picturepark-test-user)**  - para ter um equivalente da Eduarda Almeida na Picturepark que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Picturepark.

**Para configurar o Azure AD início de sessão único com Picturepark, execute os seguintes passos:**

1. No portal do Azure, sobre o **Picturepark** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

1. Sobre o **Picturepark domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.picturepark.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Picturepark](https://picturepark.com/about/contact/) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, copie a **THUMBPRINT** valor do certificado.

    ![Configurar o início de sessão único](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/picturepark-tutorial/tutorial_general_400.png)

1. Sobre o **Picturepark configuração** secção, clique em **configurar Picturepark** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Picturepark como um administrador.

1. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **Console de gerenciamento**.
   
    ![Console de gerenciamento](./media/picturepark-tutorial/ic795062.png "consola de gestão")

1. Clique em **autenticação**e, em seguida, clique em **fornecedores de identidade**.
   
    ![Autenticação](./media/picturepark-tutorial/ic795063.png "autenticação")

1. Na **configuração do fornecedor de identidade** secção, execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/picturepark-tutorial/ic795064.png "configuração do fornecedor de identidade")
   
    a. Clique em **Adicionar**.
  
    b. Escreva um nome para a sua configuração.
   
    c. Selecione **predefinir**.
   
    d. Na **URI de emissor** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.
   
    e. Na **impressão de Thumb de emissor fidedigno** caixa de texto, cole o valor de **Thumbprint** que copiou do **certificado de assinatura SAML** secção. 

1. Clique em **JoinDefaultUsersGroup**.

1. Para definir o **Emailaddress** atributo a **afirmação** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e clique em **guardar**.

      ![Configuração](./media/picturepark-tutorial/ic795065.png "configuração")

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/picturepark-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/picturepark-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/picturepark-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-picturepark-test-user"></a>Criar um utilizador de teste Picturepark

Para habilitar logon Picturepark de utilizadores do Azure AD, tem de ser aprovisionados em Picturepark. No caso de Picturepark, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Picturepark** inquilino.

1. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **utilizadores**.
   
    ![Os utilizadores](./media/picturepark-tutorial/ic795067.png "utilizadores")

1. Na **descrição geral de utilizadores** separador, clique em **New**.
   
    ![Gestão de utilizadores](./media/picturepark-tutorial/ic795068.png "gestão de utilizadores")

1. Sobre o **criar utilizador** caixa de diálogo, execute os seguintes passos de um utilizador válido do Azure Active Directory Directory que pretende aprovisionar:
   
    ![Criar utilizador](./media/picturepark-tutorial/ic795069.png "criar utilizador")
   
    a. Na **endereço de E-Mail** caixa de texto, tipo a **endereço de e-mail** do utilizador **BrittaSimon\@contoso.com**.  
   
    b. Na **palavra-passe** e **Confirmar palavra-passe** caixas de texto, tipo o **palavra-passe** de BrittaSimon. 
   
    c. Na **nome próprio** caixa de texto, tipo a **nome próprio** do utilizador **Eduarda**. 
   
    d. Na **Apelido** caixa de texto, tipo a **Apelido** do utilizador **Simon**.
   
    e. Na **empresa** caixa de texto, tipo a **nome da empresa** do utilizador. 
   
    f. Na **país** caixa de texto, selecione a **país** do utilizador.
  
    g. Na **ZIP** caixa de texto, tipo a **CEP** da cidade.
   
    h. Na **Cidade** caixa de texto, tipo a **nome da cidade** do utilizador.

    i. Selecione um **linguagem**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Pode utilizar quaisquer outras Picturepark utilizador conta criação ferramentas ou APIs fornecidas pelo Picturepark para aprovisionar contas de utilizador do Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Picturepark.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Picturepark, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Picturepark**.

    ![Configurar o início de sessão único](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Picturepark no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Picturepark. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

