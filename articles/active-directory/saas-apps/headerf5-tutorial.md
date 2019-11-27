---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5eb5cedf14af9a013a5b6a1eba5df40d665cbad5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74181767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure Active Directory (Azure AD). Ao integrar o F5 ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao F5.
* Habilite seus usuários a serem automaticamente conectados ao F5 com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).

* F5 assinatura habilitada para SSO (logon único).

* A implantação da solução conjunta requer a seguinte licença:

    * F5 BIG-IP® melhor pacote (ou) 

    * F5 BIG-IP Access Policy Manager™ (APM) licença autônoma 

    * A licença de complemento do™ Gerenciador de políticas de acesso (APM) do F5 BIG-IP em um BIG-IP F5 existente,® o Gerenciador de tráfego local™ (LTM).

    * Além da licença acima, o sistema F5 também pode ser licenciado com: 

        * Uma assinatura de filtragem de URL para usar o banco de dados de categoria de URL

        * Uma assinatura do F5 IP Intelligence para detectar e bloquear invasores conhecidos e tráfego mal-intencionado
     
        * Um HSM (módulo de segurança de hardware) de rede para proteger e gerenciar chaves digitais para autenticação forte

* O sistema de BIG-IP F5 é provisionado com módulos do APM (LTM é opcional)

* Embora seja opcional, é altamente recomendável implantar os sistemas F5 em um [grupo de dispositivos de sincronização/failover](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), que inclui o par de espera ativo, com um endereço IP flutuante para ha (alta disponibilidade). A redundância de interface adicional pode ser obtida usando o LACP (protocolo de controle de agregação de link). O LACP gerencia as interfaces físicas conectadas como uma única interface virtual (grupo de agregação) e detecta quaisquer falhas de interface dentro do grupo.

* Para aplicativos Kerberos, uma conta de serviço do AD local para delegação restrita.  Consulte a [documentação do F5](https://support.f5.com/csp/article/K43063049) para criar uma conta de delegação do AD.

## <a name="access-guided-configuration"></a>Configuração guiada de acesso

* O acesso guiado à configuração ' tem suporte em F5 TMOS versão 13.1.0.8 e superior. Se o seu sistema BIG-IP estiver executando uma versão abaixo de 13.1.0.8, consulte a seção **Configuração avançada** .

* A configuração interativa de acesso apresenta uma experiência de usuário totalmente nova e simplificada. Essa arquitetura baseada em fluxo de trabalho fornece etapas de configuração reentrantes e intuitivas adaptadas para a topologia selecionada.

* Antes de prosseguir para a configuração, atualize a configuração guiada baixando o pacote de caso de uso mais recente de [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Para atualizar, siga o procedimento abaixo.

    >[!NOTE]
    >As capturas de tela abaixo são para a versão mais recente lançada (BIG-IP 15,0 com AGC versão 5,0). As etapas de configuração a seguir são válidas para esse caso de uso em 13.1.0.8 para a versão BIG-IP mais recente.

1. Na interface do usuário da Web do F5 BIG-IP, clique em **acesso > > guia Configuração**.

1. Na página **configuração guiada** , clique em **Atualizar configuração guiada** no canto superior esquerdo.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure14.png) 

1. Na tela pop de configuração do guia de atualização, selecione **escolher arquivo** para carregar o pacote de caso de uso baixado e clique no botão **carregar e instalar** .

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure15.png) 

