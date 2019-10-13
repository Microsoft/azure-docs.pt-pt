---
title: Habilitar o acesso remoto ao SharePoint com o Azure Proxy de Aplicativo do AD | Microsoft Docs
description: Aborda as noções básicas sobre como integrar um servidor do SharePoint local com o Azure Proxy de Aplicativo do AD.
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286077"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD

Este guia passo a passo explica como integrar um farm do SharePoint local com o proxy de aplicativo do Azure Active Directory (AD do Azure).

## <a name="prerequisites"></a>Pré-requisitos

Para executar a configuração, você precisa dos seguintes recursos:
- Um farm do SharePoint 2013 ou mais recente.
- Um locatário Azure Active Directory com um plano que inclui o proxy de aplicativo. Saiba mais sobre os [planos de Azure Active Directory e os preços](https://azure.microsoft.com/pricing/details/active-directory/).
- Um [domínio verificado personalizado](../fundamentals/add-custom-domain.md) no locatário do Azure AD.
- Active Directory no local sincronizadas com Azure AD Connect com usuários capazes de [entrar no Azure](../hybrid/plan-connect-user-signin.md).
- Um conector de proxy de aplicativo instalado e em execução em um computador dentro do domínio corporativo.

Configurar o SharePoint com o proxy de aplicativo requer duas URLs:
- Uma URL externa, visível para os usuários finais e determinada em Azure Active Directory. Essa URL pode usar um domínio personalizado. Saiba mais sobre como [trabalhar com domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).
- Uma URL interna, conhecida apenas dentro do domínio corporativo e nunca usada diretamente.

> [!IMPORTANT]
> Para garantir que os links sejam mapeados corretamente, siga estas recomendações para a URL interna:
> - Usar HTTPS
> - Não usar portas personalizadas
> - No DNS corporativo, crie um host (A) para apontar para o WFE do SharePoint (ou balanceador de carga) e não para um alias (CName)

Neste artigo, os seguintes valores serão usados:
- URL interna: `https://sharepoint`
- URL externa: `https://spsites-demo1984.msappproxy.net/`
- Conta do pool de aplicativos para o aplicativo Web do SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Etapa 1: configurar um aplicativo no Azure AD que usa o proxy de aplicativo

Nesta etapa, você cria um aplicativo em seu locatário de Azure Active Directory que usará o proxy de aplicativo. Você irá definir a URL externa e especificar a URL interna, ambas usadas mais tarde no SharePoint.

1. Crie o aplicativo conforme descrito com as configurações a seguir. Para obter as instruções passo a passo, consulte [Publicando aplicativos usando o Azure proxy de aplicativo do AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interna**: URL interna do SharePoint que será definida posteriormente no SharePoint, como `https://sharepoint`.
   * **Método de pré-autenticação**: Azure Active Directory
   * **Converter URL em cabeçalhos**: não
   * **Traduzir URL no corpo do aplicativo**: não

   ![Publicar o SharePoint como aplicativo](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Depois que seu aplicativo for publicado, configure as configurações de logon único com as seguintes etapas:

   1. Na página aplicativo no portal, selecione **logon único**.
   1. Para o **modo de logon único**, selecione **autenticação integrada do Windows**.
   1. Defina **SPN do aplicativo interno** para o valor que você definiu anteriormente. Para este exemplo, o valor seria `HTTP/sharepoint`.
   1. Em **identidade de logon delegada**, selecione a opção mais adequada para sua configuração de floresta Active Directory. Por exemplo, se você tiver um único domínio do AD em sua floresta, selecione **nome da conta Sam local** (como mostrado abaixo), mas se os usuários não estiverem no mesmo domínio que o SharePoint e os servidores do conector do proxy de aplicativo, selecione **entidade de usuário local nome** (não mostrado).

   ![Configurar a autenticação integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Para concluir a configuração do aplicativo, vá para a seção **usuários e grupos** e atribua usuários para acessar este aplicativo. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Etapa 2: configurar o aplicativo Web do SharePoint

O aplicativo Web do SharePoint deve ser configurado com Kerberos e os mapeamentos de acesso alternativo apropriados para funcionar corretamente com o Azure Proxy de Aplicativo do AD. Há duas opções possíveis:

1. Crie um novo aplicativo Web e use apenas a zona padrão. Essa é a opção preferida para ter a melhor experiência com o SharePoint (por exemplo, os links nos alertas de email gerados pelo SharePoint sempre apontam para a zona padrão).
1. Estenda um aplicativo Web existente para configurar o Kerberos em uma zona não padrão.

> [!IMPORTANT]
> Independentemente da zona que será usada, a conta do pool de aplicativos do aplicativo Web do SharePoint deve ser uma conta de domínio para que o Kerberos funcione corretamente.

### <a name="provision-the-sharepoint-web-application"></a>Provisionar o aplicativo Web do SharePoint

- Se você criar um novo aplicativo Web e usar apenas a zona padrão (opção preferencial):

    1. Inicie o **Shell de gerenciamento do SharePoint** e execute o seguinte script:

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

    1. Abra o site de **Administração Central do SharePoint** .
    1. Em **configurações do sistema**, selecione **configurar mapeamentos alternativos de acesso**. A caixa mapeamentos alternativos de acesso é aberta.
    1. Filtre a exibição com o novo aplicativo Web e confirme que você vê algo assim:

       ![Mapeamentos alternativos de acesso do aplicativo Web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se você estender um aplicativo Web existente para uma nova zona (caso não possa usar a zona padrão):

    1. Inicie o **Shell de gerenciamento do SharePoint** e execute o seguinte script:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Abra o site de **Administração Central do SharePoint** .
    1. Em **configurações do sistema**, selecione **configurar mapeamentos alternativos de acesso**. A caixa mapeamentos alternativos de acesso é aberta.
    1. Filtre a exibição com o aplicativo Web que foi estendido e confirme que você vê algo assim:

        ![Mapeamentos alternativos de acesso do aplicativo estendido](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Verifique se o aplicativo Web do SharePoint está sendo executado em uma conta de domínio

Use as etapas a seguir para identificar a conta que executa o pool de aplicativos do aplicativo Web do SharePoint e verifique se ela é uma conta de domínio:

1. Abra o site de **Administração Central do SharePoint** .
1. Vá para **segurança** e selecione **Configurar contas de serviço**.
1. Selecione **pool de aplicativos Web-YourWebApplicationName**.

   ![Opções para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Confirme se a **opção Selecionar uma conta para esse componente** retorna uma conta de domínio e lembre-se de que ela será necessária na próxima etapa.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Verifique se um certificado HTTPS está configurado para o site do IIS da zona da extranet

Como a URL interna usa o protocolo HTTPS (`https://SharePoint/`), um certificado deve ser definido no site do IIS.

1. Abra o console do Windows PowerShell.
1. Execute o script a seguir para gerar um certificado autoassinado e adicioná-lo ao meu repositório do computador:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Os certificados autoassinados são adequados apenas para fins de teste. Em ambientes de produção, é altamente recomendável usar certificados emitidos por uma autoridade de certificação em vez disso.

1. Abra o console "Serviços de Informações da Internet Manager".
1. Expanda o servidor no modo de exibição de árvore, expanda "sites", selecione o site "SharePoint – proxy do AAD" e clique em **associações**.
1. Selecione Associação HTTPS e clique em **Editar...** .
1. No campo certificado SSL, escolha certificado **do SharePoint** e clique em OK.

Agora você pode acessar o site do SharePoint externamente por meio do Azure Proxy de Aplicativo do AD.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>Etapa 3: configurar a delegação restrita de Kerberos (KCD)

Os usuários serão inicialmente autenticados no Azure Active Directory e, em seguida, no SharePoint usando o Kerberos por meio do conector de proxy do Azure AD. Para permitir que o conector obtenha um token Kerberos em nome do usuário Azure Active Directory, é necessário configurar a delegação restrita de Kerberos com a transição de protocolo. Para saber mais sobre o KCD, confira [visão geral da delegação restrita de Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Definir o nome da entidade de serviço para a conta de serviço do SharePoint

Neste artigo, a URL interna é `https://sharepoint`, portanto, o SPN é `HTTP/sharepoint`. Você precisa substituir esses valores por aqueles correspondentes ao seu ambiente.
Para registrar o SPN `HTTP/sharepoint` para a conta do pool de aplicativos do SharePoint `Contoso\spapppool`, execute o seguinte comando em um prompt de comando, como administrador do domínio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

O comando SetSPN procura o SPN antes de adicioná-lo. Se já existir, você verá um erro de **valor de SPN duplicado** . Nesse caso, considere remover o SPN existente se ele não estiver definido na conta correta do pool de aplicativos.  
Você pode verificar se o SPN foi adicionado com êxito executando o comando setspn com a opção-L. Para saber mais sobre esse comando, consulte [setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Verifique se o conector é confiável para delegação para o SPN adicionado à conta do pool de aplicativos do SharePoint

Configure o KCD para que o serviço de Proxy de Aplicativo do AD do Azure possa delegar identidades de usuário para a conta do pool de aplicativos do SharePoint. Configure o KCD habilitando o conector de proxy de aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto para o aplicativo de destino ou o SharePoint nesse caso.

Para configurar o KCD, repita as seguintes etapas para cada computador do conector:

1. Entre em um controlador de domínio como um administrador de domínio e, em seguida, abra **Active Directory usuários e computadores**.
1. Localize o computador que está executando o conector de proxy do Azure AD. Neste exemplo, ele é o próprio servidor do SharePoint.
1. Clique duas vezes no computador e clique na guia **delegação** .
1. Verifique se as configurações de delegação estão definidas para **confiar neste computador para delegação somente para os serviços especificados**. Em seguida, selecione **usar qualquer protocolo de autenticação**.
1. Clique no botão **Adicionar** , clique em **usuários ou computadores**e localize a conta do pool de aplicativos do SharePoint, por exemplo `Contoso\spapppool`.
1. Na lista de SPNs, selecione aquele que você criou anteriormente para a conta de serviço.
1. Clique em **OK**. Clique em **OK** novamente para salvar as alterações.
  
   ![Configurações de delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Agora você está pronto para entrar no SharePoint usando a URL externa e autenticar com o Azure.

## <a name="troubleshoot-sign-in-errors"></a>Solucionar erros de entrada

Se a entrada no site não estiver funcionando, você poderá obter mais informações sobre o problema nos logs do conector: no computador que executa o conector, abra o Visualizador de eventos, acesse **logs de aplicativos e serviços** > **Microsoft** >  **AadApplicationProxy**o**conector** >  e inspeciona o log do **administrador** .

## <a name="next-steps"></a>Passos seguintes

* [Trabalhando com domínios personalizados no Azure Proxy de Aplicativo do AD](application-proxy-configure-custom-domain.md)
* [Entender os conectores de Proxy de Aplicativo do AD do Azure](application-proxy-connectors.md)
