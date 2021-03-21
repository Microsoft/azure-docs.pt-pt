---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com | de PCS Secure Pulse Microsoft Docs'
description: Saiba como configurar um único sinal de inserção entre o Azure Ative Directory e o Pulse Secure PCS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511448"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Pulse Secure PCS

Neste tutorial, você vai aprender a integrar Pulse Secure PCS com Azure Ative Directory (Azure AD). Quando integrar o Pulse Secure PCS com Ad AZure, pode:

* Control em Azure AD que tem acesso a Pulse Secure PCS.
* Ativar os seus utilizadores a serem automaticamente inscritos no Pulse Secure PCS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Pulse Secure PCS única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Pulse Secure PCS suporta **SSO** iniciado SP

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Adicionar Pulse Secure PCS da galeria

Para configurar a integração do Pulse Secure PCS no Azure AD, é necessário adicionar o Pulse Secure PCS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Pulse Secure PCS** na caixa de pesquisa.
1. Selecione **Pulse Secure PCS** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Configure e teste Azure AD SSO para PcS Seguros de Pulso

Configure e teste Azure AD SSO com Pulse Secure PCS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Pulse Secure PCS.

Para configurar e testar O SSO Azure AD com Pulse Secure PCS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Pulse Secure PCS SSO](#configure-pulse-secure-pcs-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Pulse Secure PCS test user](#create-pulse-secure-pcs-test-user)** - para ter uma contraparte de B.Simon em Pulse Secure PCS que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Pulse Secure PCS,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL,URL de resposta e identificador. Contacte [a equipa de suporte do cliente Pulse Secure PCS](mailto:support@pulsesecure.net) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar o Pulse Secure PCS,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso a Pulse Secure PCS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Pulse Secure PCS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-pulse-secure-pcs-sso"></a>Configure pulso seguro PCS SSO

Esta secção abrange as configurações SAML necessárias para configurar PCS como SAML SP. As outras configurações básicas como a criação de Reinos e Papéis não são abrangidas.

**As configurações de Ligação de Pulsos Secure incluem:**

* Configurar a AD AZure como Fornecedor de Metadados SAML
* Servidor SAML Auth configurado
* Atribuição aos respetivos Reinos e Funções

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Configurar a AD AZure como Fornecedor de Metadados SAML

Execute os seguintes passos na seguinte página:

![Configuração segura de ligação de pulso](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Inicie sessão na consola de administração Pulse Connect Secure
1. Navegue para **a configuração de > sistema -> SAML**
1. Clique em **Novo Fornecedor de Metadados**
1. Fornecer o Nome válido na caixa de texto **Nome**
1. Faça o upload do ficheiro XML de metadados descarregado do portal Azure para o **ficheiro metadados AZure AD**.
1. Selecione **Aceitar Metadados Não Assinados**
1. Selecione funções como **Fornecedor de Identidade**
1. Clique em **Guardar alterações.**

#### <a name="steps-to-create-a-saml-auth-server"></a>Passos para criar um servidor SAML Auth:

1. Navegar para **autenticação -> servidores Auth**
1. Selecione **Novo: SERVIDOR SAML** e clique em **Novo Servidor**

    ![Pulse Connect Secure servidor de auth](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Execute os seguintes passos na página de definições:

    ![Pulse Connect Definições de servidor de auth Secure](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Fornecer **Nome do Servidor** na caixa de texto.

    b. Selecione **a versão SAML 2.0** e **o modo de configuração** como **metadados**.

    c. Copie o valor **do Id da Entidade Segura Connect** e cole-o na caixa URL do **Identificador** na caixa de diálogo **de configuração SAML básica** no portal Azure.

    d. Selecione valor de Id da Entidade AD Azure da lista de entrega de **Id da Entidade fornecedora** de identidade .

    e. Selecione o valor URL de login Azure Ad da lista de lançamento do URL de **serviço de Sign-On único do fornecedor de identidade**.

    f. **O Logout único** é uma definição opcional. Se esta opção for selecionada, solicita uma nova autenticação após o início de súm. Se esta opção não for selecionada e não tiver fechado o navegador, poderá voltar a ligar-se sem autenticação.

    exemplo, Selecione a **Classe de Contexto Authn solicitada** como **palavra-passe** e o **método de comparação** como **exato**.

    h. Desaver a validade dos **metadados** em termos de número de dias.
    
    i. Clique **em Guardar Alterações**

### <a name="create-pulse-secure-pcs-test-user"></a>Criar utilizador de teste de PCS Seguro de Pulso

Nesta secção, cria-se um utilizador chamado Britta Simon em Pulse Secure PCS. Trabalhe com [a equipa de suporte do PcS Pulse Secure](mailto:support@pulsesecure.net) para adicionar os utilizadores na plataforma PcS Pulse Secure. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de início de Sessão do PcS Pulse Secure, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição do PCS De segurança pulse e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo Do PCS Pulse Secure no Painel de Acesso, este irá redirecionar para URL de inscrição de PCS de segurança de pulso. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Pulse Secure PCS, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).