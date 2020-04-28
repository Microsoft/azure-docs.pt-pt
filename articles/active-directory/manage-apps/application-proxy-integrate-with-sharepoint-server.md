---
title: Ativar o acesso remoto ao SharePoint - Procuração de aplicação AD Azure
description: Cobre o básico sobre como integrar um servidor SharePoint no local com o Proxy de Aplicação AD Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481301"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD

Este guia passo a passo explica como integrar uma quinta sharePoint no local com o Azure Ative Directory (Azure AD) Application Proxy.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar a configuração, necessita dos seguintes recursos:
- Uma quinta SharePoint 2013 ou mais recente.
- Um inquilino da AD Azure com um plano que inclui procuração de aplicação. Saiba mais sobre [os planos de Anúncios Azure e os preços.](https://azure.microsoft.com/pricing/details/active-directory/)
- Um [domínio personalizado e verificado](../fundamentals/add-custom-domain.md) no inquilino da AD Azure.
- No local, o Ative Directory sincronizado com o Azure AD Connect, através do qual os utilizadores podem [iniciar sessão no Azure](../hybrid/plan-connect-user-signin.md).
- Um conector Proxy de aplicação instalado e em funcionamento numa máquina dentro do domínio corporativo.

Configurar o SharePoint com procuração de aplicação requer dois URLs:
- Um URL externo, visível para utilizadores finais e determinado em Azure AD. Este URL pode usar um domínio personalizado. Saiba mais sobre [trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).
- Um URL interno, conhecido apenas dentro do domínio corporativo e nunca usado diretamente.

> [!IMPORTANT]
> Para se certificar de que os links estão mapeados corretamente, siga estas recomendações para o URL interno:
> - Utilize HTTPS.
> - Não use portas personalizadas.
> - No Sistema de Nome de Domínio corporativo (DNS), crie um anfitrião (A) para apontar para o SharePoint WFE (ou equilibrador de carga), e não um pseudónimo (CName).

Este artigo utiliza os seguintes valores:
- URL interno:`https://sharepoint`
- URL externo:`https://spsites-demo1984.msappproxy.net/`
- Conta de piscina de aplicações para a aplicação web SharePoint:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Passo 1: Configure uma aplicação em AD Azure que utilize procuração de aplicação

Neste passo, cria uma aplicação no seu inquilino do Diretório Ativo Azure que utiliza o Application Proxy. Define o URL externo e especifica o URL interno, ambos utilizados mais tarde no SharePoint.

1. Crie a aplicação conforme descrito com as seguintes definições. Para obter instruções passo a passo, consulte [as aplicações de publicação utilizando o Proxy de Aplicação AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno sharePoint que será definido `https://sharepoint`mais tarde no SharePoint, tais como .
   * **Pré-Autenticação**: Diretório Ativo Azure
   * **Traduzir URLs em Cabeçalhos:** Não
   * **Traduzir URLs no Corpo de Aplicação:** Não

   ![Publicar SharePoint como aplicação](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Depois de publicada a sua aplicação, siga estes passos para configurar as definições de início de sessão individuais:

   1. Na página de aplicação no portal, selecione **Single sign-on**.
   1. Para **o modo de acesso único,** selecione **autenticação integrada do Windows**.
   1. Detete o **SPN** de Aplicação Interna no valor que definiu anteriormente. Para este exemplo, `HTTP/sharepoint`o valor é .
   1. Sob **identidade de login delegada,** selecione a opção mais adequada para a sua configuração de floresta de Diretório Ativo. Por exemplo, se tiver um único domínio de Diretório Ativo na sua floresta, selecione o nome da **conta SAM** no local (como mostra a seguinte imagem). Mas se os seus utilizadores não estiverem no mesmo domínio que o SharePoint e os servidores do Conector proxy da aplicação, selecione o nome principal do **utilizador no local** (não mostrado na imagem).

   ![Configure autenticação integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Para terminar a configuração da sua aplicação, vá à secção **utilizadores e grupos** e atribua aos utilizadores o acesso a esta aplicação. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Passo 2: Configurar a aplicação web SharePoint

A aplicação web SharePoint deve ser configurada com Kerberos e os mapeamentos de acesso alternativos apropriados para funcionar corretamente com o Proxy de Aplicação AD Azure. Existem duas opções possíveis:

- Crie uma nova aplicação web e utilize apenas a zona Padrão. Esta é a opção preferida, pois oferece a melhor experiência com o SharePoint (por exemplo, os links nos alertas de e-mail gerados pelo SharePoint apontam sempre para a zona Padrão).
- Estenda uma aplicação web existente para configurar Kerberos numa zona não predefinida.

> [!IMPORTANT]
> Independentemente da zona utilizada, a conta de pool de aplicações da aplicação web do SharePoint deve ser uma conta de domínio para a Kerberos funcionar corretamente.

### <a name="provision-the-sharepoint-web-application"></a>Provisiona a aplicação web SharePoint

- Se criar uma nova aplicação web e utilizar apenas a zona Predefinida (opção preferida):

    1. Inicie a Shell de **Gestão sharePoint** e execute o seguinte script:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Abra o site da **Administração Central sharePoint.**
    1. Em definições do **sistema,** selecione **Configurar mapeamentos de acesso alternativos**. Abre a caixa de coleção de mapeamento de **acesso alternativo.**
    1. Filtre o ecrã com a nova aplicação web e confirme que vê algo assim:

       ![Mapeamentos alternativos de acesso da aplicação web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se estender uma aplicação web existente a uma nova zona (caso não possa utilizar a zona Predefinida):

    1. Inicie a Shell de Gestão sharePoint e execute o seguinte script:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Abra o site da **Administração Central sharePoint.**
    1. Em definições do **sistema,** selecione **Configurar mapeamentos de acesso alternativos**. Abre a caixa de coleção de mapeamento de **acesso alternativo.**
    1. Filtre o ecrã com a aplicação web que foi estendida e confirme que vê algo assim:

        ![Mapeamentos alternativos de acesso de aplicação alargada](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Certifique-se de que a aplicação web SharePoint está a ser realizada sob uma conta de domínio

Para identificar a conta que executa o conjunto de aplicações da aplicação web do SharePoint e para se certificar de que é uma conta de domínio, siga estes passos:

1. Abra o site da **Administração Central sharePoint.**
1. Vá à **Segurança** e selecione contas de **serviço Configure**.
1. Selecione **Web Application Pool - YourWebApplicationName**.

   ![Escolhas para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Confirme que **Selecione uma conta para este componente** devolve uma conta de domínio, e lembre-se dela, uma vez que será necessária no próximo passo.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Certifique-se de que um certificado HTTPS está configurado para o site IIS da zona Extranet

Uma vez que o`https://SharePoint/`URL Interno utiliza o protocolo HTTPS (), um certificado deve ser definido no site dos Serviços de Informação da Internet (IIS).

1. Abra a consola Windows PowerShell.
1. Executar o seguinte script para gerar um certificado auto-assinado e adicioná-lo à loja MY do computador:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Os certificados auto-assinados são adequados apenas para efeitos de ensaio. Em ambientes de produção, recomendamos vivamente que utilize certificados emitidos por uma autoridade de certificados.

1. Abra a consola do Gestor de Serviços de Informação da Internet.
1. Expandir o servidor na vista da árvore, expandir **sites,** selecionar o site **SharePoint - AAD Proxy** e selecionar **Ligações**.
1. Selecione **a vinculação https** e, em seguida, selecione **Editar**.
1. No campo de certificadoS TLS/SSL, escolha o certificado **SharePoint** e, em seguida, selecione **OK**.

Pode agora aceder ao site SharePoint externamente através do Proxy de Aplicação AD Azure.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Passo 3: Configure Kerberos Constrangida Delegação

Os utilizadores irão inicialmente autenticar em Azure AD e depois para o SharePoint utilizando kerberos através do conector DeProxy Azure AD. Para permitir que o conector obtenha um token Kerberos em nome do utilizador da AD Azure, deve configurar a Delegação Limitada kerberos (KCD) com a transição protocolar. Para saber mais sobre a KCD, consulte a visão geral da [Delegação Limitada kerberos.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Desloque o SPN para a conta de serviço SharePoint

Neste artigo, o URL `https://sharepoint`interno é , e assim o nome `HTTP/sharepoint`principal de serviço (SPN) é . Deve substituir esses valores pelos valores que correspondem ao seu ambiente.
Para registar a `HTTP/sharepoint` SPN para `Contoso\spapppool`a conta de pool de aplicação SharePoint, execute o seguinte comando a partir de um pedido de comando, como administrador do domínio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

O `Setspn` comando procura a SPN antes de o adicionar. Se o SPN já existir, vê-se um erro de **valor SPN duplicado.** Nesse caso, considere remover o SPN existente se não estiver definido na conta de piscina de aplicação correta. Pode verificar se o SPN foi adicionado `Setspn` com sucesso, executando o comando com a opção -L. Para saber mais sobre este comando, consulte [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Certifique-se de que o conector é confiável para a delegação ao SPN que foi adicionado à conta de piscina de aplicações SharePoint

Configure o KCD de modo a que o serviço de proxy de aplicação AD Azure possa delegar identidades de utilizador na conta de pool de aplicação SharePoint. Configure o KCD permitindo ao conector Proxy de aplicação recuperar bilhetes Kerberos para os seus utilizadores que tenham sido autenticados em Azure AD. Em seguida, esse servidor passa o contexto para a aplicação-alvo (SharePoint neste caso).

Para configurar o KCD, siga estes passos para cada máquina de conector:

1. Inscreva-se num controlador de domínio como administrador de domínio e, em seguida, abra utilizadores e computadores de diretório ativo.
1. Encontre o computador que executa o conector Azure AD Proxy. Neste exemplo, é o próprio servidor do SharePoint.
1. Clique duas vezes no computador e, em seguida, selecione o separador **Delegação.**
1. Certifique-se de que as opções de delegação são definidas para **confiar neste computador apenas para delegação aos serviços especificados**. Em seguida, selecione Utilize qualquer protocolo de **autenticação**.
1. Selecione o botão **Adicionar,** selecione **Utilizadores ou Computadores**e localize a conta de piscina da aplicação SharePoint. Por exemplo: `Contoso\spapppool`.
1. Na lista de SPNs, selecione o que criou anteriormente para a conta de serviço.
1. Selecione **OK** e, em seguida, selecione **OK** novamente para guardar as suas alterações.
  
   ![Configurações da delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Está agora pronto para iniciar sessão no SharePoint utilizando o URL externo e autenticando com o Azure.

## <a name="troubleshoot-sign-in-errors"></a>Erros de sessão de problemas

Se o login no site não estiver a funcionar, pode obter mais informações sobre o problema nos registos do Conector: Da máquina que executa o conector, abra o espectador do evento, aceda a Registos de **Aplicações e Serviços** > **Microsoft** > **AadApplicationProxy** > **Connector,** e inspecione o registo do **Administrador.**

## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md)
* [Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)
