---
title: 'Tutorial: Integração do Azure Ative Directory com a Jobscience Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jobscience.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 715e99f07d0fcbc77fd1769e5da1cae6b46ac97e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850739"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Diretório Ativo Azure com a Jobscience

Neste tutorial, aprende-se a integrar o Jobscience com o Azure Ative Directory (Azure AD).

A integração da Jobscience com a Azure AD proporciona-lhe os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso a Jobscience
- Pode permitir que os seus utilizadores se inscrevam automaticamente na Jobscience (Single Sign-On) com as suas contas AD Azure
- Pode gerir as suas contas numa localização central - o portal Azure

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, veja o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Jobscience, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma assinatura ativada por Jobscience

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente experimental da AD Azure, pode obter um julgamento de um mês aqui: [Oferta de teste](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa a Azure AD um único sinal de acesso em um ambiente de teste. O cenário delineado neste tutorial consiste em dois blocos principais de construção:

1. Adicionar Jobscience da galeria
1. Configurar e testar o Azure AD único sinal de acesso

## <a name="adding-jobscience-from-the-gallery"></a>Adicionar Jobscience da galeria
Para configurar a integração da Jobscience no Azure AD, é necessário adicionar Jobscience da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Jobscience da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.** 

    ![Active Directory][1]

1. Navegue para **aplicações da Enterprise.** Em seguida, vá a **Todas as aplicações**.

    ![O Screenshot mostra o portal Azure Enterprise Applications selecionado em Manage, com todas as aplicações selecionadas.][2]
    
1. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![A screenshot mostra nova aplicação selecionada.][3]

1. Na caixa de pesquisa, **escreva Jobscience.**

    ![Screenshot mostra Adicionar da galeria com a ciência do trabalho inserida.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. No painel de resultados, selecione **Jobscience**e, em seguida, clique em **Adicionar** o botão Adicionar a aplicação.

    ![A screenshot mostra os resultados que incluíam a Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único sinal de acesso
Nesta secção, você configura e testa Azure AD single sign-on com Jobscience baseado em um utilizador de teste chamado "Britta Simon".

Para um único s-on para funcionar, a Azure AD precisa de saber o que o utilizador homólogo em Jobscience é para um utilizador em Azure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Jobscience.

Em Jobscience, atribua o valor do nome de **utilizador** em Azure AD como o valor do Nome de **Utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD com um único sign-on com Jobscience, você precisa completar os seguintes blocos de construção:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. Criar um utilizador de **[teste AZure AD](#creating-an-azure-ad-test-user)** - para testar um único sign-on Azure com Britta Simon.
1. Criar um utilizador de **[teste de Jobscience](#creating-a-jobscience-test-user)** - ter uma contrapartida de Britta Simon em Jobscience que está ligada à representação AZure AD do utilizador.
1. **[Atribuindo o utilizador de teste Azure AD](#assigning-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Testing Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar a Azure AD único sinal de acesso

Nesta secção, você ativa a Azure AD um único sinal no portal Azure e configurar um único sinal na sua aplicação de Jobscience.

**Para configurar o Azure AD com o Jobscience, execute os seguintes passos:**

1. No portal Azure, na página de integração da aplicação **Jobscience,** clique **em 'SÚM'.**

    ![A screenshot mostra um único sinal selecionado sob o comando de Manage no portal Azure.][4]

1. No diálogo **de assinatura único,** selecione **Mode** como    **Sign-on baseado em SAML** para ativar um único sinal de súplica.
 
    ![A screenshot mostra o modo de inscrição baseado em SAML selecionado.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Na secção **Domínio de Jobscience e URLs,** execute os seguintes passos:

    ![A imagem mostra o sinal de U R L.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL Sign-On real. Obtenha este valor pela equipa de [suporte do Cliente Jobscience](http://www.jobscience.com/support) ou pelo perfil SSO que irá criar, o que é explicado mais tarde no tutorial. 
 
1. Na secção **Certificado de Assinatura SAML,** clique em **Certificado (Base64)** e guarde o ficheiro de certificado no seu computador.

    ![A screenshot mostra o painel de certificado de assinatura SAML onde pode descarregar um certificado.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Clique no botão **Guardar.**

    ![A imagem mostra o botão de salvamento.](./media/jobscience-tutorial/tutorial_general_400.png)

1. Na secção configuração de **Trabalhoscience,** clique em **Configurar Ciência** de Emprego para abrir a janela **de inscrição de configuração.** Copie o **URL de inscrição, o ID da Entidade SAML e** o URL de Serviço de Sign-On Único SAML da secção de Referência **Rápida.**

    ![A imagem mostra a janela de configuração da Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Faça login no seu site da empresa jobscience como administrador.

1. Ir para **a Configuração**.
   
   ![A screenshot mostra o item configuração para a sua empresa.](./media/jobscience-tutorial/IC784358.png "Configuração")

1. No painel de navegação à esquerda, na secção Gestão de **Dados,** clique em **Gestão de Domínio** para expandir a secção relacionada e, em seguida, clique em My **Domain** para abrir a página **My Domain.** 
   
   ![Meu Domínio](./media/jobscience-tutorial/ic767825.png "Meu Domínio")

1. Para verificar se o seu domínio foi configurado corretamente, certifique-se de que está no "**Passo 4 Implantado para os Utilizadores**" e reveja as suas "**Definições de Domínio**".

    ![Domínio implantado para utilizador](./media/jobscience-tutorial/ic784377.png "Domínio implantado para utilizador")

1. No site da empresa Jobscience, clique em **Controlos de Segurança**e, em seguida, clique em **Definições de Sign-On Únicas**.
    
    ![A screenshot mostra configurações de Sign-On única selecionadas a partir de controlos de segurança.](./media/jobscience-tutorial/ic784364.png "Controlos de Segurança")

1. Na secção **Definições de Sign-On Única,** execute os seguintes passos:
    
    ![Definições de Sign-On única](./media/jobscience-tutorial/ic781026.png "Definições de Sign-On única")
    
    a. Selecione **SAML Ativado**.

    b. Clique **em Novo**.

1. No **diálogo de Sign-On de definição de definição de saml,** execute os seguintes passos:
    
    ![Configuração de Sign-On única saml](./media/jobscience-tutorial/ic784365.png "Configuração de Sign-On única saml")
    
    a. Na caixa de texto **'Nome',** digite um nome para a sua configuração.

    b. Na caixa de texto **emitente,** cole o valor do ID da **Entidade SAML,** que copiou do portal Azure.

    c. Na caixa de texto **Id da Entidade,** escreva `https://salesforce-jobscience.com`

    d. Clique **em Procurar** para fazer o upload do certificado AZure AD.

    e. Como **Tipo de Identidade SAML,** **selecione Assertion contém o ID da Federação a partir do objeto do Utilizador**.

    f. Como **Local de Identidade SAML,** selecione Identidade está no elemento **NomeIdentfier da declaração do sujeito**.

    exemplo, Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL de serviço de Sign-On único **SAML,** que copiou do portal Azure.

    h. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL de acesso a **assinatura,** que copiou do portal Azure.

    i. Clique em **Guardar**.

1. No painel de navegação à esquerda, na secção Gestão de **Dados,** clique em **Gestão de Domínio** para expandir a secção relacionada e, em seguida, clique em My **Domain** para abrir a página **My Domain.** 
    
    ![Meu Domínio](./media/jobscience-tutorial/ic767825.png "Meu Domínio")

1. Na página **My Domain,** na secção de Marcação de Página de Início de **Sessão,** clique em **Editar**.
    
    ![A screenshot mostra a secção de marcação de página de login com o botão Editar.](./media/jobscience-tutorial/ic767826.png "Marcação de página de login")

1. Na página de **marcação de página de início de sessão,** na secção Serviço de **Autenticação,** é apresentado o nome das suas **Definições SSO SAML.** Selecione-o e, em seguida, clique em **Guardar**.
    
    ![A screenshot mostra a secção de marcação de página de login com PPE e Save selecionados.](./media/jobscience-tutorial/ic784366.png "Marcação de página de login")

1. Para obter o sinal único iniciado SP no URL de início de Sessão clique nas **definições** de sinal único na secção menu **Controlos de Segurança.**

    ![A screenshot mostra a administração de controlos de segurança com configurações de Sign-On única selecionadas.](./media/jobscience-tutorial/ic784368.png "Controlos de Segurança")
    
    Clique no perfil SSO que criou no degrau acima. Esta página mostra o Sinal Único no URL da sua empresa (por exemplo, `https://companyname.my.salesforce.com?so=companyid` .    

> [!TIP]
> Já pode ler uma versão concisa destas instruções dentro do [portal Azure](https://portal.azure.com), enquanto está a configurar a aplicação!  Depois de adicionar esta aplicação a partir da secção **Ative Directory > Aplicações empresariais,** basta clicar no separador **Sign-On único** e aceder à documentação incorporada através da secção **de Configuração** na parte inferior. Pode ler mais sobre a documentação incorporada aqui: [Documentação incorporada AZURE AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD
O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

![Criar utilizador de anúncios Azure][100]

**Para criar um utilizador de teste em Azure AD, execute os seguintes passos:**

1. No **portal Azure**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![A screenshot mostra o ícone Azure A D no portal Azure.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, vá aos **Utilizadores e aos grupos** e clique em **Todos os utilizadores**.
    
    ![O Screenshot mostra utilizadores e grupos selecionados a partir do menu Gerir, com todos os utilizadores selecionados.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Para abrir o diálogo **do Utilizador,** clique em **Adicionar** na parte superior do diálogo.
 
    ![A screenshot mostra o botão Adicionar para abrir a caixa de diálogo do utilizador.](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na página de diálogo do **Utilizador,** execute os seguintes passos:
 
    ![A screenshot mostra a caixa de diálogo do utilizador onde pode introduzir os valores neste passo.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Name,** **digite BrittaSimon**.

    b. Na caixa de texto do nome do **utilizador,** digite o endereço de **e-mail** da BrittaSimon.

    c. Selecione **Mostrar Palavra-Passe** e anota o valor da **Palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jobscience-test-user"></a>Criar um utilizador de teste de Jobscience

Para permitir que os utilizadores de Azure AD iniciem sessão no Jobscience, devem ser adsentes na Jobscience. No caso da Jobscience, o provisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Jobscience ou APIs fornecidas pela Jobscience para fornecer contas de utilizador do Azure Ative Directory.
>  
        
**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa **jobscience** como administrador.

1. Vai para a instalação.
   
   ![A imagem mostra o item de configuração.](./media/jobscience-tutorial/ic784358.png "Configuração")
1. Vá a **Gerir \> utilizadores.**
   
   ![Utilizadores](./media/jobscience-tutorial/ic784369.png "Utilizadores")
1. Clique **em Novo Utilizador**.
   
   ![Todos os Utilizadores](./media/jobscience-tutorial/ic784370.png "All Users")
1. No diálogo do **utilizador editar,** execute os seguintes passos:
   
   ![Edição de Utilizador](./media/jobscience-tutorial/ic784371.png "Edição de Utilizador")
   
   a. Na caixa de texto **Name First,** escreva um primeiro nome do utilizador como Britta.
   
   b. Na caixa de texto **Do Último Nome,** digite um apelido do utilizador como Simon.
   
   c. Na caixa de texto do **Alias,** escreva um nome pseudónimo do utilizador como brittas.

   d. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

   e. Na caixa de texto **do Nome do Utilizador,** digite um nome de utilizador do utilizador como Brittasimon@contoso.com .

   f. Na caixa de texto **nick name,** escreva um nome nick de utilizador como Simon.

   exemplo, Clique em **Guardar**.

    
> [!NOTE]
> O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Jobscience.

![A imagem mostra um nome de exibição de conta.][200] 

**Para atribuir Britta Simon à Jobscience, execute os seguintes passos:**

1. No portal Azure, abra a vista das aplicações e, em seguida, navegue para a vista do diretório e vá para **as aplicações da Enterprise** e, em seguida, clique em Todas as **aplicações**.

    ![O Screenshot mostra aplicações empresariais no menu do portal Azure com todas as aplicações selecionadas.][201] 

1. Na lista de candidaturas, selecione **Jobscience**.

    ![A screenshot mostra o Trabalhoscience selecionado.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. No menu à esquerda, clique em **Utilizadores e grupos**.

    ![O Screenshot mostra utilizadores e grupos selecionados a partir do menu do portal Azure.][202] 

1. Clique no botão **Adicionar.** Em seguida, selecione **Utilizadores e grupos** no diálogo De Atribuição de **Adicionar.**

    ![A screenshot mostra o botão Adicionar, usado para adicionar atribuições.][203]

1. No **diálogo de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores.

1. Clique em **Selecionar o** botão no diálogo **de Utilizadores e grupos.**

1. Clique no botão **Atribuir** o diálogo de atribuição de **adicionar.**
    
### <a name="testing-single-sign-on"></a>Testar um único sinal

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Jobscience no Painel de Acesso, deverá ser automaticamente inscrito na sua aplicação de Jobscience.
Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](tutorial-list.md)
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

