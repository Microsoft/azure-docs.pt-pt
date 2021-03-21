---
title: 'Tutorial: Integração do Diretório Ativo Azure com o HubSpot | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733254"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Diretório Ativo Azure com o HubSpot

Neste tutorial, você vai aprender a integrar o HubSpot com o Azure Ative Directory (Azure AD). Quando integrar o HubSpot com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao HubSpot.
* Ative os seus utilizadores a serem automaticamente inscritos no HubSpot com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o HubSpot, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição AD Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição do HubSpot com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD num ambiente de teste e integra o HubSpot com a Azure AD.

O HubSpot suporta as seguintes funcionalidades:

* **Sinal único iniciado pela SP**
* **Sinal único iniciado pelo IDP**

## <a name="adding-hubspot-from-the-gallery"></a>Adicionar HubSpot da galeria

Para configurar a integração do HubSpot no Azure AD, precisa de adicionar o HubSpot da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite HubSpot** na caixa de pesquisa.
1. Selecione **o HubSpot** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Configure e teste Azure AD SSO para HubSpot

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

1. No portal Azure, na página de integração de aplicações **do HubSpot,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

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

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
    1. No campo **Nome**, introduza `B.Simon`.  
    1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao HubSpot.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **HubSpot**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

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

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o HubSpot Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login do HubSpot e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no HubSpot para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo hubSpot nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no HubSpot para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o HubSpot, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).