1. Quando a atualização for concluída, clique no botão **continuar** .

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do F5 pode ser configurado de três maneiras diferentes.

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](#configure-f5-single-sign-on-for-header-based-application)

- [Configurar o logon único F5 para o aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chave

* Além do suporte à integração nativa Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-reportado, F5 estende o acesso seguro para aplicativos de autenticação baseados em herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseados em cabeçalho

* Aplicativos de autenticação Kerberos

* Autenticação anônima ou nenhum aplicativo de autenticação embutido

* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)

* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da Galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar o logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com F5 usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do F5](#configure-f5-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Create F5 Test User](#create-f5-test-user)** – para ter um equivalente de B. Simon em F5 que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **F5** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize XML de metadados de **Federação** e **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar F5** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao F5.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .
1. Clique em **acesso condicional** .
1. Clique em **nova política**.
1. Agora você pode ver seu aplicativo F5 como um recurso para a política de CA e aplicar qualquer acesso condicional, incluindo autenticação multifator, controle de acesso baseado em dispositivo ou política de proteção de identidade.

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar o logon único F5 para o aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Configurar o logon único F5 para o aplicativo baseado em cabeçalho

### <a name="guided-configuration"></a>Configuração guiada

1. Abra uma nova janela do navegador da Web e entre no site da empresa do F5 (baseado em cabeçalho) como administrador e execute as seguintes etapas:

1. Navegue até **sistema > gerenciamento de certificados > gerenciamento de certificado de tráfego > lista de certificados SSL**. Selecione **importar** no canto direito. Especifique um **nome de certificado** (será referenciado mais tarde na configuração). Na **origem do certificado**, selecione carregar arquivo especifique o certificado baixado do Azure ao configurar o logon único do SAML. Clique em **Importar**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure12.png)
 
1. Além disso, você precisará **de um certificado SSL para o nome de host do aplicativo. Navegue até sistema > gerenciamento de certificados > gerenciamento de certificado de tráfego > lista de certificados SSL**. Selecione **importar** no canto direito. O **tipo de importação** será **PKCS 12 (IIS)** . Especifique um **nome de chave** (que será referenciado mais tarde na configuração) e especifique o arquivo PFX. Especifique a **senha** para o PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nome do aplicativo é `Headerapp.superdemo.live`, estamos usando um certificado curinga nosso KeyName é `WildCard-SuperDemo.live`.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure13.png)

1. Usaremos a experiência guiada para configurar a Federação do Azure AD e o acesso ao aplicativo. Vá para – F5 BIG-IP **principal** e selecione **acessar > configuração guiada > Federação > provedor de serviços SAML**. Clique em **Avançar** e em **Avançar** para iniciar a configuração.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure01.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure02.png)
 
1. Forneça um **nome de configuração**. Especifique a **ID da entidade** (o mesmo que você configurou na configuração de aplicativo do Azure AD). Especifique o **nome do host**. Adicione uma **Descrição** para referência. Aceite as entradas padrão restantes e selecione e, em seguida, clique em **salvar & avançar**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure03.png) 

1. Neste exemplo, estamos criando um novo servidor virtual como 192.168.30.20 com a porta 443. Especifique o endereço IP do servidor virtual no **endereço de destino**. Selecione o **perfil cliente SSL**, selecione criar novo. Especifique o certificado de aplicativo carregado anteriormente, (o certificado curinga neste exemplo) e a chave associada e, em seguida, clique em **salvar & avançar**.

    >[!NOTE]
    >Neste exemplo, nosso servidor webinterno está em execução na porta 888 e queremos publicá-lo com 443.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure04.png) 

1. Em **selecionar método para configurar o conector IDP**, especifique metadados, clique em escolher arquivo e carregue o arquivo XML de metadados baixado anteriormente do Azure AD. Especifique um **nome** exclusivo para o conector IDP do SAML. Escolha o **certificado de assinatura de metadados** que foi carregado anteriormente. Clique em **salvar & avançar**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure05.png)
 
1. Em **selecionar um pool**, especifique **criar novo** (como alternativa, selecione um pool que já existe). Deixe outro valor como padrão. Em servidores de pool, digite o endereço IP em **endereço IP/nome do nó**. Especifique a **porta**. Clique em **salvar & avançar**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure06.png)

1. Na tela Configurações de logon único, selecione **habilitar logon único**. Em tipo de logon único selecionado, escolha **baseado em cabeçalho http**. Substitua **Session. SAML. Last. Identity** por **Session. SAML. Last. attr. Name. Identity** sob a origem do nome de usuário (essa variável foi definida usando o mapeamento de declarações no Azure AD). Em cabeçalhos de SSO.

    * **Cabeçalho: myauthorização**

    * **Valor do cabeçalho:% {Session. SAML. Last. attr. Name. Identity}**

    * Clique em **salvar & avançar**

    Consulte o apêndice para obter uma lista completa de variáveis e valores. Você pode adicionar mais cabeçalhos conforme necessário.

    >[!NOTE]
    >Nome da conta é a conta de delegação F5 criada (verifique a documentação F5).

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure07.png) 

