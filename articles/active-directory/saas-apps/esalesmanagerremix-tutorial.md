---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Remix do Gestor de Vendas E Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65406963"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrar o Diretório Ativo Azure com o Remix do Gestor de Vendas E

Neste tutorial, aprende-se a integrar o Azure Ative Directory (Azure AD) com o E Sales Manager Remix.

Ao integrar o Azure AD com o E Sales Manager Remix, obtém os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso ao E Sales Manager Remix.
- Pode permitir que os seus utilizadores se inscrevam automaticamente no E Sales Manager Remix (insessão individual, ou SSO) com as suas contas Azure AD.
- Pode gerir as suas contas num local central, o portal Azure.

Para saber mais sobre a integração de apps saaS com a Azure AD, veja [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Remix do Gestor de Vendas E, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição ativada por E Sales Manager SSO

> [!NOTE]
> Quando testar os passos deste tutorial, recomendamos que *não* utilize um ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode [ter um julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o single sign-on Azure AD em um ambiente de teste. 

O cenário delineado neste tutorial consiste em dois blocos principais de construção:

* Adicionar E Sales Manager Remix da galeria
* Configuração e teste de um único sinal de Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Adicione E Sales Manager Remix da galeria
Para configurar a integração do Azure AD com o E Sales Manager Remix, adicione o Remix do Gestor de Vendas E da galeria à sua lista de aplicações geridas do SaaS, fazendo o seguinte:

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**. 

    ![O botão Azure Ative Directory][1]

1. Selecione **aplicações** > Enterprise**Todas as aplicações**.

    ![A janela "Aplicações empresariais"][2]
    
1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da janela.

    ![O novo botão de aplicação][3]

1. Na caixa de pesquisa, type **E Sales Manager Remix**, selecione **E Sales Manager Remix** na lista de resultados e, em seguida, selecione **Adicionar**.

    ![E Sales Manager Remix na lista de resultados](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure AD com o E Sales Manager Remix, com base num utilizador de teste chamado "Britta Simon".

Para um único login funcionar, a Azure AD precisa identificar o utilizador E Sales Manager Remix e a sua congénere em Azure AD. Por outras palavras, deve ser estabelecida uma relação de ligação entre um utilizador da AD Azure e o mesmo utilizador no E Sales Manager Remix.

Para configurar e testar o único sign-on da Azure AD com o E Sales Manager Remix, complete os blocos de construção nas próximas cinco secções:

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Ative o único sign-on da Azure AD no portal Azure e configure um único sinal na aplicação E Sales Manager Remix fazendo o seguinte:

1. No portal Azure, na página de integração de aplicações **E Sales Manager Remix,** selecione **Single sign-on**.

    ![O link "Single sign-on"][4]

1. Na janela **de entrada individual,** na caixa **modo de entrada única,** selecione **saml-on baseado em SAML**.
 
    ![A janela "Single sign-on"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Em **E Sales Manager Remix Domain and URLs,** faça o seguinte:

    ![E Sales Manager Remix Domain and URLs informações únicas de inscrição](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Na caixa **de URL sign-on,** escreva um URL no seguinte formato: *\<https://> de URL baseado no servidor/\<subdomínio>/esales-pc*.

    b. Na caixa **de identificação,** digite um URL no seguinte formato: https:// *\<> de url baseado no servidor/> subdomínio/\<*.

    c. Note o valor **do Identificador** para posterior utilização neste tutorial.
    
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com o URL de inscrição real e identificador. Para obter os valores, contacte a equipa de suporte ao [Cliente Remix Do Gestor](mailto:esupport@softbrain.co.jp)de Vendas E.

1. Em **certificado de assinatura SAML,** selecione Certificado **(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de descarregamento do Certificado (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Selecione o **'Ver' e edite todos os outros atributos** do utilizador verificar a caixa e, em seguida, selecione o atributo do **endereço de e-mail.**
    
    ![A janela de atributos do utilizador](./media/esalesmanagerremix-tutorial/configure1.png)

    A janela **Edit Attribute** abre.

1. Copie os **valores** do Espaço de Nome e **nome.** Gere o valor no padrão * \<\<Namespace>/ Nome>, *e guarde-o para posterior utilização neste tutorial.

    ![A janela do Atributo editar](./media/esalesmanagerremix-tutorial/configure2.png)

1. Sob **a configuração de remix do Gestor**de Vendas E, selecione **Configure E Sales Manager Remix**.

    ![E Sales Manager Remix Configuração](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    A janela **de inscrição configuração** abre.

1. Na secção **De Referência Rápida,** copie o URL de inscrição e o URL de serviço de inscrição único SAML.

1. Selecione **Guardar**.

    ![O botão Guardar](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Inscreva-se na sua aplicação E Sales Manager Remix como administrador.

1. Na parte superior direita, selecione **Menu para Administrador**.

    ![O comando "Para Administrador"](./media/esalesmanagerremix-tutorial/configure4.png)

1. No painel esquerdo, selecione **Definições** > do sistema**Cooperação com sistema externo**.

    ![As ligações "Definições do sistema" e "Cooperação com sistema externo"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Na Cooperação com a janela do **sistema externo,** selecione **SAML**.

    ![A janela "Cooperação com sistema externo"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Sob a definição de **autenticação SAML,** faça o seguinte:

    ![A secção "Definição de autenticação SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Selecione a caixa de verificação da **versão para PC.**
    
    b. Na secção de artigos de **Colaboração,** na lista de entrega, selecione **e-mail**.

    c. Na caixa de artigos de **Colaboração,** colhe o valor de reclamação **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**que copiou anteriormente do portal Azure (isto é, ).

    d. Na caixa **Emitente (ID entidade),** colhe o valor do identificador que copiou anteriormente da secção **E Sales Manager Remix Domain e URLs** do portal Azure.

    e. Para fazer o upload do seu certificado descarregado a partir do portal Azure, selecione **a seleção**de Ficheiros .

    f. Na caixa DE URL do fornecedor de **identificação,** colá o URL de serviço de inscrição única SAML que copiou anteriormente no portal Azure.

    g. Na caixa URL do Fornecedor de **Identidade,** colhe o valor URL de inscrição que copiou anteriormente no portal Azure.

    h. Selecione **Definição completa**.

> [!TIP]
> À medida que está a configurar a aplicação, pode ler uma versão concisa das instruções anteriores no [portal Azure](https://portal.azure.com). Depois de ter adicionado a aplicação na secção > **aplicações** da Empresa de **Diretório Ativo,** selecione o separador **Sign-On único** e aceda à documentação incorporada na secção **de Configuração** na parte inferior. Para obter mais informações sobre a funcionalidade de documentação incorporada, consulte a [documentação incorporada da Azure AD.]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se a utilizadora de teste Britta Simon no portal Azure fazendo o seguinte:

![Criar um utilizador de teste Azure AD][100]

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**.

    ![A ligação Azure Ative Diretório](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Para apresentar uma lista de utilizadores atuais, selecione **Utilizadores e grupos** > **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Na parte superior da janela **All Users,** selecione **Adicionar**.

    ![O botão Adicionar](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A janela **do Utilizador** abre-se.

1. Na janela **Utilizador,** faça o seguinte:

    ![A janela utilizador](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Na caixa **de nomes,** **digite BrittaSimon.**

    b. Na caixa de nome sinuoso, digite o endereço de e-mail da utilizadora Britta Simon. **User name**

    c. Selecione a caixa de verificação **de palavra-passe do Show** e, em seguida, note o valor que está visualizado na caixa **password.**

    d. Selecione **Criar**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Criar um utilizador de teste Remix Do Gestor de Vendas E

1. Inscreva-se na sua aplicação E Sales Manager Remix como administrador.

1. Selecione **Menu para administrador** a partir do menu no topo direito.

    ![E Sales Manager Remix Configuração](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecione **as definições** > da sua empresa**Manutenção de departamentos e funcionários,** e depois selecione **Colaboradores registados**.

    ![O separador "Empregados registados"](./media/esalesmanagerremix-tutorial/user1.png)

1. Na secção de registo de **novos empregados,** faça o seguinte:
    
    ![A secção "Novo Registo de Trabalhadores"](./media/esalesmanagerremix-tutorial/user2.png)

    a. Na caixa **nome do empregado,** digite o nome do utilizador (por exemplo, **Britta**).

    b. Complete os restantes campos necessários.
    
    c. Se ativar o SAML, o administrador não pode iniciar sessão na página de sessão. Conceda privilégios de entrada de administrador ao utilizador selecionando a caixa de **verificação de login do Administrador.**

    d. Selecione **Inscrição**.

1. No futuro, para iniciar sessão como administrador, inscreva-se como utilizador que tenha permissões de administrador e, em seguida, no topo direito, selecione **Menu para Administrador**.

    ![O comando "Para Administrador"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a utilizadora Britta Simon utilize um único sign-on azure, concedendo acesso ao E Sales Manager Remix. Para isso, faça o seguinte: 

![Atribuir a função de utilizador][200] 

1. No portal Azure, abra a vista **Aplicações,** vá à vista do **Diretório** e, em seguida, selecione **aplicações** > Da Enterprise**Todas as aplicações**.

    ![As ligações "Aplicações da Empresa" e "Todas as aplicações"][201] 

1. Na lista de **Aplicações,** selecione **E Sales Manager Remix**.

    ![A ligação E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. No painel esquerdo, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"][202]

1. **Selecione Adicionar** e, em seguida, no painel **adicionar atribuição,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar][203]

1. Na janela **Utilizadores e grupos,** na lista **de Utilizadores,** selecione **Britta Simon**.

1. Selecione o botão **Selecionar.**

1. Na janela **Adicionar Atribuição,** selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo E Sales Manager Remix no Painel de Acesso, deve ser inscrito automaticamente na sua aplicação E Sales Manager Remix.

Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](../user-help/active-directory-saas-access-panel-introduction.md)de Acesso . 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre integração de apps SaaS com Diretório Ativo Azure](tutorial-list.md)
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

