---
title: 'Tutorial: Integração do Active Directory do Azure com E Sales Manager Remix | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Remix do Gestor de vendas da I.
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
ms.openlocfilehash: 5f2a3bb666e40f1aafd921d70d93133ca9139b38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406963"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrar o Azure Active Directory E Gestor de vendas Remix

Neste tutorial, saiba como integrar o Azure Active Directory (Azure AD) com E Sales Manager Remix.

Com a integração do Azure AD com E Sales Manager Remix, obtenha os seguintes benefícios:

- Pode controlar no Azure AD que tenha acesso ao E Sales Manager Remix.
- Pode ativar os seus utilizadores obter a sessão iniciada no automaticamente E Sales Manager Remix (início de sessão único, ou SSO) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central, o portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com E Sales Manager Remix, terá dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição E Sales Manager Remix SSO ativado

> [!NOTE]
> Quando testa os passos neste tutorial, é recomendável que faça *não* utilizar um ambiente de produção.

Para testar os passos neste tutorial, siga as seguintes recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando E Sales Manager Remix da Galeria
* Configuração e teste do Azure AD início de sessão único

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Adicionar E Sales Manager Remix a partir da Galeria
Para configurar a integração do Azure AD com Remix do Gestor de vendas da I, adicione E Sales Manager Remix partir da Galeria à sua lista de aplicações geridas do SaaS, fazendo o seguinte:

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão do Azure Active Directory][1]

1. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![A janela de "Aplicações empresariais"][2]
    
1. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da janela.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Remix do Gestor de vendas da i**, selecione **Remix do Gestor de vendas da i** na lista de resultados e, em seguida, selecione **Add**.

    ![E Remix de Gestor de vendas na lista de resultados](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com E Sales Manager Remix, com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa identificar o utilizador E Sales Manager Remix e sua contraparte no Azure AD. Em outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador do Azure AD e o mesmo utilizador em Remix do Gestor de vendas da I.

Para configurar e testar o Azure AD início de sessão único com E Sales Manager Remix, conclua os blocos de construção nas seções a seguir cinco:

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Ativar o Azure AD início de sessão único no portal do Azure e configurar o início de sessão único na sua aplicação E Sales Manager Remix efetuando o seguinte procedimento:

1. No portal do Azure, sobre o **Remix do Gestor de vendas da i** página de integração de aplicações, selecione **início de sessão único**.

    ![A ligação de "Início de sessão único"][4]

1. Na **início de sessão único** janela, na **modo de início de sessão único** caixa, selecione **baseado em SAML logon**.
 
    ![A janela de "Início de sessão único"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Sob **Remix domínio do Gestor de vendas E e URLs**, efetue o seguinte procedimento:

    ![URLs e Remix domínio do Gestor de vendas E únicas início de sessão em informações](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Na **URL de início de sessão** caixa, escreva um URL no seguinte formato: *https://\<Server-com base-URL > /\<subdomínio > / esales pc*.

    b. Na **identificador** caixa, escreva um URL no seguinte formato: *https://\<servidor-com base-URL > /\<subdomínio > /*.

    c. Tenha em atenção a **identificador** valor para utilização posterior neste tutorial.
    
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-as com o URL de início de sessão real e o identificador. Para obter os valores, contacte [equipa de suporte de cliente E vendas Manager Remix](mailto:esupport@softbrain.co.jp).

1. Sob **certificado de assinatura SAML**, selecione **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Selecione o **ver e editar todos os outros atributos de utilizador** caixa de verificação e, em seguida, selecione a **emailaddress** atributo.
    
    ![A janela de atributos de utilizador](./media/esalesmanagerremix-tutorial/configure1.png)

    O **Editar atributo** é aberta a janela.

1. Copiar o **espaço de nomes** e **nome** valores. Gerar o valor no padrão  *\<espaço de nomes > /\<Name >* e guarde-o para utilização posterior neste tutorial.

    ![A janela de Editar atributo](./media/esalesmanagerremix-tutorial/configure2.png)

1. Sob **Remix configuração do Gestor de vendas i**, selecione **configurar E Sales Manager Remix**.

    ![Configuração de Remix do Gestor de vendas E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    O **configurar o início de sessão** é aberta a janela.

1. Na **referência rápida** secção, copie o URL de fim de sessão e o URL do serviço de início de início de sessão único SAML.

1. Selecione **Guardar**.

    ![O botão salvar](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Inicie sessão na sua aplicação E Sales Manager Remix como administrador.

1. Na parte superior direita, selecione **para o Menu de administrador**.

    ![O comando "Para o administrador Menu"](./media/esalesmanagerremix-tutorial/configure4.png)

1. No painel esquerdo, selecione **definições do sistema** > **cooperação com o sistema externo**.

    ![A "Definições do sistema" e os links de "Cooperação com o sistema externo"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Na **cooperação com o sistema externo** janela, selecione **SAML**.

    ![A janela de "Cooperação com o sistema externo"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Sob **definição de autenticação SAML**, efetue o seguinte procedimento:

    ![A secção de "Definição de autenticação de SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Selecione o **versão de PC** caixa de verificação.
    
    b. Na **item de colaboração** secção, na lista pendente, selecione **e-mail**.

    c. Na **item de colaboração** caixa, cole o valor de afirmação que copiou anteriormente no portal do Azure (ou seja, **https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. Na **emissor (ID de entidade)** caixa, cole o valor do identificador que copiou anteriormente do **Remix domínio do Gestor de vendas E e URLs** seção do portal do Azure.

    e. Para carregar o certificado transferido do portal do Azure, selecione **seleção de ficheiros**.

    f. Na **URL de início de sessão do fornecedor de ID** caixa, cole o URL do serviço de início de início de sessão único SAML que copiou anteriormente no portal do Azure.

    g. Na **URL de fim de sessão do fornecedor de identidade** caixa, cole o valor de URL de fim de sessão que copiou anteriormente no portal do Azure.

    h. Selecione **definição completa**.

> [!TIP]
> Como estamos configurando o aplicativo, pode ler uma versão concisa das instruções anteriores no [portal do Azure](https://portal.azure.com). Depois de adicionar a aplicação no **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador e, em seguida, aceder a documentação em Embedded a **configuração** seção na parte inferior. Para obter mais informações sobre a funcionalidade de documentação do embedded, consulte [documentação do embedded do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar o utilizador de teste Eduarda Almeida no portal do Azure, fazendo o seguinte:

![Criar um utilizador de teste do Azure AD][100]

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**.

    ![A ligação do Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Para apresentar uma lista de utilizadores atuais, selecione **utilizadores e grupos** > **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Na parte superior a **todos os utilizadores** janela, selecione **Add**.

    ![Botão Adicionar](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    O **utilizador** é aberta a janela.

1. Na **utilizador** janela, efetue o seguinte procedimento:

    ![A janela de utilizador](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Selecione **Criar**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Criar um utilizador de teste E Sales Manager Remix

1. Inicie sessão seu aplicativo E Sales Manager Remix como administrador.

1. Selecione **para o Menu de administrador** no menu na parte superior direita.

    ![Configuração de Remix do Gestor de vendas E](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecione **definições da sua empresa** > **manutenção dos departamentos e funcionários**e, em seguida, selecione **funcionários registados**.

    ![O separador "Funcionários registered"](./media/esalesmanagerremix-tutorial/user1.png)

1. Na **novo registo de funcionário** secção, efetue o seguinte:
    
    ![O "registo de funcionário novo" secção](./media/esalesmanagerremix-tutorial/user2.png)

    a. Na **nome do funcionário** , escreva o nome do utilizador (por exemplo, **Eduarda**).

    b. Conclua os campos obrigatórios restantes.
    
    c. Se ativar SAML, o administrador não pode iniciar sessão a partir da página de início de sessão. Concessão início de sessão privilégios de administrador para o utilizador ao selecionar o **início de sessão de administrador** caixa de verificação.

    d. Selecione **registo**.

1. No futuro, para iniciar sessão como administrador, inicie sessão como o utilizador que tem permissões de administrador e, em seguida, na parte superior direita, selecione **para o Menu de administrador**.

    ![O comando "Para o administrador Menu"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar utilizador Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à I Sales Manager Remix. Para tal, faça o seguinte: 

![Atribuir a função de utilizador][200] 

1. No portal do Azure, abra a **aplicativos** modo de exibição, vá para o **diretório** ver e, em seguida, selecione **aplicações empresariais** > **todos os aplicativos**.

    ![A "Aplicações empresariais" e os links de "Todas as aplicações"][201] 

1. Na **aplicativos** lista, selecione **Remix do Gestor de vendas da i**.

    ![A ligação E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. No painel esquerdo, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Selecione **Add** e, em seguida, no **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

1. Na **utilizadores e grupos** janela, na **utilizadores** lista, selecione **Eduarda Almeida**.

1. Selecione o **selecione** botão.

1. Na **adicionar atribuição** janela, selecione **atribuir**.
    
### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD utilizando o painel de acesso.

Ao selecionar o mosaico E Sales Manager Remix no painel de acesso, deve estar conectado automaticamente à sua aplicação E Sales Manager Remix.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
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

