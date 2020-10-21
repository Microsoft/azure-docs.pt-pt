---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Akamai Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: bbea6a0aa31034cd1c04145fb50b72432c9f8520
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319005"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Akamai

Neste tutorial, você vai aprender a integrar a Akamai com o Azure Ative Directory (Azure AD). Quando integrar a Akamai com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Akamai.
* Permita que os seus utilizadores sejam automaticamente inscritos na Akamai com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

A integração do Azure Ative Directory e da Akamai Enterprise Application Access permite o acesso sem emenda a aplicações antigas hospedadas na nuvem ou no local. A solução integrada tira partido de todas as capacidades modernas do Azure Ative Directory como [o Azure AD acesso condicional,](../conditional-access/overview.md) [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) e [Azure AD Identity Governance](../governance/identity-governance-overview.md) para aplicações antigas de acesso sem modificações de aplicações ou instalação de agentes.

A imagem abaixo descreve, onde a Akamai EAA se enquadra no cenário mais amplo de Acesso Seguro Híbrido

![Akamai EAA enquadra-se no cenário mais amplo de acesso híbrido seguro](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Principais cenários de autenticação

Para além do suporte de integração nativa do Azure Ative Directory para protocolos modernos de autenticação como Open ID Connect, SAML e WS-Fed, a Akamai EAA alarga o acesso seguro a aplicações de autenticação baseadas em legados para acesso interno e externo com Azure AD, permitindo cenários modernos (por exemplo, acesso sem palavra-passe) a estas aplicações. O que está incluído:

* Aplicativos de autenticação baseados em cabeçalho
* Ambiente de Trabalho Remoto
* SSH (Concha Segura)
* Aplicativos de autenticação Kerberos
* VNC (Computação de Rede Virtual)
* Auth anónimo ou sem aplicativos de autenticação incorporado
* Aplicativos de autenticação NTLM (proteção com solicitações duplas para o utilizador)
* Forms-Based Aplicação (proteção com solicitações duplas para o utilizador)

### <a name="integration-scenarios"></a>Cenários de Integração

A parceria Microsoft e Akamai EAA permite a flexibilidade para satisfazer os requisitos do seu negócio, suportando múltiplos cenários de integração baseados na sua exigência de negócio. Estes poderiam ser utilizados para fornecer uma cobertura de zero dias em todas as aplicações e classificar e configurar gradualmente as classificações políticas adequadas.

#### <a name="integration-scenario-1"></a>Cenário de integração 1

A Akamai EAA é configurada como uma única aplicação no Azure AD. A Administra pode configurar a Política de CA sobre a Aplicação e uma vez que as condições estejam satisfeitas os utilizadores podem ter acesso ao Portal EAA da Akamai.

**Prós:**

* Só precisa configurar o IDP uma vez.

**Contras:**

* Os utilizadores acabam por ter dois portais de aplicações

* Cobertura única da Política Comum de CA para todas as aplicações.

![Cenário de integração 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Cenário de Integração 2

A Aplicação Akamai EAA é configurada individualmente no Portal AD Azure. A Administra pode configurar individualmente a política de CA sobre as aplicações e uma vez que as condições estejam preenchidas os utilizadores podem ser redirecionados diretamente para a aplicação específica.

**Prós:**

* Pode definir políticas individuais de CA

* Todas as Aplicações estão representadas no Painel 0365 waffle e myApps.microsoft.com.


**Contras:**

* Tens de configurar vários IDP.

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

![Screenshot do separador Geral para AZURESSO-SP no Akamai Enterprise Application Access. Destaca-se o campo URL de configuração de autenticação.](./media/header-akamai-tutorial/important.png)

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

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

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

    ![Screenshot da janela de fornecedores de identidade da consola Akamai EAA. Selecione Fornecedores de Identidade no menu Identidade e selecione Adicionar Fornecedor de Identidade.](./media/header-akamai-tutorial/configure01.png)

1. No **Criar Novo Fornecedor de Identidade** execute os seguintes passos:

    ![Screenshot do diálogo Criar Novos Fornecedores de Identidade na consola Akamai EAA.](./media/header-akamai-tutorial/configure02.png)

    a. Especificar o **Nome Único.**

    b. Escolha **o SAML de terceiros** e clique em **Criar Fornecedor de Identidade e Configurar.**

### <a name="general-settings"></a>Definições Gerais

1. **Intercetação de Identidade** - Especifique o nome do URL de base SP - será usado para configuração AD Azure)

    > [!NOTE]
    > Pode optar por ter o seu próprio domínio personalizado (será necessário uma entrada de DNS e um Certificado). Neste exemplo, vamos usar o Domínio Akamai.

1. **Akamai Cloud Zone** - Selecione a zona de nuvem apropriada.
1. **Validação de Certificados** - Verifique documentação da Akamai (opcional)

    ![Screenshot do separador geral da consola Akamai EAA mostrando definições para O Intercept de Identidade, Zona de Nuvem Akamai e Validação de Certificados.](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configuração de autenticação

1. URL – Especifique o URL igual ao da sua interceção de identidade (é aqui que os utilizadores são redirecionados após a autenticação).
2. URL logout : Atualizar o URL logout.
3. Assine pedido SAML: padrão não verificado.
4. Para o Ficheiro de Metadados IDP, adicione a Aplicação na Consola AD Azure.

    ![Screenshot da configuração de autenticação da consola Akamai EAA mostrando definições para URL, URL de logote, pedido de sinal SAML e ficheiro de metadados IDP.](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Definições de sessão

Deixe as definições como padrão.

![Screenshot do diálogo de sessão de consola da Akamai EAA.](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Diretórios

Ignore a configuração do diretório.

![Screenshot do separador De consolas Akamai EAA.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>UI de personalização

Pode adicionar personalização ao IDP.

![Screenshot do separador de personalização da consola Akamai EAA mostrando definições para Personalizar UI, configurações de idioma e temas.](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Definições Avançadas

Ignore as definições de Advance / consulte a documentação da Akamai para mais detalhes.

![Screenshot do separador Configurações Avançadas da consola Akamai EAA mostrando as definições para cliente EAA, Avançado e OIDC para a ponte SAML.](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Implementação

1. Clique no Fornecedor de Identidade de Implementação.

    ![Screenshot do separador implementação da consola Akamai EAA mostrando o botão de fornecedor de entidade implementar.](./media/header-akamai-tutorial/deployment.png)

2. Verifique se a implementação foi bem sucedida

### <a name="header-based-authentication"></a>Autenticação baseada em cabeçalho

Autenticação baseada em cabeçalho de Akamai

1. Escolha **o formulário HTTP personalizado** do Assistente de Aplicações de Adicionar.

    ![Screenshot do assistente de aplicações de adicionar aplicações da consola Akamai EAA mostrando CustomHTTP listado na secção Aplicações de Acesso.](./media/header-akamai-tutorial/configure05.png)

2. Insira **o nome** e **descrição da aplicação.**

    ![Screenshot de um diálogo de aplicação HTTP personalizado mostrando as definições para Nome de Aplicação e Descrição.](./media/header-akamai-tutorial/configure06.png)

    ![Screenshot do separador geral da consola Akamai EAA mostrando definições gerais para MYHEADERAPP.](./media/header-akamai-tutorial/configure07.png)

    ![Screenshot da consola Akamai EAA mostrando definições para Certificado e Localização.](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autenticação

1. Selecione **o separador autenticação.**

    ![Screenshot da consola Akamai EAA com o separador autenticação selecionado.](./media/header-akamai-tutorial/configure09.png)

2. Atribuir o **fornecedor de identidade**

    ![Screenshot do separador de autenticação da consola Akamai EAA para MYHEADERAPP mostrando o conjunto do fornecedor de identidade para Azure AD SSO.](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Serviços

Clique em Guardar e Ir à Autenticação.

![Screenshot do separador Serviços de consola Akamai EAA para MYHEADERAPP mostrando o Botão Save e ir para o botão AdvancedSettings no canto inferior direito.](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Definições Avançadas

1. Sob os **cabeçalhos HTTP do Cliente**, especifique o **Atributo CustomerHeader** e **SAML**.

    ![Screenshot do separador Configurações Avançadas da consola Akamai EAA mostrando o campo URL registado SSO destacado na Autenticação.](./media/header-akamai-tutorial/configure12.png)

1. Clique em Guardar e ir para o botão **De implementação.**

    ![Screenshot do separador Configurações Avançadas da consola Akamai EAA mostrando o botão Salvar e ir para o botão De implementação no canto inferior direito.](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Implementar a Aplicação

1. Clique no botão **Implementar Aplicação.**

    ![Screenshot do separador implementação da consola Akamai EAA mostrando o botão de aplicação Implementar.](./media/header-akamai-tutorial/configure14.png)

1. Verifique se a aplicação foi implementada com sucesso.

    ![Screenshot do separador implementação da consola Akamai EAA mostrando a mensagem de estado da aplicação: "Aplicação implementada com sucesso".](./media/header-akamai-tutorial/configure15.png)

1. End-User Experience.

    ![Screenshot do ecrã de abertura para myapps.microsoft.com com uma imagem de fundo e um Sinal em diálogo.](./media/header-akamai-tutorial/enduser01.png)

    ![Screenshot mostrando parte de uma janela de Apps com ícones para add-in, HRWEB, Akamai - CorpApps, Despesas, Grupos e avaliações de Acesso. ](./media/header-akamai-tutorial/enduser02.png)

1. Acesso Condicional.

    ![Screenshot da mensagem: Aprovar sinal a pedido. Enviámos uma notificação para o seu dispositivo móvel. Por favor, responda para continuar.](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Screenshot de um ecrã de aplicações mostrando um ícone para o MyHeaderApp.](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto

1. Escolha **RDP** no Assistente de Aplicações ADD.

    ![Screenshot do assistente de aplicações de adicionar aplicações da consola Akamai EAA mostrando RDP listado entre as aplicações na secção De Aplicações de Acesso.](./media/header-akamai-tutorial/configure16.png)

1. Insira **o nome** e **descrição da aplicação.**

    ![Screenshot de um diálogo de aplicação RDP mostrando definições para Nome de Aplicação e Descrição.](./media/header-akamai-tutorial/configure17.png)

    ![Screenshot do separador geral da consola Akamai EAA mostrando definições de identidade da aplicação para SECRETRDPAPP.](./media/header-akamai-tutorial/configure18.png)

1. Especifique o Conector que irá fazer a manutenção disto.

    ![Screenshot da consola Akamai EAA mostrando definições para Certificado e Localização. Os conectores associados estão definidos para USWST-CON1.](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autenticação

Clique **em Guardar e ir aos Serviços.**

![A imagem do separador de autenticação da consola Akamai EAA para o SECRETRDPAPP mostrando o botão Salvar e ir para Serviços está no canto inferior direito.](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Serviços

Clique **em Guardar e ir para Definições Avançadas**.

![Screenshot do separador Serviços de consola Da Akamai EAA para SECRETRDPAPP mostrando o Botão Save e ir para o botão AdvancedSettings no canto inferior direito.](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Definições Avançadas

1. Clique **em Guardar e ir para a Implementação**.

    ![Screenshot do separador Configurações Avançadas da consola Akamai EAA para SECRETRDPAPP mostrando as definições para configuração de ambiente de trabalho remoto.](./media/header-akamai-tutorial/configure22.png)

    ![Screenshot do separador Configurações Avançadas da consola Akamai EAA para SECRETRDPAPP mostrando as definições para a configuração de autenticação e verificação de saúde.](./media/header-akamai-tutorial/configure23.png)

    ![Screenshot das definições de cabeçalhos HTTP personalizados da consola Akamai EAA para SECRETRDPAPP com o Botão De implementação no canto inferior direito.](./media/header-akamai-tutorial/configure24.png)

1. experiência End-User

    ![Screenshot de uma janela myapps.microsoft.com com uma imagem de fundo e um sinal em diálogo.](./media/header-akamai-tutorial/enduser03.png)

    ![Screenshot da janela myapps.microsoft.com Apps com ícones para comentários de Add-in, HRWEB, Akamai - CorpApps, Despesas, Grupos e Access.](./media/header-akamai-tutorial/enduser02.png)

1. Acesso Condicional

    ![Screenshot da mensagem de acesso condicional: Aprovar sinal a pedido. Enviámos uma notificação para o seu dispositivo móvel. Por favor, responda para continuar.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Screenshot de um ecrã de aplicações mostrando ícones para o MyHeaderApp e SecretRDPApp.](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Screenshot do ecrã RS do Windows Server 2012 mostrando ícones genéricos do utilizador. Os ícones para administrador, utilizador e utilizador1 mostram que estão inscritos.](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Em alternativa, também pode escrever diretamente o URL de aplicação RDP.

#### <a name="ssh"></a>SSH

1. Vá para adicionar aplicações, escolha **SSH**.

    ![Screenshot do assistente de aplicações de adicionar aplicações da consola Akamai EAA mostrando SSH listado entre as aplicações na secção De Aplicações de Acesso.](./media/header-akamai-tutorial/configure25.png)

1. Insira **o nome** e **descrição da aplicação.**

    ![Screenshot de um diálogo de aplicação SSH mostrando as definições para Nome de Aplicação e Descrição.](./media/header-akamai-tutorial/configure26.png)

1. Configurar identidade de aplicação.

    ![Screenshot do separador geral da consola Akamai EAA mostrando definições de identidade da aplicação para SSH-SECURE.](./media/header-akamai-tutorial/configure27.png)

    a. Especificar Nome / Descrição.

    b. Especifique o Servidor de Aplicações IP/FQDN e a porta para SSH.

    c. Especificar o nome de utilizador /palavra-passe SSH *Check Akamai EAA.

    d. Especifique o Nome do anfitrião Externo.

    e. Especifique a localização do conector e escolha o conector.

#### <a name="authentication"></a>Autenticação

Clique em **Guardar e vá aos Serviços.**

![A imagem do separador de autenticação da consola Akamai EAA para SSH-SECURE mostrando o botão Salvar e ir para Serviços está no canto inferior direito.](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Serviços

Clique **em Guardar e ir para Definições Avançadas**.

![Screenshot do separador Serviços de consola Akamai EAA para SSH-SECURE mostrando o Save e ir para o botão AdvancedSettings no canto inferior direito.](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Definições Avançadas

Clique em Guardar e ir Implantar

![Screenshot do separador Configurações Avançadas da consola Akamai EAA para SSH-SECURE mostrando as definições para a configuração de autenticação e verificação de saúde.](./media/header-akamai-tutorial/configure30.png)

![Screenshot das definições de cabeçalhos HTTP personalizados da consola Akamai EAA para SSH-SECURE com o Save e ir para o botão de implementação no canto inferior direito.](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implementação

1. Clique **na aplicação Implementar**.

    ![Screenshot do separador implementação da consola Akamai EAA para SSH-SECURE mostrando o botão de aplicação Implementar.](./media/header-akamai-tutorial/configure32.png)

1. experiência End-User

    ![Screenshot de uma janela myapps.microsoft.com Sinal em diálogo.](./media/header-akamai-tutorial/enduser03.png)

    ![Screenshot da janela apps para myapps.microsoft.com mostrando ícones para comentários de Add-in, HRWEB, Akamai - CorpApps, Despesas, Grupos e Access.](./media/header-akamai-tutorial/enduser04.png)

1. Acesso Condicional

    ![Screenshot mostrando a mensagem: Aprova o sinal a pedido. Enviámos uma notificação para o seu dispositivo móvel. Por favor, responda para continuar.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Screenshot de um ecrã de aplicações mostrando ícones para MyHeaderApp, SSH Secure e SecretRDPApp.](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Screenshot de uma janela de comando para ssh-secure-go.akamai-access.com mostrando uma solicitação de palavra-passe.](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Screenshot de uma janela de comando para ssh-secure-go.akamai-access.com mostrando informações sobre a aplicação e exibindo um pedido de comandos.](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Autenticação Kerberos

No exemplo abaixo publicaremos um servidor web interno <code>http://frp-app1.superdemo.live</code> e ativaremos o SSO usando o KCD

#### <a name="general-tab"></a>Separador Geral

![Screenshot do separador geral da consola Akamai EAA para MYKERBOROSAPP.](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Separador de autenticação

Atribuir o Fornecedor de Identidade

![Screenshot do separador de autenticação da consola Akamai EAA para MYKERBOROSAPP mostrando o fornecedor de identidade definido para Azure AD SSO.](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Separador de Serviços

![Screenshot do separador Serviços de consola Akamai EAA para MYKERBOROSAPP.](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Definições Avançadas

![Screenshot do separador Configurações Avançadas da consola Akamai EAA para MYKERBOROSAPP mostrando definições para Aplicações Relacionadas e Autenticação.](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> O SPN para o Web Server tem estado no SPN@Domain Formato ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` para esta demonstração. Deixe o resto das definições por defeito.

#### <a name="deployment-tab"></a>Separador de implementação

![Screenshot do separador implementação da consola Akamai EAA para MYKERBOROSAPP mostrando o botão de aplicação Implementar.](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Adição de Diretório

1. Selecione **AD** a partir do dropdown.

    ![Screenshot da janela de diretórios de consolas Akamai EAA mostrando um diálogo Create New Directory com AD selecionado na entrega para o Directory Type.](./media/header-akamai-tutorial/configure33.png)

1. Forneça os dados necessários.

    ![Screenshot da janela SUPERDEMOLIVE da consola Akamai EAA com definições para o Nome de Diretório, Serviço de Diretório, Conector e Mapeamento de Atributos.](./media/header-akamai-tutorial/configure34.png)

1. Verifique a Criação do Diretório.

    ![Screenshot da janela de diretórios de consolas Akamai EAA mostrando que o diretório superdemo.live foi adicionado.](./media/header-akamai-tutorial/directorydomain.png)

1. Adicione os Grupos/OUs que seriam necessários acesso.

    ![Screenshot das definições para o diretório superdemo.live. O ícone que seleciona para adicionar Grupos ou OUs é realçado.](./media/header-akamai-tutorial/addgroup.png)

1. No grupo abaixo é chamado EAAGroup e tem 1 Membro.

    ![Screenshot dos grupos de consolas Akamai EAA na janela do DIRETÓRIO SUPERDEMOLIVE. O Grupo EAA com 1 Utilizador está listado em Grupos.](./media/header-akamai-tutorial/eaagroup.png)

1. Adicione o Diretório ao Fornecedor **Identity**de Identidade clicando em  >  **Fornecedores de Identidade** de Identidade e clique no separador **Diretório** e clique no **diretório de atribuição**.

    ![Screenshot do separador De diretórios de consolas Da Akamai EAA para Azure AD SSO, mostrando superdemo.live na lista de diretórios atualmente atribuídos.](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Configure delegação KCD para a EAA Walkthrough

#### <a name="step-1-create-an-account"></a>Passo 1: Criar uma Conta 

1. No exemplo, usaremos uma conta chamada **EAADelegation**. Pode efetuar isto utilizando os utilizadores do **Ative Directory e o computador** Snappin.

    ![Screenshot do separador De diretórios de consolas Akamai EAA para Azure AD SSO. O diretório superdemo.live está listado em diretórios atualmente atribuídos.](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > O nome de utilizador deve estar num formato específico baseado no **Nome de Interceção de Identidade**. Da figura 1 que vemos é **corpapps.login.go.akamai-access.com**

1. O nome do logotipo do utilizador será:`HTTP/corpapps.login.go.akamai-access.com`

    ![Screenshot mostrando EAADelegation Properties com o primeiro nome definido para "EAADelegation" e user logon name set to HTTP/corpapps.login.go.akamai-access.com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Passo 2: Configurar o SPN para esta conta

1. Com base nesta amostra, o SPN será o mais baixo.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Screenshot de um Aviso de Comando de Administrador mostrando os resultados do conjunto de comandos Http/corpapps.login.go.akamai-access.com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Passo 3: Delegação de Configuração

1. Para a conta EAADelegation clique no separador Delegação.

    ![Screenshot de um aviso de comando do administrador mostrando o comando para configurar o SPN.](./media/header-akamai-tutorial/spn.png)

    * Especifique a utilização de qualquer protocolo de autenticação
    * Clique em Adicionar e Adicione a Conta Pool de Aplicações para o Website Kerberos. Deverá resolver automaticamente a correção da SPN se estiver configurado corretamente.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Passo 4: Criar um ficheiro keytab para o AKAMAI EAA

1. Aqui está a Sintaxe genérica.

1. ktpass /out ActiveDirectorydomain.keytab /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass /crypto All /ptype KRB5_NT_PRINCIPAL

1. Exemplo explicado

    | Fragmento de código | Explicação |
    | - | - |
    | Ktpass /out EAADemo.keytab | Nome do ficheiro Keytab de saída |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | Conta da delegação da EAA |
    | /passe RANDOMPASS | Password da conta da delegação da EAA |
    | /crypto All ptype KRB5_NT_PRINCIPAL | consultar documentação akamai EAA |
    | | |

1. Ktpass /out EAADemo.keytab /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Screenshot de um aviso de comando do administrador mostrando os resultados do comando para a criação de um Ficheiro Keytab para a AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Passo 5: Import Keytab na consola AKAMAI EAA

1. Clique **System**em  >  **Keytabs de**Sistema .

    ![Screenshot da consola Akamai EAA mostrando Keytabs sendo selecionados a partir do menu System.](./media/header-akamai-tutorial/keytabs.png)

1. No Tipo Keytab escolha **a Delegação Kerberos.**

    ![Screenshot do ecrã EAAKEYTAB da consola EAAA da Akamai mostrando as definições de Keytab. O Tipo Keytab está definido para a delegação Kerberos.](./media/header-akamai-tutorial/keytabdelegation.png)

1. Certifique-se de que o Keytab aparece como Implantado e Verificado.

    ![Screenshot do ecrã KEYTABS da consola Da Akamai EAA que lista o Keytab da EAA como "Keytab implantado e verificado".](./media/header-akamai-tutorial/keytabs02.png)

1. Experiência do Utilizador

    ![Screenshot do Sinal em diálogo em myapps.microsoft.com. ](./media/header-akamai-tutorial/enduser03.png)

    ![Screenshot da janela Apps para myapps.microsoft.com mostrando ícones da App.](./media/header-akamai-tutorial/enduser04.png)

1. Acesso Condicional

    ![Screenshot mostrando um sinal de aprovação na mensagem de pedido. a mensagem.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Screenshot de um ecrã de aplicações mostrando ícones para MyHeaderApp, SSH Secure, SecretRDPApp e myKerberosApp.](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Screenshot do ecrã de respingo para o myKerberosApp. A mensagem "Welcome superdemo\user1" é exibida sobre uma imagem de fundo.](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Criar utilizador de teste Akamai

Nesta secção, cria-se um utilizador chamado B.Simon em Akamai. Trabalhe com a [equipa de suporte do Cliente Akamai](https://www.akamai.com/us/en/contact-us/) para adicionar os utilizadores na plataforma Akamai. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Akamai no Painel de Acesso, deverá ser automaticamente inscrito no Akamai para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a Akamai com a Azure AD](https://aad.portal.azure.com/)