1. Para fins deste guia, ignoraremos as verificações de ponto de extremidade.  Consulte a documentação do F5 para obter detalhes. Selecione **salvar & avançar**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure08.png)

1. Aceite os padrões e clique em **salvar & avançar**. Consulte a documentação do F5 para obter detalhes sobre as configurações de gerenciamento de sessão SAML.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure09.png)

1. Examine a tela Resumo e selecione **implantar** para configurar o Big-IP. clique em **concluir**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure10.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Configuração Avançada

Esta seção deve ser usada se você não puder usar a configuração guiada ou desejar adicionar/modificar parâmetros adicionais. Você precisará do certificado SSL para o nome do host do aplicativo.

1. Navegue até **sistema > gerenciamento de certificados > gerenciamento de certificado de tráfego > lista de certificados SSL**. Selecione **importar** no canto direito. O **tipo de importação** será **PKCS 12 (IIS)** . Especifique um **nome de chave** (que será referenciado mais tarde na configuração) e especifique o arquivo PFX. Especifique a **senha** para o PFX. Clique em **Importar**.

    >[!NOTE]
    >No exemplo, o nome do aplicativo é `Headerapp.superdemo.live`, estamos usando um certificado curinga nosso KeyName é `WildCard-SuperDemo.live`.
  
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Adicionando um novo servidor Web a BigIP-F5

1. Clique em **principal > IApps > Serviços de Aplicativos > aplicativo > criar**.

1. Forneça o **nome** e, em **modelo** , escolha **F5. http**.
 
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure18.png)

1. Publicaremos nosso HeaderApp2 externamente como HTTPS nesse caso, **como o sistema Big IP lida com o tráfego SSL**? especificamos o **término de SSL do cliente, o texto não criptografado para servidores (descarregamento de SSL)** . Especifique seu certificado e a chave sob qual certificado SSL você deseja usar? e **qual chave privada SSL você deseja usar?** . Especifique o IP do servidor virtual em **qual endereço IP você deseja usar para o servidor virtual?** . 

    * **Especificar outros detalhes**

        * FQDN  

        * Especifique o pool de aplicativos de saída ou crie um novo.

        * Se estiver criando um novo servidor de aplicativos, especifique o **endereço IP interno** e o **número da porta**.

        ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure19.png) 

1. Clique em **concluído**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure20.png) 

1. Verifique se as propriedades do aplicativo podem ser modificadas. Clique em **principal > IApps > serviços de aplicativos: aplicativos > > HeaderApp2**. Desmarque **as atualizações estritas** (modificaremos algumas configurações fora da GUI). Clique no botão **Atualizar** .

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure21.png) 

1. Neste ponto, você deve ser capaz de navegar pelo servidor virtual.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Configurando F5 como SP e Azure como IDP

1.  Clique em **acesso > federação > provedor de serviços SAML > serviço SP Local > clique em criar ou + assinar**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure22.png)

1. Especifique os detalhes para o serviço do provedor de serviços. Especifique o **nome** que representa a configuração de SP F5. Especifique a **ID da entidade** (geralmente igual à URL do aplicativo).

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure23.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure24.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure25.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure26.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure27.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Criar conector IDP

