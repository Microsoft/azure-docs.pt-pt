---
title: 'Tutorial: Integração do Active Directory do Azure com o Tableau Online | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5e3087c21908600be9cd369a15f3036e5acb2f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884708"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integração do Active Directory do Azure com o Tableau Online

Neste tutorial, saiba como integrar o Tableau Online com o Azure Active Directory (Azure AD).

Integrar o Tableau Online com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD, quem tem acesso a Tableau Online
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Tableau Online (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Tableau Online, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Tableau Online logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Tableau Online a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online a partir da Galeria
Para configurar a integração do Tableau Online com o Azure AD, terá de adicionar Tableau Online na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Tableau Online a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Tableau Online**.

    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_search.png)

1. No painel de resultados, selecione **Tableau Online**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o Tableau Online com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no Tableau Online a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Tableau Online tem de ser estabelecida.

No Tableau Online, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Tableau Online, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Online de Tableau](#creating-a-tableau-online-test-user)**  - para ter um equivalente da Eduarda Almeida Tableau online que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Tableau Online.

**Para configurar o Azure AD início de sessão único com o Tableau Online, execute os seguintes passos:**

1. No portal do Azure, sobre o **Tableau Online** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

1. Sobre o **Tableau de domínio Online e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. Na **URL de início de sessão** caixa de texto, escreva o URL: `https://sso.online.tableau.com`

    b. Na **identificador** caixa de texto, escreva o URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_general_400.png)

1. Numa janela do browser diferente, início de sessão na sua aplicação Tableau Online. Aceda a **configurações** e, em seguida **autenticação**.
   
    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)
    
1. Para ativar SAML, em **tipos de autenticação** secção. Verifique os **início de sessão único com o SAML** caixa de verificação.
   
    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

1. Desloque para baixo até **importar o ficheiro de metadados no Tableau Online** secção.  Clique em Procurar e importar o ficheiro de metadados que transferiu a partir do Azure AD. Em seguida, clique em **aplicar**.
   
   ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

1. Na **corresponde ao asserções** secção, insira o nome de asserção de fornecedor de identidade correspondente para **endereço de e-mail**, **nome**, e **Apelido**. Para obter essas informações do Azure AD: 
  
    a. No portal do Azure, avance os **Tableau Online** página de integração de aplicativo.
    
    b. Na secção de atributos, selecione o **"ver e editar todos os outros atributos de utilizador"** caixa de verificação. 
    
   ![Configurar o início de sessão único](./media/tableauonline-tutorial/attributesection.png)
      
    c. Copie o valor de espaço de nomes para esses atributos: givenname, e-mail e o apelido, utilizando os seguintes passos:

   ![O Azure AD Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Clique em **user.givenname** valor 
    
    e. Copie o valor a partir da **espaço de nomes** caixa de texto.

   ![Configurar o início de sessão único](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar o espaço de nomes de valores para o e-mail e o apelido, siga os passos anteriores.

    g. Mude para o aplicativo Tableau Online, em seguida, configure as **atributos de Online Tableau** secção da seguinte forma:
     * E-mail: **correio** ou **userprincipalname**
     * Nome próprio: **givenname**
     * Apelido: **Apelido**
   
   ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-tableau-online-test-user"></a>Criar um utilizador de teste Tableau Online

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Tableau Online.

1. No **Tableau Online**, clique em **definições** e, em seguida **autenticação** secção. Desloque para baixo até **selecionar utilizadores** secção. Clique em **adicionar utilizadores** e, em seguida **Introduza endereços de E-Mail**.
   
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)
1. Selecione **adicionar utilizadores para a autenticação de início de sessão único (SSO)**. Na **Introduza endereços de E-Mail** adicionar caixa de texto britta.simon@contoso.com
   
    ![Criar um utilizador de teste do Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)
1. Clique em **Criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Tableau Online.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Tableau Online, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Tableau Online**.

    ![Configurar o início de sessão único](./media/tableauonline-tutorial/tutorial_tableauonline_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico Tableau Online no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Tableau Online.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/tableauonline-tutorial/tutorial_general_203.png
