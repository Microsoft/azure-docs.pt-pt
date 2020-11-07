---
title: 'Tutorial: Integração do Azure Ative Directory com a Printix Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Printix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: dfde9bbbeb7f6b349ecbdc4c2da605d39a0708da
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357883"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Tutorial: Integração do Diretório Ativo Azure com a Printix

Neste tutorial, aprende-se a integrar o Printix com o Azure Ative Directory (Azure AD).

A integração do Printix com a Azure AD proporciona-lhe os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso a Printix
- Pode permitir que os seus utilizadores se inscrevam automaticamente no Printix (Single Sign-On) com as suas contas AD Azure
- Pode gerir as suas contas numa localização central - o portal Azure

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, veja o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Printix, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma assinatura ativada por Printix

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente de experimentação da AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa a Azure AD um único sinal de acesso em um ambiente de teste. O cenário delineado neste tutorial consiste em dois blocos principais de construção:

1. Adicionar Printix da galeria
1. Configurar e testar o Azure AD único sinal de acesso

## <a name="adding-printix-from-the-gallery"></a>Adicionar Printix da galeria
Para configurar a integração do Printix no Azure AD, é necessário adicionar o Printix da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Printix da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.** 

    ![Active Directory][1]

1. Navegue para **aplicações da Enterprise.** Em seguida, vá a **Todas as aplicações**.

    ![O Screenshot mostra o portal Azure Enterprise Applications selecionado em Manage, com todas as aplicações selecionadas.][2]
    
1. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![A screenshot mostra nova aplicação selecionada.][3]

1. Na caixa de pesquisa, **escreva Printix.**

    ![A screenshot mostra a procura de Printix na Caixa de Diálogo da galeria.](./media/printix-tutorial/tutorial_printix_search.png)

