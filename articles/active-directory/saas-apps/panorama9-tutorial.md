---
title: 'Tutorial: Integração do Active Directory do Azure com Panorama9 | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc3a42c280a344ef08fa23111df8652b6167cfb0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109904"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração do Active Directory do Azure com Panorama9

Neste tutorial, saiba como integrar Panorama9 com o Azure Active Directory (Azure AD).

Integrar Panorama9 no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Panorama9
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Panorama9 (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Panorama9, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Panorama9 logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Panorama9 da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-panorama9-from-the-gallery"></a>Adicionando Panorama9 da Galeria
Para configurar a integração do Panorama9 com o Azure AD, terá de adicionar Panorama9 a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Panorama9 a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Panorama9**.

    ![Criar um utilizador de teste do Azure AD](./media/panorama9-tutorial/tutorial_panorama9_search.png)

1. No painel de resultados, selecione **Panorama9**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Panorama9 com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Panorama9 a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Panorama9 deve ser estabelecido.

Panorama9, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Panorama9, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Panorama9](#creating-a-panorama9-test-user)**  - para ter um equivalente da Eduarda Almeida na Panorama9 que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Panorama9.

**Para configurar o Azure AD início de sessão único com Panorama9, execute os seguintes passos:**

1. No portal do Azure, sobre o **Panorama9** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

1. Sobre o **Panorama9 domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL como: `https://dashboard.panorama9.com/saml/access/3262`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Panorama9](https://support.panorama9.com) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, copie a **THUMBPRINT** valor do certificado.

    ![Configurar o início de sessão único](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/panorama9-tutorial/tutorial_general_400.png)

1. Sobre o **Panorama9 configuração** secção, clique em **configurar Panorama9** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Panorama9 como um administrador.

1. Na barra de ferramentas na parte superior, clique em **Manage**e, em seguida, clique em **extensões**.
   
   ![As extensões](./media/panorama9-tutorial/ic790023.png "extensões")
1. Sobre o **extensões** caixa de diálogo, clique em **Single Sign-On**.
   
   ![Início de sessão único](./media/panorama9-tutorial/ic790024.png "início de sessão único")
1. Na **definições** secção, execute os seguintes passos:
   
   ![As definições](./media/panorama9-tutorial/ic790025.png "definições")
   
    a. Na **URL de fornecedor de identidade** caixa de texto, cole o valor de **único URL de início de sessão no serviço**, que copiou do portal do Azure.
   
    b. Na **impressão digital de certificado** caixa de texto, colar a **Thumbprint** valor do certificado que copiou do portal do Azure.    
         
1. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/panorama9-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/panorama9-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/panorama9-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-panorama9-test-user"></a>Criar um utilizador de teste Panorama9

Para habilitar logon Panorama9 de utilizadores do Azure AD, tem de ser aprovisionados em Panorama9.  

No caso de Panorama9, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Panorama9** site da empresa como administrador.

1. No menu na parte superior, clique em **Manage**e, em seguida, clique em **utilizadores**.
   
   ![Os utilizadores](./media/panorama9-tutorial/ic790027.png "utilizadores")

1. Na secção utilizadores, clique em **+** para adicionar novo utilizador.

   ![Os utilizadores](./media/panorama9-tutorial/ic790028.png "utilizadores")

1. Aceda à secção de dados de utilizador, escreva o endereço de e-mail de um utilizador válido do Azure Active Directory que pretende aprovisionar para o **E-Mail** caixa de texto.

1. São fornecidos para a secção de utilizadores, clique em **guardar**.
   
> [!NOTE]
> O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Panorama9.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Panorama9, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Panorama9**.

    ![Configurar o início de sessão único](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Panorama9 no painel de acesso, deve obter automaticamente com sessão iniciada a aplicação de Panorama9.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

