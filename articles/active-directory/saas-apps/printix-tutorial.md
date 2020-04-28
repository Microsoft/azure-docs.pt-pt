---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Printix Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "62111458"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Tutorial: Integração do Diretório Ativo Azure com a Printix

Neste tutorial, aprende-se a integrar o Printix com o Azure Ative Directory (Azure AD).

Integrar o Printix com a AD Azure proporciona-lhe os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso ao Printix
- Pode permitir que os seus utilizadores se inscrevam automaticamente no Printix (Single Sign-On) com as suas contas Azure AD
- Você pode gerir suas contas em uma localização central - o portal Azure

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, veja o que é o acesso à aplicação e o único registo com o [Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Printix, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição ativada por um único sinal printix

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o single sign-on Azure AD em um ambiente de teste. O cenário delineado neste tutorial consiste em dois blocos principais de construção:

1. Adicionar Printix da galeria
1. Configuração e teste de um único sinal de Azure AD

## <a name="adding-printix-from-the-gallery"></a>Adicionar Printix da galeria
Para configurar a integração do Printix em Azure AD, precisa de adicionar o Printix da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Printix da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.** 

    ![Active Directory][1]

1. Navegar para **aplicações da Enterprise.** Em seguida, vá a **Todas as aplicações**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, **escreva Printix**.

    ![Criação de um utilizador de teste azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. No painel de resultados, **selecione Printix**, e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Criação de um utilizador de teste azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste de um único sinal de Azure AD
Nesta secção, configura e testa um único sign-on azure com printix com base num utilizador de teste chamado "Britta Simon".

Para um único login funcionar, a Azure AD precisa de saber qual é o utilizador homólogo da Printix para um utilizador em Azure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Printix.

No Printix, atribua o valor do nome de **utilizador** em Azure AD como o valor do nome de **utilizador** para estabelecer a relação de ligação.

Para configurar e testar o único sinal de Azure AD com o Printix, é necessário completar os seguintes blocos de construção:

1. **[Configurar o Single Sign-On do Azure AD](#configuring-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. Criar um utilizador de **[teste Azure AD](#creating-an-azure-ad-test-user)** - para testar o single sign-on azure AD com Britta Simon.
1. Criar um utilizador de **[teste Printix](#creating-a-printix-test-user)** - para ter uma contrapartida de Britta Simon no Printix que está ligada à representação da AD Azure do utilizador.
1. Atribuir o utilizador de **[teste Azure AD](#assigning-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Testar o single sign-on](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o único sign-on da Azure AD

Nesta secção, ativa o único sinal de entrada do Azure AD no portal Azure e configura o único sinal na aplicação Printix.

**Para configurar o único signo da Azure AD com o Printix, execute os seguintes passos:**

1. No portal Azure, na página de integração de aplicações **Printix,** clique em **um único sinal .**

    ![Configurar um único sinal][4]

1. No diálogo **de início de sessão single,** selecione **Mode** como **Sign-on baseado em SAML** para ativar um único sinal.
 
    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Na secção **Printix Domain e URLs,** execute os seguintes passos:

    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_url.png)

    Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte ao [Cliente da Printix](mailto:support@printix.net) para obter o valor. 
 
1. Na secção Certificado de **Assinatura SAML,** clique em **Metadata XML** e, em seguida, guarde o ficheiro metadados no seu computador.

    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Clique no botão **Guardar.**

    ![Configurar um único sinal](./media/printix-tutorial/tutorial_general_400.png)

1. Inscreva-se no seu inquilino da Printix como administrador.

1. No menu em cima, clique no ícone no canto superior direito e selecione "**Autenticação**".
   
    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_06.png)

1. No separador **Configuração,** selecione **autenticação Enable Azure/Office 365**
   
    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_07.png)

1. No separador **Azure,** a federação de metadados de entrada URL para a caixa de texto do documento de**metadados da Federação**". 

    Fixe o ficheiro xml de metadados que descarregou do Azure AD para a equipa de [suporte printix](mailto:support@printix.net). Em seguida, eles carregam o ficheiro xml e fornecem um URL de metadados da federação.
   
    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Clique no botão "**Teste**" e clique em "**OK**" se o teste tiver sucesso.
   
     A página de diretório ativo Azure aparecerá depois de clicar no botão de **teste.** "O teste foi bem sucedido" aqui significa que depois de introduzir as credenciais da sua conta de teste Azure vai aparecer uma mensagem "Definições testadas OK". Em seguida, clique no botão **OK.**
   
    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_09.png)

1. Clique no botão **Guardar** na página **"Autenticação".**


> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal Azure](https://portal.azure.com), enquanto está a configurar a app!  Depois de adicionar esta aplicação a partir da secção Aplicações de > Empresa de **Diretório Ativo,** basta clicar no separador **Sign-On único** e aceder à documentação incorporada através da secção **de Configuração** na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporada aqui: [Documentação incorporada da Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste azure AD
O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

![Criar o Utilizador de Anúncios Azure][100]

**Para criar um utilizador de teste em Azure AD, execute os seguintes passos:**

1. No **portal Azure,** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![Criação de um utilizador de teste azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Para exibir a lista de utilizadores, vá aos **Utilizadores e grupos** e clique em **Todos os utilizadores**.
    
    ![Criação de um utilizador de teste azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Para abrir o diálogo **do Utilizador,** clique em **Adicionar** na parte superior do diálogo.
 
    ![Criação de um utilizador de teste azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Na página de diálogo **do Utilizador,** execute os seguintes passos:
 
    ![Criação de um utilizador de teste azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Name,** **digite BrittaSimon**.

    b. Na caixa de texto **do nome utilizador,** digite o endereço de **e-mail** da BrittaSimon.

    c. Selecione **Mostrar palavra-passe** e anote o valor da **Palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-printix-test-user"></a>Criação de um utilizador de teste Printix

O objetivo desta secção é criar um utilizador chamado Britta Simon em Printix. A Printix suporta o fornecimento just-in-time, que é por defeito habilitado.

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao Printix se ainda não existir. 

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte do Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Printix.

![Utilizador de atribuição][200] 

**Para atribuir Britta Simon à Printix, execute os seguintes passos:**

1. No portal Azure, abra a vista das aplicações e, em seguida, navegue para a vista de diretório e vá para **aplicações da Enterprise** e, em seguida, clique em **Todas as aplicações**.

    ![Utilizador de atribuição][201] 

1. Na lista de aplicações, **selecione Printix**.

    ![Configurar um único sinal](./media/printix-tutorial/tutorial_printix_app.png) 

1. No menu à esquerda, clique em **Utilizadores e grupos.**

    ![Utilizador de atribuição][202] 

1. Clique no botão **Adicionar.** Em seguida, selecione **Utilizadores e grupos** no diálogo **adicionar atribuição.**

    ![Utilizador de atribuição][203]

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores.

1. Clique em **Selecionar** o botão no diálogo **de utilizadores e grupos.**

1. Clique no botão **atribuir** no diálogo **adicionar atribuição.**
    
### <a name="testing-single-sign-on"></a>Testar um único sinal

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Printix no Painel de Acesso, deverá ser automaticamente inscrito na sua aplicação Printix.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](tutorial-list.md)
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

