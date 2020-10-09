---
title: 'Tutorial: Integração do Azure Ative Directory com o HubSpot Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 51e27526b10bcdd74d6e2bffb8bf620d7b022aac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551477"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Diretório Ativo Azure com o HubSpot

Neste tutorial, aprende-se a integrar o HubSpot com o Azure Ative Directory (Azure AD).

Integrar o HubSpot com a Azure AD dá-lhe os seguintes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso ao HubSpot.
* Os utilizadores podem ser automaticamente inscritos no HubSpot com as suas contas AD Azure (súm numa única sação).
* Pode gerir as suas contas num local central, o portal Azure.

Para obter mais informações sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [um único sinal de acesso às aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o HubSpot, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição do HubSpot com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD num ambiente de teste e integra o HubSpot com a Azure AD.

O HubSpot suporta as seguintes funcionalidades:

* **Sinal único iniciado pela SP**
* **Sinal único iniciado pelo IDP**

## <a name="add-hubspot-in-the-azure-portal"></a>Adicione o HubSpot no portal Azure

Para integrar o HubSpot com a Azure AD, tem de adicionar o HubSpot à sua lista de aplicações geridas para o SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Ative Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **Nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **o HubSpot.** Nos resultados da pesquisa, selecione **HubSpot**e, em seguida, **selecione Adicionar**.

    ![HubSpot na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o HubSpot com base num utilizador de teste chamado **Britta Simon.** Para um único sinal de sação a funcionar, deve estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no HubSpot.

Para configurar e testar o Azure AD com o HubSpot, deve completar os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar Azure AD único sinal de inscrição](#configure-azure-ad-single-sign-on)** | Permite que os seus utilizadores utilizem esta funcionalidade. |
| **[Configurar o hubSpot um único sinal](#configure-hubspot-single-sign-on)** | Configura as definições de inscrição única na aplicação. |
| **[Criar um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** | Testa o Azure AD um único sinal para um utilizador chamado Britta Simon. |
| **[Atribuir o utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** | Permite que Britta Simon utilize um único sinal de Azure. |
| **[Criar um utilizador de teste HubSpot](#create-a-hubspot-test-user)** | Cria uma contrapartida de Britta Simon no HubSpot que está ligada à representação AD Azure do utilizador. |
| **[Testar o início de sessão único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, configurar o Azure AD com o HubSpot no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)no painel de integração de aplicações **HubSpot,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML** ou **SAML/WS-Fed** para ativar um único sinal de súmido.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** para configurar o *modo iniciado pelo IDP,* complete os seguintes passos:

    1. Na caixa **do identificador,** introduza um URL com o seguinte padrão: https: \/ /api.hubspot.com/login-api/v1/saml/login?portalId= \<CUSTOMER ID\> .

    1. Na caixa **URL de resposta,** introduza um URL com o seguinte padrão: https: \/ /api.hubspot.com/login-api/v1/saml/acs?portalId= \<CUSTOMER ID\> .

    ![Informações únicas de sign-on do domínio HubSpot e URLs](common/idp-intiated.png)

    > [!NOTE]
    > Para formatar os URLs, também pode consultar os padrões indicados no painel **de configuração SAML básico** no portal Azure.

1. Para configurar a aplicação no modo *iniciado pelo SP:*

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa url de **sinalização,** **insira https: \/ /app.hubspot.com/login**.

    ![A opção UrLs adicionais set](common/metadata-upload-additional-signon.png)

1. Na **configuração single Sign-On com painel SAML,** na secção certificado de assinatura **SAML,** selecione **Download** ao lado do **Certificado (Base64)**. Selecione uma opção de descarregamento com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de descarregamento do Certificado (Base64)](common/certificatebase64.png)

1. Na secção **Configurar HubSpot,** copie os seguintes URLs com base nos seus requisitos:

    * URL de Inicio de Sessão
    * Identificador de Azure Ad
    * Logout URL

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurar o hubSpot um único sinal

1. Abra um novo separador no seu navegador e inscreva-se na sua conta de administrador hubSpot.

1. Selecione o ícone **Definições** no canto superior direito da página.

    ![O ícone de Definições no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Incumprimentos de Conta**.

    ![A opção De Incumprimento de Conta no HubSpot](./media/hubspot-tutorial/config2.png)

1. Desloque-se até à secção **de Segurança** e, em seguida, selecione **Configurar**.

    ![A opção Configurar no HubSpot](./media/hubspot-tutorial/config3.png)

1. Na secção **Configuração de inscrição única,** complete os seguintes passos:

    1. Na caixa **URl (Service Provider Entity ID),** selecione **Copy** para copiar o valor. No portal Azure, no painel **de configuração SAML Básico,** cole o valor na caixa **do Identificador.**

    1. Na placa de **sinal na URl, ACS, Destinatário ou caixa de redirecionamento,** selecione **Copy** para copiar o valor. No portal Azure, no painel **de configuração DE SAML Básico,** cole o valor na caixa **URL de resposta.**

    1. No HubSpot, na caixa **de URL do Fornecedor de Identidade ou do Emitente,** cole o valor para **identificador AD AD Azure** que copiou no portal Azure.

    1. No HubSpot, na caixa **DE URL do Fornecedor de Identidade Single Sign-On,** cole o valor para URL de **Login** que copiou no portal Azure.

    1. No Bloco de Notas do Windows, abra o ficheiro Certificado (Base64) que descarregou. Selecione e copie o conteúdo do ficheiro. Em seguida, no HubSpot, cole-o na caixa de **certificado X.509.**

    1. Selecione **Verificar**.

        ![A secção de inscrição única no HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e de Todos os utilizadores](common/users.png)

1. Selecione **Novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** complete os seguintes passos:

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza brittasimon \@ \<your-company-domain> . \<extension\> ** Por exemplo, ** \@ brittasimon contoso.com**.

    1. Selecione a caixa **de verificação de senha show.** Anota o valor que é apresentado na **caixa de palavra-passe.**

    1. Selecione **Criar**.

    ![O painel de utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, concedes acesso à Britta Simon ao HubSpot para que ela possa usar o Azure single sign-on.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **HubSpot**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **HubSpot**.

    ![HubSpot na lista de aplicações](common/all-applications.png)

1. No menu, selecione **Utilizadores e grupos.**

    ![A opção Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, no painel de **atribuição Adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera de um valor de função na afirmação SAML, no painel **de funções Select,** selecione a função relevante para o utilizador da lista. Escolha **Selecionar**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-hubspot-test-user"></a>Criar um utilizador de teste HubSpot

Para permitir que o Azure AD um utilizador entre no HubSpot, o utilizador deve ser a provisionado no HubSpot. No HubSpot, o provisionamento é uma tarefa manual.

Para a disponibilização de uma conta de utilizador no HubSpot:

1. Inscreva-se no site da empresa HubSpot como administrador.

1. Selecione o ícone **Definições** no canto superior direito da página.

    ![O ícone de Definições no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Utilizadores & Equipas**.

    ![A opção Utilizadores & Equipas no HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecione **Criar utilizador**.

    ![A opção de utilizador Criar no HubSpot](./media/hubspot-tutorial/user2.png)

1. Na caixa **addes (es) addes(es) do Add, insira** o endereço de e-mail do utilizador no formato brittasimon contoso.com e, em \@ seguida, selecione **Seguinte**.

    ![A caixa de endereço de e-mail (es) na secção Criar utilizadores no HubSpot](./media/hubspot-tutorial/user3.png)

1. Na secção **Criar utilizadores,** selecione cada separador. Em cada separador, desa estaba as opções e permissões relevantes para o utilizador. Em seguida, selecione **Seguinte**.

    ![Separadores na secção Criar utilizadores no HubSpot](./media/hubspot-tutorial/user4.png)

1. Para enviar o convite ao utilizador, selecione **Enviar**.

    ![A opção Enviar no HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O utilizador é ativado depois de o utilizador aceitar o convite.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Depois de configurar um único sôs-on, quando selecionar o **HubSpot** no portal My Apps, é automaticamente inscrito no HubSpot. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, reveja estes artigos:

- [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Inscrição única para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
