---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Jobscience Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f0ea5b922b2c958aabf5be3a6123bb81a8f0234
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048509"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Diretório Ativo Azure com a Jobscience

Neste tutorial, aprende-se a integrar a Ciência do Trabalho com o Azure Ative Directory (Azure AD).

Integrar a Ciência do Trabalho com a AD Azure proporciona-lhe os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso a Jobscience
- Pode permitir que os seus utilizadores se inscrevam automaticamente na Jobscience (Single Sign-On) com as suas contas Azure AD
- Você pode gerir suas contas em uma localização central - o portal Azure

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, veja o que é o acesso à aplicação e o único registo com o [Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Jobscience, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma assinatura ativada por um único sign-on da Jobscience

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode obter um julgamento de um mês aqui: oferta de [julgamento.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o single sign-on Azure AD em um ambiente de teste. O cenário delineado neste tutorial consiste em dois blocos principais de construção:

1. Adicionando ciência de trabalho da galeria
1. Configuração e teste de um único sinal de Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Adicionando ciência de trabalho da galeria
Para configurar a integração da Jobscience em Azure AD, você precisa adicionar Jobscience da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Jobscience da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.** 

    ![Active Directory][1]

1. Navegar para **aplicações da Enterprise.** Em seguida, vá a **Todas as aplicações**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Jobscience.**

    ![Criação de um utilizador de teste azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. No painel de resultados, selecione **Jobscience**, e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Criação de um utilizador de teste azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste de um único sinal de Azure AD
Nesta secção, configura e testa um único sign-on azure com jobscience com base num utilizador de teste chamado "Britta Simon".

Para um único login funcionar, a Azure AD precisa de saber qual é o utilizador homólogo em Jobscience para um utilizador em Azure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Jobscience.

Na Jobscience, atribua o valor do nome de **utilizador** em Azure AD como o valor do Nome de **Utilizador** para estabelecer a relação de ligação.

Para configurar e testar o único sign-on azure ad com a Jobscience, você precisa completar os seguintes blocos de construção:

1. **[Configurar o Single Sign-On do Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. Criar um utilizador de **[teste Azure AD](#creating-an-azure-ad-test-user)** - para testar o single sign-on azure AD com Britta Simon.
1. **[Criar um utilizador](#creating-a-jobscience-test-user)** de teste de Jobscience - para ter uma contrapartida de Britta Simon em Jobscience que está ligada à representação da AD Azure do utilizador.
1. Atribuir o utilizador de **[teste Azure AD](#assigning-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Testar o single sign-on](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o único sign-on da Azure AD

Nesta secção, permite o único sign-on da Azure AD no portal Azure e configura ruma inscrição na sua aplicação Jobscience.

**Para configurar o único sign-on da Azure AD com a Jobscience, execute os seguintes passos:**

1. No portal Azure, na página de integração de aplicações **de Jobscience,** clique em **um único sign-on**.

    ![Configurar um único sinal][4]

1. No diálogo **de início de sessão single,** selecione **Mode** como **Sign-on baseado em SAML** para ativar um único sinal.
 
    ![Configurar um único sinal](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Na secção Domínio de **Jobscience e URLs,** execute os seguintes passos:

    ![Configurar um único sinal](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de Início de Sinal real. Obtenha este valor pela equipa de suporte do [Cliente jobscience](https://www.jobscience.com/support) ou do perfil SSO que irá criar, o que é explicado mais tarde no tutorial. 
 
1. Na secção **Certificado de Assinatura SAML,** clique no **Certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar um único sinal](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Clique no botão **Guardar.**

    ![Configurar um único sinal](./media/jobscience-tutorial/tutorial_general_400.png)

1. Na secção de Configuração de **Jobscience,** clique em **Configurar a Jobscience** para abrir a janela **de inscrição do Configure.** Copie o URL de Saída, o ID da Entidade SAML e o URL de Serviço De **Assinatura Única SAML** da secção de Referência **Rápida.**

    ![Configurar um único sinal](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Inicie sessão no site da empresa de ciência de emprego como administrador.

1. Vá para a **Configuração.**
   
   ![Configuração](./media/jobscience-tutorial/IC784358.png "Configuração")

1. No painel de navegação à esquerda, na secção **Administração,** clique em **Gestão** de Domínio para expandir a secção relacionada e, em seguida, clique em **My Domain** para abrir a página **My Domain.** 
   
   ![O meu domínio](./media/jobscience-tutorial/ic767825.png "O meu domínio")

1. Para verificar se o seu domínio foi configurado corretamente, certifique-se de que está em "**Passo 4 Implantado para Utilizadores**" e reveja as suas**definições**de domínio " My Domain ".

    ![Domínio implantado para utilizador](./media/jobscience-tutorial/ic784377.png "Domínio implantado para utilizador")

1. No site da empresa jobscience, clique em **Controlos**de Segurança e, em seguida, clique em **Definições de Inscrição Única**.
    
    ![Controlos de Segurança](./media/jobscience-tutorial/ic784364.png "Controlos de Segurança")

1. Na secção Definições de **Sinal único,** execute os seguintes passos:
    
    ![Definições únicas de inscrição](./media/jobscience-tutorial/ic781026.png "Definições únicas de inscrição")
    
    a. Selecione **SAML Ativado**.

    b. Clique em **Novo**.

1. No diálogo de edição de definição de **sinais simples SAML,** execute os seguintes passos:
    
    ![Definição de sinal único SAML](./media/jobscience-tutorial/ic784365.png "Definição de sinal único SAML")
    
    a. Na caixa de texto **Name,** digite um nome para a sua configuração.

    b. Na caixa de texto **Emitida,** cola o valor do ID da **Entidade SAML,** que copiou do portal Azure.

    c. Na caixa de texto **Id entidade,** escreva`https://salesforce-jobscience.com`

    d. Clique em **Navegar** para carregar o certificado Azure AD.

    e. Como Tipo de **Identidade SAML,** selecione **Afirmação contém o ID da Federação do objeto utilizador**.

    f. Como **Localização de Identidade SAML,** selecione Identidade está no elemento **NameIdentfier da declaração do Sujeito**.

    g. Na caixa de texto URL do Fornecedor de **Identidade,** colá o valor do URL de Serviço De **Assinatura Única SAML,** que copiou do portal Azure.

    h. Na caixa de texto URL do Fornecedor de **Identidade Logout,** colhe o valor do URL de **Saída,** que copiou do portal Azure.

    i. Clique em **Guardar**.

1. No painel de navegação à esquerda, na secção **Administração,** clique em **Gestão** de Domínio para expandir a secção relacionada e, em seguida, clique em **My Domain** para abrir a página **My Domain.** 
    
    ![O meu domínio](./media/jobscience-tutorial/ic767825.png "O meu domínio")

1. Na página **My Domain,** na secção **'Marcação de página de login',** clique em **Editar**.
    
    ![Marca de página de login](./media/jobscience-tutorial/ic767826.png "Marca de página de login")

1. Na página de marcação de página de **login,** na secção Serviço de **Autenticação,** é apresentado o nome das **definições SAML SSO.** Selecione-o e, em seguida, clique em **Guardar**.
    
    ![Marca de página de login](./media/jobscience-tutorial/ic784366.png "Marca de página de login")

1. Para obter o SP iniciado Sinal Único no URL de login clique nas **definições de sinal único na** secção de **controlode segurança.**

    ![Controlos de Segurança](./media/jobscience-tutorial/ic784368.png "Controlos de Segurança")
    
    Clique no perfil SSO que criou no passo acima. Esta página mostra o Sinal Único no URL `https://companyname.my.salesforce.com?so=companyid`para a sua empresa (por exemplo, .    

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal Azure](https://portal.azure.com), enquanto está a configurar a app!  Depois de adicionar esta aplicação a partir da secção Aplicações de > Empresa de **Diretório Ativo,** basta clicar no separador **Sign-On único** e aceder à documentação incorporada através da secção **de Configuração** na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporada aqui: [Documentação incorporada da Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste azure AD
O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

![Criar o Utilizador de Anúncios Azure][100]

**Para criar um utilizador de teste em Azure AD, execute os seguintes passos:**

1. No **portal Azure,** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![Criação de um utilizador de teste azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Para exibir a lista de utilizadores, vá aos **Utilizadores e grupos** e clique em **Todos os utilizadores**.
    
    ![Criação de um utilizador de teste azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Para abrir o diálogo **do Utilizador,** clique em **Adicionar** na parte superior do diálogo.
 
    ![Criação de um utilizador de teste azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na página de diálogo **do Utilizador,** execute os seguintes passos:
 
    ![Criação de um utilizador de teste azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Name,** **digite BrittaSimon**.

    b. Na caixa de texto **do nome utilizador,** digite o endereço de **e-mail** da BrittaSimon.

    c. Selecione **Mostrar palavra-passe** e anote o valor da **Palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jobscience-test-user"></a>Criar um utilizador de teste de Jobscience

Para permitir que os utilizadores de Anúncios Azure entrem na Jobscience, devem ser aprovisionados na Jobscience. No caso da Ciência do Trabalho, o provisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Jobscience ou APIs fornecidas pela Jobscience para fornecer contas de utilizadores do Diretório Ativo Azure.
>  
        
**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inicie sessão no site da empresa **de ciência de emprego** como administrador.

1. Vai para a Configuração.
   
   ![Configuração](./media/jobscience-tutorial/ic784358.png "Configuração")
1. Vá para **gerir utilizadores \> utilizadores**.
   
   ![Utilizadores](./media/jobscience-tutorial/ic784369.png "Utilizadores")
1. Clique em **Novo Utilizador**.
   
   ![Todos os Utilizadores](./media/jobscience-tutorial/ic784370.png "All Users")
1. No diálogo Editar **Utilizador,** execute os seguintes passos:
   
   ![Edição do utilizador](./media/jobscience-tutorial/ic784371.png "Edição do utilizador")
   
   a. Na caixa de texto **First Name,** digite um primeiro nome do utilizador como Britta.
   
   b. Na caixa de texto **Last Name,** digite um sobrenome do utilizador como Simon.
   
   c. Na caixa de texto **da Alias,** escreva um pseudónimo do utilizador como brittas.

   d. Na caixa de texto **e-mail,** Brittasimon@contoso.comdigite o endereço de e-mail do utilizador como .

   e. Na caixa de texto nome do **utilizador,** digite um nome de utilizador como Brittasimon@contoso.com.

   f. Na caixa de texto **Nick Name,** escreva um nome nick de utilizador como Simon.

   g. Clique em **Guardar**.

    
> [!NOTE]
> O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do utilizador de teste Azure AD

Nesta secção, permite que Britta Simon use o único sign-on Azure, concedendo acesso à Jobscience.

![Utilizador de atribuição][200] 

**Para atribuir Britta Simon à Jobscience, execute os seguintes passos:**

1. No portal Azure, abra a vista das aplicações e, em seguida, navegue para a vista de diretório e vá para **aplicações da Enterprise** e, em seguida, clique em **Todas as aplicações**.

    ![Utilizador de atribuição][201] 

1. Na lista de candidaturas, selecione **Jobscience**.

    ![Configurar um único sinal](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. No menu à esquerda, clique em **Utilizadores e grupos.**

    ![Utilizador de atribuição][202] 

1. Clique no botão **Adicionar.** Em seguida, selecione **Utilizadores e grupos** no diálogo **adicionar atribuição.**

    ![Utilizador de atribuição][203]

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores.

1. Clique em **Selecionar** o botão no diálogo **de utilizadores e grupos.**

1. Clique no botão **atribuir** no diálogo **adicionar atribuição.**
    
### <a name="testing-single-sign-on"></a>Testar um único sinal

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Jobscience no Painel de Acesso, deve ser automaticamente inscrito na sua aplicação de Jobscience.
Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](../user-help/active-directory-saas-access-panel-introduction.md)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

