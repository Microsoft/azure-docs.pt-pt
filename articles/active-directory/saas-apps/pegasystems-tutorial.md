---
title: 'Tutorial: Integração de Diretório Sonárgico Azure com a Pega Systems [ Microsoft Docs'
description: Neste tutorial, você vai aprender a configurar um único sign-on entre o Azure Ative Directory e a Pega Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72026815"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração de Diretório Ativo Azure com a Pega Systems

Neste tutorial, você vai aprender a integrar a Pega Systems com o Azure Ative Directory (Azure AD).

Esta integração proporciona estes benefícios:

* Pode utilizar a AD Azure para controlar quem tem acesso à Pega Systems.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Pega Systems (única investida) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Pega Systems, é necessário ter:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode [inscrever-se](https://azure.microsoft.com/pricing/free-trial/)para um julgamento de um mês.
* Uma subscrição da Pega Systems que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* A Pega Systems suporta o SSO iniciado por SP e o SSO iniciado pelo IDP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicione sistemas Pega da galeria

Para configurar a integração da Pega Systems em Azure AD, precisa de adicionar a Pega Systems da galeria à sua lista de aplicações geridas do SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza a **Pega Systems**. Selecione **Sistemas Pega** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sign-on azure ad com a Pega Systems utilizando um utilizador de teste chamado Britta Simon.
Para permitir uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente na Pega Systems.

Para configurar e testar o único sinal de Azure AD com a Pega Systems, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure](#configure-pega-systems-single-sign-on)** a pega Systems única inscrição no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador](#create-a-pega-systems-test-user)** de teste Pega Systems que esteja ligado à representação da AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a Pega Systems, tome estas medidas:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Pega Systems,** selecione **Single sign-on:**

    ![Selecione um único sinal on](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado pelo IDP, complete os seguintes passos.

    ![Caixa de diálogo de configuração SAML básica](common/idp-intiated.png)

    1. Na caixa **de identificador,** introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na caixa **DEURL resposta,** introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se pretender configurar a aplicação no modo iniciado por SP, selecione **Definir URLs adicionais** e completar os seguintes passos.

    ![Pega Systems Domain e URLs informações únicas de inscrição](common/both-advanced-urls.png)

    1. Na caixa **de URL Sign on,** introduza o sinal no valor URL.

    1. Na caixa **de Retransmissão,** introduza um URL neste padrão:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores aqui fornecidos são espaços reservados. Você precisa usar o identificador real, url de resposta, assinar no URL e retransmitir URL do estado. Pode obter os valores de URL do identificador e resposta a partir de uma aplicação Pega, como explicado mais tarde neste tutorial. Para obter o valor do estado de retransmissão, contacte a equipa de suporte da [Pega Systems.](https://www.pega.com/contact-us) Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. A aplicação Pega Systems necessita que as afirmações da SAML estejam num formato específico. Para os colocar no formato correto, precisa adicionar mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra os atributos padrão. Selecione o ícone **Editar** para abrir a caixa de diálogo **de Atributos do Utilizador:**

    ![Atributos do utilizador](common/edit-attribute.png)

7. Além dos atributos mostrados na imagem anterior, a aplicação Pega Systems requer que mais alguns atributos sejam retransmitidos na resposta SAML. Na secção de **reclamações** do Utilizador da caixa de diálogo **Deatributos** do Utilizador, complete os seguintes passos para adicionar estes atributos de token SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Estes valores são específicos da sua organização. Forneça os valores adequados.

    1. **Selecione Adicionar nova alegação** para abrir a caixa de diálogo **de reclamações do utilizador:**

    ![Selecione Adicionar nova reclamação](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de reclamações do utilizador](common/new-attribute-details.png)

    1. Na caixa **nome,** introduza o nome do atributo mostrado para aquela linha.

    1. Deixe a caixa **namespace** vazia.

    1. Para a **Fonte,** selecione **Atributo**.

    1. Na lista de **atributos Fonte,** selecione o valor do atributo mostrado para essa linha.

    1. Selecione **Ok**.

    1. Selecione **Guardar**.

8. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** ao lado dos **Metadados da Federação XML,** de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/metadataxml.png)

9. Na secção **Configurar sistemas pega,** copie os URLs apropriados, com base nos seus requisitos.

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.

### <a name="configure-pega-systems-single-sign-on"></a>Configure Pega Systems única sign-on

1. Para configurar um único sinal no lado da **Pega Systems,** inscreva-se no Portal Pega com uma conta de administrador noutra janela do navegador.

2. Selecione **Criar** > o Serviço de**Autenticação****SysAdmin:** > 

    ![Selecione Serviço de Autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Complete os seguintes passos no ecrã do **Serviço de Autenticação Criar.**

    ![Criar ecrã do Serviço de Autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na lista **tipo,** selecione **SAML 2.0**.

    1. Na caixa **Nome,** introduza qualquer nome (por exemplo, **Azure AD SSO**).

    1. Na caixa de **descrição curta,** introduza uma descrição.  

    1. Selecione **Criar e abrir**.
    
4. Na secção de informação do Fornecedor de **Identidade (IDP),** selecione **metadados IdP de importação** e navegue no ficheiro de metadados que descarregou do portal Azure. Clique **em Submeter** para carregar os metadados:

    ![Secção de informação do Fornecedor de Identidade (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    A importação preencherá os dados do IDP, como mostrado aqui:

    ![Dados idp importados](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Complete os seguintes passos na secção de definições do Prestador de **Serviços (SP).**

    ![Definições do prestador de serviços](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Copie o valor de **identificação** da entidade e cole-o na caixa **de identificação** na secção **de Configuração SAML Básica** no portal Azure.

    1. Copie o valor de localização do Serviço de **Consumidores de Afirmação (ACS)** e cole-o na caixa URL de **resposta** na secção **de Configuração SAML Básica** no portal Azure.

    1. Selecione **Desativar a assinatura de pedido**.

7. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Azure Ative Directory** no painel esquerdo, selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores:**

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** complete os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    a. Na caixa **de nomes,** entre **brittaSimon.**
  
    b. Na caixa de **nomes do Utilizador,** introduza **\<brittasimon@> de domínio da\< sua empresa.>de extensão. ** (Por exemplo, BrittaSimon@contoso.com.)

    c. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está na caixa **password.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo-lhe acesso à Pega Systems.

1. No portal Azure, selecione **aplicações Enterprise,** selecione **Todas as aplicações,** e, em seguida, selecione **Pega Systems**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Pega Systems**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Select** na parte inferior do ecrã.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-pega-systems-test-user"></a>Criar um utilizador de teste pega Systems

Em seguida, é necessário criar uma utilizadora chamada Britta Simon na Pega Systems. Trabalhe com a equipa de suporte da [Pega Systems](https://www.pega.com/contact-us) para criar utilizadores.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Pega Systems no Painel de Acesso, deve ser automaticamente inscrito na instância Pega Systems para a qual configura o SSO. Para mais informações, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)