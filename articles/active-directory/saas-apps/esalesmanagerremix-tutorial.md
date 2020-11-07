---
title: 'Tutorial: Integração do Azure Ative Directory com o E Sales Manager Remix Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o E Sales Manager Remix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358750"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integre o Azure Ative Directy com o E Sales Manager Remix

Neste tutorial, aprende-se a integrar o Azure Ative Directory (Azure AD) com o E Sales Manager Remix.

Ao integrar o Azure AD com o E Sales Manager Remix, obtém os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso ao E Sales Manager Remix.
- Pode permitir que os seus utilizadores se inscrevam automaticamente no E Sales Manager Remix (s-on único, ou SSO) com as suas contas AD Azure.
- Pode gerir as suas contas num local central, o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, veja [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o E Sales Manager Remix, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição ativada pelo E Sales Manager Remix SSO

> [!NOTE]
> Ao testar os passos neste tutorial, recomendamos que *não* utilize um ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente experimental da AD Azure, pode [ter um julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa a Azure AD um único sinal de acesso em um ambiente de teste. 

O cenário delineado neste tutorial consiste em dois blocos principais de construção:

* Adicionar E Sales Manager Remix da galeria
* Configurar e testar o Azure AD único sinal de acesso

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Adicionar E Sales Manager Remix da galeria
Para configurar a integração do AZure AD com o E Sales Manager Remix, adicione o E Sales Manager Remix da galeria à sua lista de aplicações geridas para o SaaS, fazendo o seguinte:

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**. 

    ![O botão Azure Ative Directory][1]

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![A janela "Aplicações da Empresa"][2]
    
1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da janela.

    ![O novo botão de aplicação][3]

1. Na caixa de pesquisa, o Remix do tipo **E Sales Manager** , selecione E Sales Manager **Remix** na lista de resultados e, em seguida, selecione **Add**.

    ![E Sales Manager Remix na lista de resultados](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o E Sales Manager Remix, baseado num utilizador de teste chamado "Britta Simon".

Para um único sinal de sação a funcionar, o Azure AD precisa de identificar o utilizador E Sales Manager Remix e a sua congénere em Azure AD. Por outras palavras, deve ser estabelecida uma relação de ligação entre um utilizador Azure AD e o mesmo utilizador no E Sales Manager Remix.

Para configurar e testar o Azure AD com o E Sales Manager Remix, complete os blocos de construção nas próximas cinco secções:

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Ativar o Azure AD num único sinal no portal Azure e configurar um único sinal na sua aplicação E Sales Manager Remix, fazendo o seguinte:

1. No portal Azure, na página de integração da aplicação **E Sales Manager Remix,** selecione **Single sign-on**.

    ![O link "Single sign-on"][4]

1. Na janela **de inscrição única,** na caixa **de modo de inscrição única,** selecione **Sign-on com base em SAML**.
 
    ![A janela "Single sign-on"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Em **E Sales Manager Remix Domain and URLs,** faça o seguinte:

    ![E Sales Manager Remix Domain e URLs informações únicas de início de súmito](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Na caixa **URL de entrada de inscrição,** digite um URL no seguinte formato: https:// *\<Server-Based-URL> / \<sub-domain> /esales-pc*.

    b. Na caixa **do identificador,** digite um URL no seguinte formato: *\<Server-Based-URL> / \<sub-domain> / https://*.

    c. Note o valor **do Identificador** para posterior utilização neste tutorial.
    
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com o URL de inscrição real e o identificador. Para obter os valores, contacte a [equipa de suporte do Cliente Remix Do Gerente de Vendas da E.](mailto:esupport@softbrain.co.jp)

1. Ao abrigo **do Certificado de Assinatura SAML,** selecione Certificado **(Base64)** e guarde o ficheiro de certificado no seu computador.

    ![O link de descarregamento do Certificado (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Selecione a **Caixa de Verificação de Todos os outros atributos do utilizador** e, em seguida, selecione o atributo **e-mailaddress.**
    
    ![A janela Atributos do Utilizador](./media/esalesmanagerremix-tutorial/configure1.png)

    A janela **Edite Attribute** abre-se.

1. Copie os **valores namespace** e **Name.** Gere o valor no *\<Namespace>/\<Name>* padrão, e guarde-o para posterior utilização neste tutorial.

    ![A janela editar atributo](./media/esalesmanagerremix-tutorial/configure2.png)

1. No **âmbito da configuração do Remix do Gerente de Vendas E** , selecione **Configure E Sales Manager Remix**.

    ![Screenshot que mostra a secção "E Sales Manager Remix Configuration" com "Configure E Sales Manager Remix" selecionada.](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    A janela de inscrição de configuração **abre.-** A janela de inscrição se abre.

1. Na secção **referência rápida,** copie o URL de inscrição e o URL de serviço único SAML.

1. Selecione **Save** (Guardar).

    ![O botão Guardar](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Inscreva-se na sua aplicação E Sales Manager Remix como administrador.

1. No menu superior direito, selecione **Para o Menu de Administrador**.

    ![O comando "Menu de Administrador"](./media/esalesmanagerremix-tutorial/configure4.png)

1. No painel esquerdo, selecione **Definições de Sistema**  >  **Cooperação com sistema externo**.

    ![As ligações "Definições do Sistema" e "Cooperação com o sistema externo"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Na **janela de Cooperação com sistema externo,** selecione **SAML**.

    ![A janela "Cooperação com o sistema externo"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Na **definição de autenticação SAML,** faça o seguinte:

    ![A secção "definição de autenticação SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Selecione a caixa de verificação **da versão pc.**
    
    b. Na secção **de artigos de Colaboração,** na lista de drop-down, selecione **e-mail**.

    c. Na caixa **de item da Colaboração,** cole o valor de reclamação que copiou anteriormente do portal Azure (isto é, **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. Na caixa **emitente (ID da entidade),** cole o valor do identificador que copiou anteriormente a partir da secção **E Sales Manager Remix Domain e URLs** do portal Azure.

    e. Para fazer o upload do certificado descarregado a partir do portal Azure, **selecione Ficheiros**.

    f. Na caixa DE URL de login do **fornecedor de ID,** cole o URL de serviço único de inscrição SAML que copiou anteriormente no portal Azure.

    exemplo, Na caixa **URL do Fornecedor de Identidade,** cole o valor URL de inscrição que copiou anteriormente no portal Azure.

    h. Selecione **Definição completa**.

> [!TIP]
> Ao configurar a aplicação, pode ler uma versão concisa das instruções anteriores no [portal Azure](https://portal.azure.com). Depois de ter adicionado a aplicação na secção Aplicações empresariais do **Diretório Ativo,**  >  **Enterprise Applications** selecione o separador **Sign-On único** e, em seguida, aceda à documentação incorporada na secção **Configuração** na parte inferior. Para obter mais informações sobre a documentação incorporada, consulte [a documentação incorporada da Azure AD.]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria a utilizadora de teste Britta Simon no portal Azure, fazendo o seguinte:

![Criar um utilizador de teste AZure AD][100]

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**.

    ![A ligação Azure Ative Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Para apresentar uma lista de utilizadores atuais, selecione **Utilizadores e grupos**  >  **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Na parte superior da janela **Todos os Utilizadores,** selecione **Adicionar**.

    ![O botão Adicionar](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A janela **do utilizador** abre-se.

1. Na janela **do Utilizador,** faça o seguinte:

    ![A janela do utilizador](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome,** **digite BrittaSimon.**

    b. Na caixa **de nome do utilizador,** digite o endereço de e-mail do utilizador Britta Simon.

    c. Selecione a caixa **de verificação 'Mostrar palavra-passe'** e, em seguida, note o valor que é apresentado na caixa **palavra-passe.**

    d. Selecione **Criar**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Criar um utilizador de teste E Sales Manager Remix

1. Inscreva-se na sua aplicação E Sales Manager Remix como administrador.

1. Selecione Para o **Menu de Administrador** a partir do menu no topo direito.

    ![Configuração remix do gerente de vendas do E](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecione **as definições da sua empresa** Manutenção de  >  **departamentos e funcionários** , e, em seguida, selecione **Funcionários registados**.

    ![O separador "Empregados registados"](./media/esalesmanagerremix-tutorial/user1.png)

1. Na nova secção **de registo de funcionários,** faça o seguinte:
    
    ![A secção "Novo Registo de Funcionários"](./media/esalesmanagerremix-tutorial/user2.png)

    a. Na caixa **nome do empregado,** digite o nome do utilizador (por exemplo, **Britta).**

    b. Complete os restantes campos necessários.
    
    c. Se ativar o SAML, o administrador não pode iniciar súmis na página de inscrição. Conceder privilégios de entrada de administrador ao utilizador selecionando a caixa de verificação **de Login Admin.**

    d. Selecione **Registo**.

1. No futuro, para se inscrever como administrador, inscreva-se como o utilizador que tem permissões de administrador e, em seguida, no topo direito, selecione Para o **Menu de Administrador**.

    ![O comando "Menu de Administrador"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite ao utilizador Britta Simon utilizar o Azure single sign-on, permitindo o acesso ao E Sales Manager Remix. Para tal, faça o seguinte: 

![Atribuir a função de utilizador][200] 

1. No portal Azure, abra a vista **applications,** vá à vista do **Diretório** e, em seguida, selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![As ligações "Aplicações da Empresa" e "Todas as aplicações"][201] 

1. Na lista **de Aplicações,** selecione **E Sales Manager Remix**.

    ![O link remix do gerente de vendas E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. No painel esquerdo, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. **Selecione Adicionar** e, em seguida, no painel **de atribuição de adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição de adição][203]

1. Na janela **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **Britta Simon**.

1. Selecione o botão **Selecionar**.

1. Na janela **'Adicionar Atribuição',** selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo E Sales Manager Remix no Painel de Acesso, deverá ser assinado automaticamente na aplicação E Sales Manager Remix.

Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md) 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre a integração de apps saaS com diretório ativo Azure](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png