1. No painel de resultados, selecione **Printix** e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

    ![A screenshot mostra a opção Printix selecionada.](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único sinal de acesso
Nesta secção, configura e testa o Azure AD com printix com base num utilizador de teste chamado "Britta Simon".

Para um único sinal de sação a funcionar, o Azure AD precisa de saber o que o utilizador homólogo da Printix é para um utilizador em Azure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Printix.

Na Printix, atribua o valor do nome de **utilizador** em Azure AD como o valor do Nome de **Utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD com o Printix, é necessário completar os seguintes blocos de construção:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. Criar um utilizador de **[teste AZure AD](#creating-an-azure-ad-test-user)** - para testar um único sign-on Azure com Britta Simon.
1. Criar um utilizador de **[teste Printix](#creating-a-printix-test-user)** - ter uma contrapartida de Britta Simon na Printix que está ligada à representação AD AD do utilizador.
1. **[Atribuindo o utilizador de teste Azure AD](#assigning-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Testing Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar a Azure AD único sinal de acesso

Nesta secção, ativa o único sinal de Azure AD no portal Azure e configura um único sinal na sua aplicação Printix.

**Para configurar o Azure AD com a Printix, execute os seguintes passos:**

1. No portal Azure, na página de integração da aplicação **Printix,** clique **em 'SÚM'.**

    ![A screenshot mostra um único sinal selecionado sob o comando de Manage no portal Azure.][4]

1. No diálogo **de assinatura único,** selecione **Mode** como **Sign-on baseado em SAML** para ativar um único sinal de súplica.
 
    ![A screenshot mostra o modo de inscrição baseado em SAML selecionado.](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Na secção **Domínio printix e URLs,** execute os seguintes passos:

    ![A screenshot mostra a secção de Domínio printix e URLs onde pode especificar um U R L para iniciar sposição.](./media/printix-tutorial/tutorial_printix_url.png)

    Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente Printix](mailto:support@printix.net) para obter o valor. 
 
1. Na secção **Certificado de Assinatura SAML,** clique em **Metadata XML** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A screenshot mostra o painel de certificado de assinatura SAML onde pode descarregar um certificado.](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Clique no botão **Guardar.**

    ![Captura de ecrã a mostrar o botão Guardar.](./media/printix-tutorial/tutorial_general_400.png)

1. Inscreva-se no seu inquilino printix como administrador.

1. No menu em cima, clique no ícone no canto superior direito e selecione " **Autenticação** ".
   
    ![A screenshot mostra autenticação selecionada no menu.](./media/printix-tutorial/tutorial_printix_06.png)

1. No **separador Configuração,** selecione **Ativar a autenticação Azure/Office 365**
   
    ![A screenshot mostra a página Printix.net onde pode selecionar a autenticação Enable Azure/Office 365.](./media/printix-tutorial/tutorial_printix_07.png)

1. No **separador Azure,** introduza o URL de metadados da federação para a caixa de texto do " documento de **metadados da Federação** ". 

    Anexe o ficheiro xml de metadados que descarregou da equipa de suporte AZure AD para [a equipa de suporte printix](mailto:support@printix.net). Depois carregam o ficheiro XML e fornecem um URL de metadados da federação.
   
    ![A screenshot mostra a página Printix.net onde pode especificar um documento de metadados da Federação.](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Clique no botão " **Test** " e clique no botão " **OK** " se o teste tiver sido bem sucedido.
   
     A página de diretório ativo Azure mostrará depois de clicar no botão **de teste.** "O teste foi bem sucedido" aqui significa que depois de introduzir as credenciais da sua conta de teste Azure, aparecerá uma mensagem "Definições testadas OK". Em seguida, clique no botão **OK.**
   
    ![A imagem mostra os resultados do teste.](./media/printix-tutorial/tutorial_printix_09.png)

1. Clique no botão **Guardar** na página " **Autenticação".**


> [!TIP]
> Já pode ler uma versão concisa destas instruções dentro do [portal Azure](https://portal.azure.com), enquanto está a configurar a aplicação!  Depois de adicionar esta aplicação a partir da secção **Ative Directory > Aplicações empresariais,** basta clicar no separador **Sign-On único** e aceder à documentação incorporada através da secção **de Configuração** na parte inferior. Pode ler mais sobre a documentação incorporada aqui: [Documentação incorporada AZURE AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD
O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

![Criar utilizador de anúncios Azure][100]

**Para criar um utilizador de teste em Azure AD, execute os seguintes passos:**

1. No **portal Azure** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![A screenshot mostra um nome e nome de utilizador a ser criado.](./media/printix-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, vá aos **Utilizadores e aos grupos** e clique em **Todos os utilizadores**.
    
    ![A screenshot mostra o ícone Azure A D no portal Azure.](./media/printix-tutorial/create_aaduser_02.png) 

1. Para abrir o diálogo **do Utilizador,** clique em **Adicionar** na parte superior do diálogo.
 
    ![O Screenshot mostra utilizadores e grupos selecionados a partir do menu Gerir, com todos os utilizadores selecionados.](./media/printix-tutorial/create_aaduser_03.png) 

1. Na página de diálogo do **Utilizador,** execute os seguintes passos:
 
    ![A screenshot mostra a caixa de diálogo do utilizador onde pode introduzir os valores descritos.](./media/printix-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Name,** **digite BrittaSimon**.

    b. Na caixa de texto do nome do **utilizador,** digite o endereço de **e-mail** da BrittaSimon.

    c. Selecione **Mostrar Palavra-Passe** e anota o valor da **Palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-printix-test-user"></a>Criar um utilizador de teste Printix

O objetivo desta secção é criar um utilizador chamado Britta Simon na Printix. A Printix suporta o provisionamento just-in-time, que é por defeito ativado.

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao Printix se ainda não existir. 

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte da [Printix.](mailto:support@printix.net)
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso ao Printix.

![A screenshot mostra um utilizador com acesso predefinido.][200] 

**Para atribuir Britta Simon à Printix, execute os seguintes passos:**

1. No portal Azure, abra a vista das aplicações e, em seguida, navegue para a vista do diretório e vá para **as aplicações da Enterprise** e, em seguida, clique em Todas as **aplicações**.

    ![O Screenshot mostra aplicações empresariais selecionadas em Manage, com todas as aplicações selecionadas.][201] 

1. Na lista de candidaturas, selecione **Printix**.

    ![A screenshot mostra a lista de aplicações onde pode selecionar Printix.](./media/printix-tutorial/tutorial_printix_app.png) 

1. No menu à esquerda, clique em **Utilizadores e grupos**.

    ![A screenshot mostra utilizadores e grupos selecionados a partir do menu Gerir.][202] 

1. Clique no botão **Adicionar.** Em seguida, selecione **Utilizadores e grupos** no diálogo De Atribuição de **Adicionar.**

    ![A screenshot mostra o botão Adicionar e a página 'Adicionar Atribuição', onde pode selecionar Utilizadores e Grupos.][203]

1. No **diálogo de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores.

1. Clique em **Selecionar o** botão no diálogo **de Utilizadores e grupos.**

1. Clique no botão **Atribuir** o diálogo de atribuição de **adicionar.**
    
### <a name="testing-single-sign-on"></a>Testar um único sinal

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Printix no Painel de Acesso, deverá ser automaticamente inscrito na sua aplicação Printix.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

