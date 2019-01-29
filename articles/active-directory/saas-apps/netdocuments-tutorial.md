---
title: 'Tutorial: Integração do Active Directory do Azure com NetDocuments | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: f85fb6a628692fc5c0054ac6047980a5ea66cbe3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152507"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração do Active Directory do Azure com NetDocuments

Neste tutorial, saiba como integrar NetDocuments com o Azure Active Directory (Azure AD).

Integrar NetDocuments no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao NetDocuments
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para NetDocuments (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com NetDocuments, terá dos seguintes itens:

- Uma subscrição do Azure
- Um NetDocuments logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando NetDocuments da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionando NetDocuments da Galeria
Para configurar a integração do NetDocuments com o Azure AD, terá de adicionar NetDocuments a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar NetDocuments a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **NetDocuments**.

    ![Criar um utilizador de teste do Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_search.png)

1. No painel de resultados, selecione **NetDocuments**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com NetDocuments com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no NetDocuments a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no NetDocuments deve ser estabelecido.

NetDocuments, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com NetDocuments, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste NetDocuments](#creating-a-netdocuments-test-user)**  - para ter um equivalente da Eduarda Almeida na NetDocuments que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo NetDocuments.

**Para configurar o Azure AD início de sessão único com NetDocuments, execute os seguintes passos:**

1. No portal do Azure, sobre o **NetDocuments** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

1. Sobre o **NetDocuments domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o URL de resposta. Contacte [equipa de suporte de NetDocuments](https://support.netdocuments.com/hc/) obter esses valores.
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/netdocuments-tutorial/tutorial_general_400.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa NetDocuments como um administrador.

1. Aceda a **administrador**.

1. Clique em **adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "repositório")

1. Clique em **configurar opções de autenticação avançadas**.
    
    ![Configurar opções de autenticação avançadas](./media/netdocuments-tutorial/ic795048.png "configurar opções de autenticação avançadas")

1. Sobre o **identidade federada** caixa de diálogo, execute os seguintes passos:
   
    ![Federado Identitty](./media/netdocuments-tutorial/ic795049.png "federado Identitty")
   
    a. Como **federado o tipo de servidor de identidade**, selecione **serviços de Federação do Active Directory**.
   
    b. Clique em **Escolher ficheiro**, para carregar o ficheiro de metadados baixado que transferiu a partir do portal do Azure.
   
    c. Clique em **OK**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/netdocuments-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/netdocuments-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/netdocuments-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/netdocuments-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-netdocuments-test-user"></a>Criar um utilizador de teste NetDocuments

Para ativar a utilizadores do Azure AD iniciar sessão no NetDocuments, tem de ser aprovisionados em NetDocuments.  
No caso de NetDocuments, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Brilhar em sua **NetDocuments** site da empresa como administrador.

1. No menu na parte superior, clique em **administrador**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

1. Clique em **adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "repositório")

1. Na **endereço de E-Mail** caixa de texto, escreva o endereço de e-mail de uma conta válida do Azure Active Directory que pretende aprovisionar e, em seguida, clique em **adicionar utilizador**.
   
    ![Endereço de e-mail](./media/netdocuments-tutorial/ic795053.png "endereço de E-Mail")
   
   >[!NOTE]
   >O titular da conta do Azure Active Directory irá receber uma mensagem de e-mail que inclui uma ligação para confirmar a conta até se tornar Active Directory. Pode utilizar quaisquer outras NetDocuments utilizador conta criação ferramentas ou APIs fornecidas pelo NetDocuments para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para NetDocuments.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a NetDocuments, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **NetDocuments**.

    ![Configurar o início de sessão único](./media/netdocuments-tutorial/tutorial_netdocuments_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico NetDocuments no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo NetDocuments.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/netdocuments-tutorial/tutorial_general_203.png

