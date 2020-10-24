---
title: 'Tutorial: Integração do Azure Ative Directory com a Pega Systems Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 4fb117b7f7b9a0c7a6a67e2714380a01cd53a4e0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515649"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Diretório Ativo Azure com a Pega Systems

Neste tutorial, você vai aprender a integrar a Pega Systems com o Azure Ative Directory (Azure AD).

Esta integração proporciona estes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso à Pega Systems.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Pega Systems (súmido único) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a Pega Systems, você precisa ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode [inscrever-se](https://azure.microsoft.com/pricing/free-trial/)para um julgamento de um mês.
* Uma subscrição da Pega Systems que tem um único sinal de acesso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* A Pega Systems suporta sSO iniciado pelo SP e iniciado pelo IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicione sistemas Pega da galeria

Para configurar a integração da Pega Systems no Azure AD, é necessário adicionar a Pega Systems da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Enterprise**  >  **Todas as aplicações.**

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **a Pega Systems**. Selecione **Os Sistemas Pega** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com a Pega Systems utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente nos Sistemas Pega.

Para configurar e testar o Azure AD com a Pega Systems, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar a Pega Systems uma única sação no](#configure-pega-systems-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste da Pega Systems](#create-a-pega-systems-test-user)** que esteja ligado à representação AD AZure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Pega Systems, tome estes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **pega Systems,** selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **de configuração de configuração básica SAML,** se pretender configurar a aplicação no modo iniciado pelo IdP, complete os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/idp-intiated.png)

    1. Na caixa **identifier,** introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na caixa **URL de resposta,** introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e completar os seguintes passos.

    ![Pega Systems Domain e URLs informações únicas de súmis](common/both-advanced-urls.png)

    1. Na placa na caixa **URL,** introduza o sinal no valor URL.

    1. Na caixa **'Retransmissão Estado',** introduza um URL neste padrão: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores aqui fornecidos são espaços reservados. Você precisa usar o identificador real, URL de resposta, assinar em URL, e transmitir URL estado. Você pode obter o identificador e responder valores DE URL a partir de uma aplicação Pega, como explicado mais tarde neste tutorial. Para obter o valor do estado de retransmissão, contacte a equipa de suporte da [Pega Systems.](https://www.pega.com/contact-us) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A aplicação Pega Systems necessita que as afirmações DOL estejam num formato específico. Para os obter no formato correto, é necessário adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra os atributos predefinidos. Selecione o ícone **Editar** para abrir a caixa de diálogo **'Atributos do Utilizador':**

    ![Atributos do utilizador](common/edit-attribute.png)

7. Além dos atributos mostrados na imagem anterior, a aplicação Pega Systems requer mais alguns atributos para ser repercutido na resposta SAML. Na secção de **reclamações** do Utilizador da caixa de diálogo Atributos do **Utilizador,** complete os seguintes passos para adicionar estes atributosken SAML:

    
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
    > Estes valores são específicos da sua organização. Fornecer os valores apropriados.

    1. **Selecione Adicionar nova reclamação** para abrir a caixa de diálogo **de reclamações** do utilizador::

    ![Selecione Adicionar nova reclamação](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de reclamações do utilizador](common/new-attribute-details.png)

    1. Na caixa **Nome,** insira o nome de atributo mostrado para esta linha.

    1. Deixe a caixa **namespace** vazia.

    1. Para a **Fonte**, selecione **Atributo**.

    1. Na lista **de atributos 'Fonte',** selecione o valor do atributo mostrado para esta linha.

    1. Selecione **OK**.

    1. Selecione **Guardar**.

8. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** ao lado **do Metadados XML da Federação,** de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/metadataxml.png)

9. Na secção **Configurar sistemas Pega,** copie os URLs apropriados, com base nos seus requisitos.

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-pega-systems-single-sign-on"></a>Configurar a Pega Systems um único sinal

1. Para configurar um único sinal no lado da **Pega Systems,** inscreva-se no Portal Pega com uma conta de administração noutra janela do navegador.

2. Selecione **Criar**  >  o Serviço de Autenticação**SysAdmin**  >  **Authentication Service**:

    ![Selecione Serviço de Autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Complete os seguintes passos no ecrã **do Serviço de Autenticação Create.**

    ![Criar ecrã de serviço de autenticação](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na lista **tipo,** selecione **SAML 2.0**.

    1. Na caixa **Nome,** insira qualquer nome (por exemplo, **Azure AD SSO).**

    1. Na caixa **de descrição curta,** introduza uma descrição.  

    1. Selecione **Criar e abrir**.
    
4. Na secção **de informações do Fornecedor de Identidade (IdP),** selecione **metadados de IdP de importação** e navegue no ficheiro de metadados que descarregou a partir do portal Azure. Clique **em Enviar** para carregar os metadados:

    ![Secção de informação do Fornecedor de Identidade (IdP)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    A importação preencherá os dados do IdP, tal como mostrado aqui:

    ![Dados idP importados](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Preencha os seguintes passos na secção **de definições do Fornecedor de Serviços (SP).**

    ![Definições de prestadores de serviços](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Copie o valor **de Identificação da Entidade** e cole-o na caixa de **identificação** na secção **Configuração DE SAML Básico** no portal Azure.

    1. Copie o valor **de localização do Serviço de Apoio ao Consumidor de Afirmação (ACS)** e cole-o na caixa **URL de resposta** na secção de **Configuração SAML Básica** no portal Azure.

    1. **Selecione A assinatura do pedido de desativação**.

7. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **O Diretório Ativo Azure** no painel esquerdo, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**:

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do utilizador,** complete os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    a. Na caixa **Nome,** **insira BrittaSimon**.
  
    b. Na caixa **do nome do utilizador,** **introduza brittasimon@ . \<yourcompanydomain> . \<extension> ** (Por exemplo, BrittaSimon@contoso.com .)

    c. Selecione **Mostrar a palavra-passe**e, em seguida, anotar o valor que está na caixa **de palavra-passe.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo-lhe acesso à Pega Systems.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações**e, em seguida, selecione **Pega Systems**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Pega Systems**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-pega-systems-test-user"></a>Criar um utilizador de teste de Sistemas Pega

Em seguida, precisa criar um utilizador chamado Britta Simon in Pega Systems. Trabalhe com a [equipa de suporte da Pega Systems](https://www.pega.com/contact-us) para criar utilizadores.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Ao selecionar o azulejo pega Systems no Painel de Acesso, deverá ser automaticamente inscrito no caso Pega Systems para o qual configura sSO. Para mais informações, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)