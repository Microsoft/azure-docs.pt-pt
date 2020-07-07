---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Akamai Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fef2d7e3afb0cdda3c3c3e2506b39ba542e0760
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956887"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Akamai

Neste tutorial, você vai aprender a integrar a Akamai com o Azure Ative Directory (Azure AD). Quando integrar a Akamai com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Akamai.
* Permita que os seus utilizadores sejam automaticamente inscritos na Akamai com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

A integração do Azure Ative Directory e da Akamai Enterprise Application Access permite o acesso sem emenda a aplicações antigas hospedadas na nuvem ou no local. A solução integrada tira partido de todas as capacidades modernas do Azure Ative Directory como [o Azure AD acesso condicional,](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) e [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview) para aplicações antigas de acesso sem modificações de aplicações ou instalação de agentes.

A imagem abaixo descreve, onde a Akamai EAA se enquadra no cenário mais amplo de Acesso Seguro Híbrido

![Akamai EAA enquadra-se no cenário mais amplo de acesso híbrido seguro](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Principais cenários de autenticação

Para além do suporte de integração nativa do Azure Ative Directory para protocolos modernos de autenticação como Open ID Connect, SAML e WS-Fed, a Akamai EAA alarga o acesso seguro a aplicações de autenticação baseadas em legados para acesso interno e externo com Azure AD, permitindo cenários modernos (por exemplo, acesso sem palavra-passe) a estas aplicações. Isto inclui:

* Aplicativos de autenticação baseados em cabeçalho
* Ambiente de Trabalho Remoto
* SSH (Concha Segura)
* Aplicativos de autenticação Kerberos
* VNC (Computação de Rede Virtual)
* Auth anónimo ou sem aplicativos de autenticação incorporado
* Aplicativos de autenticação NTLM (proteção com solicitações duplas para o utilizador)
* Aplicação baseada em formulários (proteção com solicitações duplas para o utilizador)

### <a name="integration-scenarios"></a>Cenários de Integração

A parceria Microsoft e Akamai EAA permite a flexibilidade para satisfazer os requisitos do seu negócio, suportando múltiplos cenários de integração baseados na sua exigência de negócio. Estes poderiam ser utilizados para fornecer uma cobertura de zero dias em todas as aplicações e classificar e configurar gradualmente as classificações políticas adequadas.

#### <a name="integration-scenario-1"></a>Cenário de integração 1

A Akamai EAA é configurada como uma única aplicação no Azure AD. A Administra pode configurar a Política de CA sobre a Aplicação e uma vez que as condições estejam satisfeitas os utilizadores podem ter acesso ao Portal EAA da Akamai.

**Prós:**

• Só é necessário configurar o IDP uma vez

**Contras:**

• Os utilizadores acabam por ter dois portais de aplicações

• Cobertura única da política comum de CA para todas as aplicações.

![Cenário de integração 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Cenário de Integração 2

A Aplicação Akamai EAA é configurada individualmente no Portal AD Azure. A Administra pode configurar individualmente a política de CA sobre as aplicações e uma vez que as condições estejam preenchidas os utilizadores podem ser redirecionados diretamente para a aplicação específica.

**Prós:**

• Pode definir políticas individuais de CA

• Todas as aplicações estão representadas no Painel 0365 waffle e myApps.microsoft.com.

**Contras:**

• É necessário configurar vários IDP.

![Cenário de Integração 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativada pela Akamai (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

- Akamai apoia IDP iniciado SSO

#### <a name="important"></a>Importante

Todas as configurações listadas abaixo são as mesmas para o **Cenário de Integração 1** e **Cenário 2**. Para o **cenário de Integração 2** tem de configurar o IDP individual na Akamai EAA e a propriedade URL precisa de ser modificada para apontar para o URL da aplicação.

![Importante](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Adicionar Akamai da galeria

Para configurar a integração da Akamai no AD Azure, precisa adicionar a Akamai da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Akamai** na caixa de pesquisa.
1. Selecione **Akamai** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Configurar e testar Azure AD único sinal para a Akamai

Configure e teste Azure AD SSO com a Akamai usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Akamai.

Para configurar e testar o Azure AD SSO com a Akamai, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO da Akamai](#configure-akamai-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Criação de IDP](#setting-up-idp)**
    * **[Autenticação baseada em cabeçalho](#header-based-authentication)**
    * **[Ambiente de Trabalho Remoto](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Autenticação Kerberos](#kerberos-authentication)**
    * **[Create Akamai test user](#create-akamai-test-user)** - para ter uma contraparte de B.Simon em Akamai que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Akamai,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a [equipa de suporte do Cliente Akamai](https://www.akamai.com/us/en/contact-us/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar a Akamai,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Akamai.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Akamai.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-akamai-sso"></a>Configurar Akamai SSO

### <a name="setting-up-idp"></a>Criação de IDP

**Configuração do IDP AKAMAI EAA**

1. Inscreva-se na consola **de acesso à aplicação da Empresa Akamai.**
1. Na **consola Akamai EAA**, selecione **Identity**  >  **Fornecedores de Identidade** de Identidade e clique em **Adicionar Fornecedor de Identidade**.

    ![Akamai configurante](./media/header-akamai-tutorial/configure01.png)

1. No **Criar Novo Fornecedor de Identidade** execute os seguintes passos:

    ![Akamai configurante](./media/header-akamai-tutorial/configure02.png)

    a. Especificar o **Nome Único.**

    b. Escolha **o SAML de terceiros** e clique em **Criar Fornecedor de Identidade e Configurar.**

### <a name="general-settings"></a>Definições Gerais

1. **Intercetação de Identidade** - Especifique o nome do URL de base SP - será usado para configuração AD Azure)

    > [!NOTE]
    > Pode optar por ter o seu próprio domínio personalizado (será necessário uma entrada de DNS e um Certificado). Neste exemplo, vamos usar o Domínio Akamai.

1. **Akamai Cloud Zone** - Selecione a zona de nuvem apropriada.
1. **Validação de Certificados** - Verifique documentação da Akamai (opcional)

    ![Akamai configurante](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configuração de autenticação

1. URL – Especifique o URL igual ao da sua interceção de identidade (é aqui que os utilizadores são redirecionados após a autenticação).
2. URL logout : Atualizar o URL logout.
3. Assine pedido SAML: padrão não verificado.
4. Para o Ficheiro de Metadados IDP, adicione a Aplicação na Consola AD Azure.

    ![Akamai configurante](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Definições de sessão

Deixe as definições como padrão.

![Akamai configurante](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Diretórios

Ignore a configuração do diretório.

![Akamai configurante](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>UI de personalização

Pode adicionar personalização ao IDP.

![Akamai configurante](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Definições Avançadas

Ignore as definições de Advance / consulte a documentação da Akamai para mais detalhes.

![Akamai configurante](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Implementação

1. Clique no Fornecedor de Identidade de Implementação.

    ![Akamai configurante](./media/header-akamai-tutorial/deployment.png)

2. Verifique se a implementação foi bem sucedida

### <a name="header-based-authentication"></a>Autenticação baseada em cabeçalho

Autenticação baseada em cabeçalho de Akamai

1. Escolha **o formulário HTTP personalizado** do Assistente de Aplicações de Adicionar.

    ![Akamai configurante](./media/header-akamai-tutorial/configure05.png)

2. Insira **o nome** e **descrição da aplicação.**

    ![Akamai configurante](./media/header-akamai-tutorial/configure06.png)

    ![Akamai configurante](./media/header-akamai-tutorial/configure07.png)

    ![Akamai configurante](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autenticação

1. Selecione **o separador autenticação.**

    ![Akamai configurante](./media/header-akamai-tutorial/configure09.png)

2. Atribuir o **fornecedor de identidade**

    ![Akamai configurante](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Serviços

Clique em Guardar e Ir à Autenticação.

![Akamai configurante](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Definições Avançadas

1. Sob os **cabeçalhos HTTP do Cliente**, especifique o **Atributo CustomerHeader** e **SAML**.

    ![Akamai configurante](./media/header-akamai-tutorial/configure12.png)

1. Clique em Guardar e ir para o botão **De implementação.**

    ![Akamai configurante](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Implementar a Aplicação

1. Clique no botão **Implementar Aplicação.**

    ![Akamai configurante](./media/header-akamai-tutorial/configure14.png)

1. Verifique se a aplicação foi implementada com sucesso.

    ![Akamai configurante](./media/header-akamai-tutorial/configure15.png)

1. Experiência de Utilizador Final.

    ![Akamai configurante](./media/header-akamai-tutorial/enduser01.png)

    ![Akamai configurante](./media/header-akamai-tutorial/enduser02.png)

1. Acesso Condicional.

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto

1. Escolha **RDP** no Assistente de Aplicações ADD.

    ![Akamai configurante](./media/header-akamai-tutorial/configure16.png)

1. Insira **o nome** e **descrição da aplicação.**

    ![Akamai configurante](./media/header-akamai-tutorial/configure17.png)

    ![Akamai configurante](./media/header-akamai-tutorial/configure18.png)

1. Especifique o Conector que irá fazer a manutenção disto.

    ![Akamai configurante](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autenticação

Clique **em Guardar e ir aos Serviços.**

![Akamai configurante](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Serviços

Clique **em Guardar e ir para Definições Avançadas**.

![Akamai configurante](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Definições Avançadas

1. Clique **em Guardar e ir para a Implementação**.

    ![Akamai configurante](./media/header-akamai-tutorial/configure22.png)

    ![Akamai configurante](./media/header-akamai-tutorial/configure23.png)

    ![Akamai configurante](./media/header-akamai-tutorial/configure24.png)

1. Experiência de Utilizador Final

    ![Akamai configurante](./media/header-akamai-tutorial/enduser03.png)

    ![Akamai configurante](./media/header-akamai-tutorial/enduser02.png)

1. Acesso Condicional

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Em alternativa, também pode escrever diretamente o URL de aplicação RDP.

#### <a name="ssh"></a>SSH

1. Vá para adicionar aplicações, escolha **SSH**.

    ![Akamai configurante](./media/header-akamai-tutorial/configure25.png)

1. Insira **o nome** e **descrição da aplicação.**

    ![Akamai configurante](./media/header-akamai-tutorial/configure26.png)

1. Configurar identidade de aplicação.

    ![Akamai configurante](./media/header-akamai-tutorial/configure27.png)

    a. Especificar Nome / Descrição.

    b. Especifique o Servidor de Aplicações IP/FQDN e a porta para SSH.

    c. Especificar o nome de utilizador /palavra-passe SSH *Check Akamai EAA.

    d. Especifique o Nome do anfitrião Externo.

    e. Especifique a localização do conector e escolha o conector.

#### <a name="authentication"></a>Autenticação

Clique em **Guardar e vá aos Serviços.**

![Akamai configurante](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Serviços

Clique **em Guardar e ir para Definições Avançadas**.

![Akamai configurante](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Definições Avançadas

Clique em Guardar e ir Implantar

![Akamai configurante](./media/header-akamai-tutorial/configure30.png)

![Akamai configurante](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implementação

1. Clique **na aplicação Implementar**.

    ![Akamai configurante](./media/header-akamai-tutorial/configure32.png)

1. Experiência de Utilizador Final

    ![Akamai configurante](./media/header-akamai-tutorial/enduser03.png)

    ![Akamai configurante](./media/header-akamai-tutorial/enduser04.png)

1. Acesso Condicional

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Autenticação Kerberos

No exemplo abaixo publicaremos um servidor web interno [http://frp-app1.superdemo.live](http://frp-app1.superdemo.live/) e ativaremos o SSO usando o KCD

#### <a name="general-tab"></a>Separador Geral

![Akamai configurante](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Separador de autenticação

Atribuir o Fornecedor de Identidade

![Akamai configurante](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Separador de Serviços

![Akamai configurante](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Definições Avançadas

![Akamai configurante](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> O SPN para o Web Server tem estado no SPN@Domain Formato ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` para esta demonstração. Deixe o resto das definições por defeito.

#### <a name="deployment-tab"></a>Separador de implementação

![Akamai configurante](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Adição de Diretório

1. Selecione **AD** a partir do dropdown.

    ![Akamai configurante](./media/header-akamai-tutorial/configure33.png)

1. Forneça os dados necessários.

    ![Akamai configurante](./media/header-akamai-tutorial/configure34.png)

1. Verifique a Criação do Diretório.

    ![Akamai configurante](./media/header-akamai-tutorial/directorydomain.png)

1. Adicione os Grupos/OUs que seriam necessários acesso.

    ![Akamai configurante](./media/header-akamai-tutorial/addgroup.png)

1. No grupo abaixo é chamado EAAGroup e tem 1 Membro.

    ![Akamai configurante](./media/header-akamai-tutorial/eaagroup.png)

1. Adicione o Diretório ao Fornecedor **Identity**de Identidade clicando em  >  **Fornecedores de Identidade** de Identidade e clique no separador **Diretório** e clique no **diretório de atribuição**.

    ![Akamai configurante](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Configure delegação KCD para a EAA Walkthrough

#### <a name="step-1-create-an-account"></a>Passo 1: Criar uma Conta 

1. No exemplo, usaremos uma conta chamada **EAADelegation**. Pode efetuar isto utilizando os utilizadores do **Ative Directory e o computador** Snappin.

    ![Akamai configurante](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > O nome de utilizador deve estar num formato específico baseado no **Nome de Interceção de Identidade**. Da figura 1 que vemos é **corpapps.login.go.akamai-access.com**

1. O nome do logotipo do utilizador será:`HTTP/corpapps.login.go.akamai-access.com`

    ![Akamai configurante](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Passo 2: Configurar o SPN para esta conta

1. Com base nesta amostra, o SPN será o mais baixo.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Akamai configurante](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Passo 3: Delegação de Configuração

1. Para a conta EAADelegation clique no separador Delegação.

    ![Akamai configurante](./media/header-akamai-tutorial/spn.png)

    * Especifique a utilização de qualquer protocolo de autenticação
    * Clique em Adicionar e Adicione a Conta Pool de Aplicações para o Website Kerberos. Deverá resolver automaticamente a correção da SPN se estiver configurado corretamente.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Passo 4: Criar um ficheiro keytab para o AKAMAI EAA

1. Aqui está a Sintaxe genérica.

1. ktpass /out ActiveDirectorydomain.keytab /princ `HTTP/yourloginportalurl@ADDomain.com` /mapuser serviceaccount@ADdomain.com /pass +rdnPass /crypto All /ptype KRB5_NT_PRINCIPAL

1. Exemplo explicado

    | Fragmento de código | Explicação |
    | - | - |
    | Ktpass /out EAADemo.keytab | Nome do ficheiro Keytab de saída |
    | /princHTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapusereaadelegation@superdemo.live | Conta da delegação da EAA |
    | /passe RANDOMPASS | Password da conta da delegação da EAA |
    | /crypto All ptype KRB5_NT_PRINCIPAL | consultar documentação akamai EAA |
    | | |

1. Ktpass /out EAADemo.keytab /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Akamai configurante](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Passo 5: Import Keytab na consola AKAMAI EAA

1. Clique **System**em  >  **Keytabs de**Sistema .

    ![Akamai configurante](./media/header-akamai-tutorial/keytabs.png)

1. No Tipo Keytab escolha **a Delegação Kerberos.**

    ![Akamai configurante](./media/header-akamai-tutorial/keytabdelegation.png)

1. Certifique-se de que o Keytab aparece como Implantado e Verificado.

    ![Akamai configurante](./media/header-akamai-tutorial/keytabs02.png)

1. Experiência do Utilizador

    ![Akamai configurante](./media/header-akamai-tutorial/enduser03.png)

    ![Akamai configurante](./media/header-akamai-tutorial/enduser04.png)

1. Acesso Condicional

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Akamai configurante](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Criar utilizador de teste Akamai

Nesta secção, cria-se um utilizador chamado B.Simon em Akamai. Trabalhe com a [equipa de suporte do Cliente Akamai](https://www.akamai.com/us/en/contact-us/) para adicionar os utilizadores na plataforma Akamai. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Akamai no Painel de Acesso, deverá ser automaticamente inscrito no Akamai para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Akamai com a Azure AD](https://aad.portal.azure.com/)
