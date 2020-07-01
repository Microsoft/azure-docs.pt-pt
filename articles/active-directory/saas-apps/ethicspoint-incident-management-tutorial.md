---
title: 'Tutorial: Integração do Diretório Ativo Azure com a EthicsPoint Incident Management (EPIM) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a EthicsPoint Incident Management (EPIM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 917e70374007d230c4a675f882457b05ba3a021b
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85607019"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Tutorial: Integração do Diretório Ativo Azure com a EthicsPoint Incident Management (EPIM)

Neste tutorial, aprende-se a integrar a EthicsPoint Incident Management (EPIM) com o Azure Ative Directory (Azure AD).
Integrar a EthicsPoint Incident Management (EPIM) com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a EthicsPoint Incident Management (EPIM).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na EthicsPoint Incident Management (EPIM) (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a EthicsPoint Incident Management (EPIM), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* EthicsPoint Incident Management (EPIM) subscrição única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* EthicsPoint Incident Management (EPIM) suporta SSO iniciado **pela SP**

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Adicionar EthicsPoint Incident Management (EPIM) da galeria

Para configurar a integração da EthicsPoint Incident Management (EPIM) no Azure AD, é necessário adicionar a EthicsPoint Incident Management (EPIM) da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar EthicsPoint Incident Management (EPIM) da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva EthicsPoint Incident Management (EPIM)**, selecione **EthicsPoint Incident Management (EPIM)** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![EthicsPoint Incident Management (EPIM) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com a EthicsPoint Incident Management (EPIM) com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na EthicsPoint Incident Management (EPIM).

Para configurar e testar o Azure AD com a EthicsPoint Incident Management (EPIM), é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Gestão de Incidentes de ÉticaPoint (EPIM) Único Sign-On](#configure-ethicspoint-incident-management-epim-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create EthicsPoint Incident Management (EPIM) test user](#create-ethicspoint-incident-management-epim-test-user)** - para ter uma contraparte de Britta Simon em EthicsPoint Incident Management (EPIM) que está ligada à representação AD Ad do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de insuflação com a EthicsPoint Incident Management (EPIM), execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **EthicsPoint Incident Management (EPIM),** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de gestão de incidentes de Ponto de Ética (EPIM) e URLs de súms únicas](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:
    
        ```https
        https://<companyname>.navexglobal.com
        https://<companyname>.ethicspointvp.com
        ```

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão:`https://<companyname>.navexglobal.com/adfs/services/trust`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição, identificação e URL de resposta. Contacte [a equipa de suporte ao cliente da EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar a Gestão de Incidentes de ÉticaPoint (EPIM),** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-ethicspoint-incident-management-epim-single-sign-on"></a>Configurar a Gestão de Incidentes de ÉticaPoint (EPIM) Único Sign-On

Para configurar o lado único de sinal de acesso à Gestão de **Incidentes de Ética (EPIM),** é necessário enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de suporte de Gestão de [Incidentes de ÉticaPoint (EPIM).](https://www.navexglobal.com/company/contact-us) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite que Britta Simon utilize o Azure single sign-on, concedendo acesso à EthicsPoint Incident Management (EPIM).

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** selecione **EthicsPoint Incident Management (EPIM)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **EthicsPoint Incident Management (EPIM)**.

    ![A ligação de Gestão de Incidentes de ÉticaPoint (EPIM) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ethicspoint-incident-management-epim-test-user"></a>Criar utilizadores de teste de Gestão de Incidentes de ÉticaPoint (EPIM)

Nesta secção, cria um utilizador chamado Britta Simon in EthicsPoint Incident Management (EPIM). Trabalhe com a [equipa de suporte da EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) para adicionar os utilizadores na plataforma EthicsPoint Incident Management (EPIM). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Gestão de Incidentes de ÉticaPoint (EPIM) no Painel de Acesso, deverá ser automaticamente inscrito na Gestão de Incidentes de ÉticaPoint (EPIM) para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

