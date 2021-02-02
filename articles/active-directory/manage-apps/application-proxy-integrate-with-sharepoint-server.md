---
title: Permitir o acesso remoto ao SharePoint - Azure AD Application Proxy
description: Cobre o básico sobre como integrar um servidor SharePoint no local com O Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b5895d016e2d9d9b471218bc083ea7585254b45
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258686"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD

Este guia passo a passo explica como integrar uma fazenda SharePoint no local com a Azure Ative Directory (Azure AD) Application Proxy.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar a configuração, precisa dos seguintes recursos:
- Uma quinta SharePoint 2013 ou mais recente.
- Um inquilino da AD Azure com um plano que inclui procuração de aplicação. Saiba mais sobre [os planos e preços da AZure AD.](https://azure.microsoft.com/pricing/details/active-directory/)
- Um [domínio personalizado e verificado](../fundamentals/add-custom-domain.md) no inquilino AZure AD.
- No local, ative Directy sincronizado com Azure AD Connect, através do qual os utilizadores podem [iniciar sação no Azure](../hybrid/plan-connect-user-signin.md).
- Um conector Proxy de aplicação instalado e em funcionamento numa máquina dentro do domínio corporativo.

Configurar o SharePoint com o Application Proxy requer dois URLs:
- Um URL externo, visível para os utilizadores finais e determinado em Azure AD. Este URL pode usar um domínio personalizado. Saiba mais sobre [trabalhar com domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- Um URL interno, conhecido apenas dentro do domínio corporativo e nunca usado diretamente.

> [!IMPORTANT]
> Para garantir que os links estão corretamente mapeados, siga estas recomendações para o URL interno:
> - Utilize HTTPS.
> - Não use portas personalizadas.
> - No sistema de nome de domínio corporativo (DNS), crie um anfitrião (A) para apontar para o SharePoint WFE (ou balanceador de carga), e não um pseudónimo (CName).

Este artigo utiliza os seguintes valores:
- URL interno: `https://sharepoint`
- URL externo: `https://spsites-demo1984.msappproxy.net/`
- Conta de conjunto de aplicações para a aplicação web SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Passo 1: Configurar uma aplicação em Azure AD que usa Proxy de aplicação

Neste passo, você cria uma aplicação no seu inquilino Azure Ative Directory que usa Application Proxy. Você define o URL externo e especifica o URL interno, ambos usados mais tarde no SharePoint.

1. Crie a aplicação conforme descrito com as seguintes definições. Para obter instruções passo a passo, consulte [as aplicações de publicação utilizando o Azure AD Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno sharePoint que será definido mais tarde no SharePoint, tal como `https://sharepoint` .
   * **Pré-Autenticação**: Diretório Ativo Azure
   * **Traduzir URLs em Cabeçalhos**: Não
   * **Traduzir URLs no Corpo de Aplicação:** Não

   ![Publicar SharePoint como aplicação](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Depois de a sua aplicação ser publicada, siga estes passos para configurar as definições de inscrição única:

   1. Na página da aplicação no portal, selecione **Início de sessão único**.
   1. Em **Modo de Início de Sessão Único**, selecione **Autenticação Integrada do Windows**.
   1. Defina **a Aplicação Interna SPN** ao valor que definiu anteriormente. Para este exemplo, o valor `HTTP/sharepoint` é.
   1. Em **Identidade de Login Delegada,** selecione a opção mais adequada para a configuração da floresta do Diretório Ativo. Por exemplo, se tiver um único domínio ative directory na sua floresta, selecione **o nome da conta SAM no local** (como mostrado na imagem seguinte). Mas se os seus utilizadores não estiverem no mesmo domínio que o SharePoint e os servidores do Conector de Procuração de Aplicações, selecione **o nome principal do utilizador no local** (não mostrado na imagem).

   ![Configurar autenticação integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Para terminar a configuração da aplicação, aceda à secção **Utilizadores e grupos** e atribua os utilizadores que poderão aceder a esta aplicação. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Passo 2: Configurar a aplicação web SharePoint

A aplicação web SharePoint deve ser configurada com Kerberos e os mapeamentos de acesso alternativo apropriados para funcionar corretamente com o Azure AD Application Proxy. Existem duas opções possíveis:

- Crie uma nova aplicação web e utilize apenas a zona Padrão. Esta é a opção preferida, pois oferece a melhor experiência com o SharePoint (por exemplo, os links nos alertas de e-mail gerados pelo SharePoint apontam sempre para a zona padrão).
- Estender uma aplicação web existente para configurar Kerberos numa zona não padrão.

> [!IMPORTANT]
> Independentemente da zona utilizada, a conta de grupo de aplicações da aplicação web SharePoint deve ser uma conta de domínio para a Kerberos funcionar corretamente.

### <a name="provision-the-sharepoint-web-application"></a>Provisão da aplicação web SharePoint

- Se criar uma nova aplicação web e utilizar apenas a zona padrão (opção preferida):

    1. Inicie a **Shell de Gestão sharePoint** e execute o seguinte script:

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

    2. Abra o site **da Administração Central sharePoint.**
    1. Nas **Definições do Sistema**, selecione **Configure Cartminhos de Acesso Alternativos**. Abre a caixa **de recolha de mapeamento de acesso alternativo.**
    1. Filtre o visor com a nova aplicação web e confirme que vê algo assim:

       ![Mapeamentos de acesso alternativos da aplicação web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se estender uma aplicação web existente para uma nova zona (caso não possa utilizar a zona Padrão):

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

    2. Abra o site **da Administração Central sharePoint.**
    1. Nas **Definições do Sistema**, selecione **Configure Cartminhos de Acesso Alternativos**. Abre a caixa **de recolha de mapeamento de acesso alternativo.**
    1. Filtre o visor com a aplicação web que foi estendida e confirme que vê algo assim:

        ![Mapeamentos de acesso alternativos de aplicação alargada](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Certifique-se de que a aplicação web SharePoint está a decorrer numa conta de domínio

Para identificar a conta que executa o conjunto de aplicações da aplicação web SharePoint e para se certificar de que é uma conta de domínio, siga estes passos:

1. Abra o site **da Administração Central sharePoint.**
1. Vá à **Segurança** e selecione **contas de serviço Configure**.
1. Selecione **Web Application Pool - YourWebApplicationName**.

   ![Escolhas para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Confirme que **Selecione uma conta para este componente** devolva uma conta de domínio e lembre-se, uma vez que será necessária no passo seguinte.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Certifique-se de que um certificado HTTPS está configurado para o site IIS da zona extranet

Uma vez que o URL interno utiliza o protocolo HTTPS `https://SharePoint/` (), um certificado deve ser definido no site dos Serviços de Informação da Internet (IIS).

1. Abra a consola Windows PowerShell.
1. Execute o seguinte script para gerar um certificado auto-assinado e adicione-o à loja MY do computador:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Os certificados auto-assinados são adequados apenas para efeitos de teste. Em ambientes de produção, recomendamos vivamente que utilize certificados emitidos por uma autoridade de certificados.

1. Abra a consola Internet Information Services Manager.
1. Expandir o servidor na vista da árvore, expandir **sites,** selecionar o **sharePoint - site de procuração AAD** e selecionar **Ligações**.
1. Selecione **https binding** e, em seguida, selecione **Editar**.
1. No campo de certificados TLS/SSL, escolha o certificado **SharePoint** e, em seguida, selecione **OK**.

Agora pode aceder ao site do SharePoint externamente através do Azure AD Application Proxy.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Passo 3: Configurar a delegação restrita kerberos

Os utilizadores irão inicialmente autenticar em Azure AD e depois no SharePoint utilizando kerberos através do conector Azure AD Proxy. Para permitir que o conector obtenha um token Kerberos em nome do utilizador AZure AD, deve configurar a Delegação Restrita Kerberos (KCD) com a transição protocolar. Para saber mais sobre o KCD, consulte [a visão geral da delegação restrita de Kerberos.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Definir o SPN para a conta de serviço SharePoint

Neste artigo, o URL interno é `https://sharepoint` , e assim o nome principal de serviço (SPN) é `HTTP/sharepoint` . Deve substituir esses valores pelos valores que correspondem ao seu ambiente.
Para registar a SPN `HTTP/sharepoint` para a conta de pool de aplicações SharePoint, executar o seguinte comando a partir de uma `Contoso\spapppool` solicitação de comando, como administrador do domínio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

O `Setspn` comando procura o SPN antes de o adicionar. Se o SPN já existir, vê um erro **de Valor SPN duplicado.** Nesse caso, considere remover a SPN existente se não estiver definida na conta de conjunto de aplicações correta. Pode verificar se o SPN foi adicionado com sucesso executando o `Setspn` comando com a opção -L. Para saber mais sobre este comando, consulte [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Certifique-se de que o conector é confiável para a delegação da SPN que foi adicionada à conta do conjunto de aplicações SharePoint

Configure o KCD para que o serviço de procuração de aplicações AD Azure possa delegar identidades dos utilizadores na conta do pool de aplicações SharePoint. Configure a KCD ao ativar o conector do Proxy de Aplicações para obter permissões Kerberos para os utilizadores que foram autenticados no Azure Active Directory. Em seguida, esse servidor passa o contexto para a aplicação-alvo (SharePoint neste caso).

Para configurar o KCD, siga estes passos para cada máquina de conector:

1. Inscreva-se num controlador de domínio como administrador de domínio e, em seguida, abra Utilizadores e Computadores de Diretório Ativo.
1. Encontre o computador a executar o conector Azure AD Proxy. Neste exemplo, é o próprio servidor SharePoint.
1. Faça duplo clique no computador e selecione o separador **Delegação**.
1. Certifique-se de que as opções de delegação são definidas para **confiar neste computador apenas para a delegação aos serviços especificados**. Em seguida, **selecione Utilize qualquer protocolo de autenticação**.
1. Selecione o botão **Adicionar,** selecione **Utilizadores ou Computadores** e localize a conta de grupo de aplicações SharePoint. Por exemplo: `Contoso\spapppool`.
1. Na lista de SPNs, selecione a que criou anteriormente para a conta de serviço.
1. Selecione **OK** e, em seguida, selecione **OK** novamente para guardar as suas alterações.
  
   ![Definições de delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Está agora pronto para iniciar seduca no SharePoint usando o URL externo e autenticar com o Azure.

## <a name="troubleshoot-sign-in-errors"></a>Erros de sessão de resolução de problemas

Se o login no site não estiver a funcionar, pode obter mais informações sobre o problema nos registos do Conector: A partir da máquina que executa o conector, abra o espectador do evento, vá às **Aplicações e Serviços**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**, e inspecione o registo **de Admin.**

## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com domínios personalizados em Azure AD Application Proxy](application-proxy-configure-custom-domain.md)
* [Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)