1. Clique no botão **ligar/desassociar conectores IDP** , selecione **criar novo conector IDP** e escolha de **metadados** opção, em seguida, execute as seguintes etapas:
 
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure29.png)

    a. Navegue até o arquivo Metadata. xml baixado do Azure AD e especifique um **nome de provedor de identidade**.

    b. Clique em **OK**.

    c. O conector é criado e o certificado é pronto automaticamente do arquivo XML de metadados.
    
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure30.png)

    d. Configure o F5BIG-IP para enviar todas as solicitações ao Azure AD.

    e. Clique em **Adicionar nova linha**, escolha **AzureIDP** (conforme criado nas etapas anteriores, especifique 

    f. **Fonte correspondente =% {Session. Server. landinguri}** 

    g. **Valor correspondente =/** *

    h. Clique em **Atualizar**

    i. Clique em **OK**

    j. **A instalação do IDP do SAML foi concluída**
    
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Configurar a política F5 para redirecionar os usuários para o IDP do Azure SAML

1. Para configurar a política F5 para redirecionar os usuários para o IDP do Azure SAML, execute as seguintes etapas:

    a. Clique em **principal > acesso > perfil/políticas > perfis de acesso**.

    b. Clique no botão **criar** .

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure32.png)
 
    c. Especifique o **nome** (HeaderAppAzureSAMLPolicy no exemplo).

    d. Você pode personalizar outras configurações, consulte a documentação de F5.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure33.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure34.png) 

    e. Clique em **concluído**.

    f. Quando a criação da política for concluída, clique na política e vá para a guia **política de acesso** .

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure35.png)
 
    g. Clique no **Editor de política Visual**, editar **política de acesso para o link de perfil** .

    h. Clique no sinal de + no editor de política visual e escolha **autenticação SAML**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure36.png)

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure37.png)
 
    i. Clique em **Adicionar item**.

    j. Em **Propriedades** , especifique **nome** e, em **servidor AAA** , selecione o SP configurado anteriormente, clique em **salvar**.
 
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure38.png)

    k. A política básica está pronta. você pode personalizar a política para incorporar fontes/armazenamentos de atributos adicionais.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure39.png)
 
    l. Certifique-se de clicar no link **aplicar política de acesso** na parte superior.

### <a name="apply-access-profile-to-the-virtual-server"></a>Aplicar perfil de acesso ao servidor virtual

1. Atribua o perfil de acesso ao servidor virtual para F5 BIG-IP APM para aplicar as configurações de perfil ao tráfego de entrada e executar a política de acesso definida anteriormente.

    a. Clique em **principal** > **tráfego local** > **servidores virtuais**.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure40.png)
 
    b. Clique na seção servidor virtual, role até a **política de acesso** , no menu suspenso perfil de **acesso** e selecione a política SAML criada (no exemplo HeaderAppAzureSAMLPolicy)

    c. Clique em **Atualizar**
 
    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure41.png)

    d. Crie um® F5 BIG-IP Irule usando para extrair os atributos SAML personalizados da declaração de entrada e passá-los como cabeçalhos HTTP para o aplicativo de teste de back-end. Clique em **principal > tráfego Local > iRules > lista de irule usando > clique em criar**

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure42.png)
 
    e. Cole o texto Irule usando de BIG-IP F5 abaixo na janela de definição.

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure43.png)
 
    Quando RULE_INIT {Set static::d ebug 0} quando ACCESS_ACL_ALLOWED {

    Defina AZUREAD_USERNAME [ACCESS:: session data Get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] se {$static::d ebug} {log local0. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} se {! ( [HTTP:: o cabeçalho existe "AZUREAD_USERNAME"]) } {HTTP:: cabeçalho inserir "AZUREAD_USERNAME" $AZUREAD _USERNAME}

    Defina AZUREAD_DISPLAYNAME [ACCESS:: session data Get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] se {$static::d ebug} {log local0. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} se {! ( [HTTP:: o cabeçalho existe "AZUREAD_DISPLAYNAME"]) } {HTTP:: cabeçalho inserir "AZUREAD_DISPLAYNAME" $AZUREAD _DISPLAYNAME}

    Defina AZUREAD_EMAILADDRESS [ACCESS:: session data Get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] se {$static::d ebug} {log local0. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} se {! ( [HTTP:: o cabeçalho existe "AZUREAD_EMAILADDRESS"]) } {HTTP:: cabeçalho inserir "AZUREAD_EMAILADDRESS" $AZUREAD _EMAILADDRESS}}

    **Exemplo de saída abaixo**

    ![Configuração de F5 (baseada em cabeçalho)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B. Simon em F5. Trabalhe com a [equipe de suporte ao cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários à plataforma F5. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco F5 no painel de acesso, você deverá ser conectado automaticamente ao F5 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único F5 para o aplicativo Kerberos](kerbf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](advance-kerbf5-tutorial.md)

