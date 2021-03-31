---
title: 'Tutorial: Integração do Azure Ative Directory com a ArcGIS Enterprise | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9cde75440292fffb830656c32181d7be64a55f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645529"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Integração do Azure Ative Directory com a ArcGIS Enterprise

Neste tutorial, você vai aprender a integrar a ArcGIS Enterprise com a Azure Ative Directory (Azure AD). Quando integrar a ArcGIS Enterprise com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à ArcGIS Enterprise.
* Habilita os seus utilizadores a serem automaticamente inscritos na ArcGIS Enterprise com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pela ArcGIS Enterprise (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A ArcGIS Enterprise apoia **a SP e a IDP** iniciada sSO.
* A ArcGIS Enterprise suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Adicione a ArcGIS Enterprise da galeria

Para configurar a integração da ArcGIS Enterprise em Azure AD, é necessário adicionar a ArcGIS Enterprise da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite a ArcGIS Enterprise** na caixa de pesquisa.
1. Selecione **ArcGIS Enterprise** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Configure e teste Azure AD SSO para a ArcGIS Enterprise

Configure e teste Azure AD SSO com a ArcGIS Enterprise utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na ArcGIS Enterprise.

Para configurar e testar a Azure AD SSO com a ArcGIS Enterprise, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o ArcGIS Enterprise SSO](#configure-arcgis-enterprise-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create ArcGIS Enterprise test user](#create-arcgis-enterprise-test-user)** - para ter uma contraparte de B.Simon na ArcGIS Enterprise que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **da ArcGIS Enterprise,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos, se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<EXTERNAL_DNS_NAME>.portal`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao Cliente da ArcGIS Enterprise](mailto:support@esri.com) para obter estes valores. Você receberá o valor identificador da secção De Fornecedor de **Identidade,** que é explicado mais tarde neste tutorial.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à ArcGIS Enterprise.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ArcGIS Enterprise**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-arcgis-enterprise-sso"></a>Configurar a ArcGIS Enterprise SSO

1. Para automatizar a configuração dentro do ArcGIS Enterprise, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar a ArcGIS Enterprise** irá direcioná-lo para a aplicação ArcGIS Enterprise. A partir daí, forneça as credenciais de administração para assinar na ArcGIS Enterprise. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar a ArcGIS Enterprise manualmente, inicie sessão no site da empresa ArcGIS Enterprise como administrador.


1. Selecione **Configurações de EDIÇÃO >Organização.**

    ![A screenshot mostra o separador ArcGIS Enterprise Organization com definições de Edição chamadas.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Selecione o separador **Segurança**.

    ![A screenshot mostra o separador de Segurança selecionado.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Desloque-se até aos Logins da Empresa através da secção **SAML** e selecione **SET ENTERPRISE LOGIN**.

    ![O Screenshot mostra logins da Empresa via SAML onde pode selecionar set Enterprise Login.](./media/arcgisenterprise-tutorial/configure-3.png)

1. Na secção **'Fornecedor de Identidade' set,** execute os seguintes passos:

    ![A screenshot mostra o Fornecedor de Identidade Definido onde executa os passos descritos aqui.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Por favor, forneça um nome como **Azure Ative Directory Test** na caixa de texto **Name.**

    b. Na caixa de texto **URL,** cole o valor url de **metadados da Federação de Aplicações** que copiou a partir do portal Azure.

    c. Clique em **Mostrar definições avançadas** e copie o valor **de ID** da Entidade e cole-o na caixa de texto **identifier** na secção **Domínio Empresarial e URLs arcgis** no portal Azure.

    ![A screenshot mostra onde obter a Entidade I D e atualizar o fornecedor de identificação.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. CLIQUE **EM UPDATE IDENTITY PROVIDER**.

### <a name="create-arcgis-enterprise-test-user"></a>Criar utilizador de teste arcgis enterprise

Nesta secção, um utilizador chamado Britta Simon é criado na ArcGIS Enterprise. A ArcGIS Enterprise suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na ArcGIS Enterprise, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte da ArcGIS Enterprise.](mailto:support@esri.com)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Signo da Empresa ArcGIS no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição da Empresa ArcGIS e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Empresa ArcGIS para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo da Empresa ArcGIS nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Empresa ArcGIS para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a ArcGIS Enterprise, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).