---
title: Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre como integrar um servidor do SharePoint no local com o Proxy de aplicações do Azure AD.
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
ms.date: 08/28/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1265341ecfdb7f418ea89bb0ec848a20c6b430cd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127674"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD

Este artigo aborda como integrar um servidor do SharePoint no local com o Proxy de aplicações do Azure Active Directory (Azure AD).

Para ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD, siga as secções neste artigo passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tenha SharePoint 2013 ou mais recente no seu ambiente. Além disso, considere os seguintes pré-requisitos:

* O SharePoint inclui suporte nativo do Kerberos. Por conseguinte, os utilizadores que estão a aceder a sites internos remotamente através do Proxy de aplicações do Azure AD podem assumir que tenham uma experiência de início de sessão único (SSO).
* Este cenário inclui alterações de configuração ao seu servidor do SharePoint. Recomendamos que utilize um ambiente de teste. Dessa forma, pode fazer atualizações ao seu servidor de teste pela primeira vez e, em seguida, facilitam um ciclo de testes antes de entrar em produção.
* É necessário SSL no URL publicado. O SSL também é necessário na URL interna para garantir que os links sejam enviados/mapeados corretamente.

> [!NOTE]
> Como prática recomendada, use domínios personalizados sempre que possível. Com um domínio personalizado, você pode configurar a mesma URL para a URL interna e a URL externa. Em seguida, o mesmo link pode ser usado para acessar o aplicativo de dentro ou de fora da sua rede. Essa configuração otimiza a experiência para usuários e outros aplicativos que precisam acessar seu aplicativo. Saiba mais sobre como [trabalhar com domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passo 1: Configurar a delegação restrita de Kerberos (KCD)

Para aplicações no local que utilizam a autenticação do Windows, pode obter início de sessão único (SSO) com o protocolo de autenticação Kerberos e um recurso chamado delegação restringida de Kerberos (KCD). KCD, quando configurado, permite que o conector de Proxy de aplicações obter um token do Windows para um utilizador, mesmo que o utilizador não tiver sessão iniciada Windows diretamente. Para saber mais sobre o KCD, veja [descrição geral de delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Para configurar o KCD para um servidor do SharePoint, utilize os procedimentos nas seguintes secções seqüenciais:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Verifique se o aplicativo Web do SharePoint está sendo executado em uma conta de domínio

Primeiro, verifique se o aplicativo Web do SharePoint está em execução em uma conta de domínio – não sistema local, serviço local ou serviço de rede. Faça isso para que você possa anexar SPNs (nomes da entidade de serviço) a essa conta. SPNs são a forma como o protocolo Kerberos identifica os diferentes serviços. E terá da conta mais tarde para configurar o KCD.

> [!NOTE]
> Tem de ter um criado anteriormente conta do Azure AD para o serviço. Sugerimos que permite que uma alteração de palavra-passe automática. Para obter mais informações sobre o conjunto completo de etapas e problemas de solução de problemas, consulte [Configurar a alteração automática de senha no SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Para garantir que seus sites estão em execução sob uma conta de serviço definido, execute os seguintes passos:

1. Abra o site de **Administração Central do SharePoint** .
1. Aceda a **Security** e selecione **configurar contas de serviço**.
1. Selecione **Web Pool de aplicativos - SharePoint – 80**. As opções podem ser ligeiramente diferentes com base no nome do seu conjunto de web, ou se o conjunto de web utiliza SSL por padrão.

   ![Opções para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Se **Selecione uma conta para este componente** campo é definido como **Serviço Local** ou **serviço de rede**, tem de criar uma conta. Se não tiver terminado e pode avançar para a secção seguinte.
1. Selecione **Registre-se conta gerida novo**. Depois de criar a sua conta, tem de definir **Pool de aplicativos Web** antes de poder utilizar a conta.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Defina um nome de principal de serviço para a conta de serviço do SharePoint

Antes de configurar o KCD, você precisa:

* Identifique a conta de domínio que executa o aplicativo Web do SharePoint que o proxy do Azure AD vai expor.
* Escolha uma URL interna que será configurada no proxy do Azure AD e no SharePoint. Essa URL interna já não deve ser usada no aplicativo Web e nunca aparecerá no navegador da Web.

Supondo que a URL interna <https://sharepoint>escolhida seja, o SPN é:

```
HTTP/SharePoint
```

> [!NOTE]
> Siga estas recomendações para a URL interna:
> * Usar HTTPS
> * Não usar portas personalizadas
> * No DNS, crie um host (A) para apontar para o SharePoint WFE (ou balanceador de carga) e não um alias (CName)

Para registrar esse SPN, execute o seguinte comando no prompt de comando como um administrador do domínio:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Esse comando define o SPN _http/SharePoint_ para a conta do pool de aplicativos do SharePoint _demo\spAppPoolAccount_.

Substitua _http/SharePoint_ pelo SPN para sua URL interna e _demo\spAppPoolAccount_ com a conta do pool de aplicativos em seu ambiente. O comando Setspn procura o SPN antes de adicioná-lo. Nela já existir, você verá um erro de **valor de SPN duplicado** . Nesse caso, considere remover o SPN existente se ele não estiver definido na conta correta do pool de aplicativos.

Você pode verificar se o SPN foi adicionado executando o comando setspn com a opção-L. Para saber mais sobre este comando, consulte [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Verifique se o conector é confiável para delegação para o SPN adicionado à conta do pool de aplicativos do SharePoint

Configure o KCD para que o serviço de Proxy de Aplicativo do AD do Azure possa delegar identidades de usuário para a conta do pool de aplicativos do SharePoint. Configure o KCD ao ativar o conector do Proxy de aplicações para obter permissões de Kerberos para seus usuários que tiverem sido autenticados no Azure AD. Em seguida, esse servidor passa o contexto para a aplicação de destino ou o SharePoint neste caso.

Para configurar o KCD, repita os passos seguintes para cada computador do conector:

1. Iniciar sessão como um administrador de domínio para um controlador de domínio e, em seguida, abra **Active Directory Users and Computers**.
1. Encontre o que o conector está em execução no computador. Neste exemplo, é o mesmo servidor do SharePoint.
1. Clique duas vezes o computador e, em seguida, clique nas **delegação** separador.
1. Certifique-se de que as definições de delegação estiverem definidas como **confiar no computador para delegação apenas as serviços especificados**. Em seguida, selecione **utilizar qualquer protocolo de autenticação**.
1. Clique no botão **Adicionar** , clique em **usuários ou computadores**e localize a conta do pool de aplicativos do SharePoint, por exemplo, _demo\spAppPoolAccount_.
1. Na lista de SPNs, selecione aquela que criou anteriormente para a conta de serviço.
1. Clique em **OK**. Clique em **OK** novamente para guardar as alterações.
  
   ![Definições de delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Passo 2: Configurar o proxy do Azure AD

Agora que você configurou o KCD, você está pronto para configurar o Azure Proxy de Aplicativo do AD.

1. Publica o seu site do SharePoint com as seguintes definições. Para obter instruções passo a passo, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interna**: URL interna do SharePoint que foi escolhida anteriormente, **<https://SharePoint/>** como.
   * **Método de pré-autenticação**: Azure Active Directory
   * **Traduzir URL nos cabeçalhos**: NO

   > [!TIP]
   > O SharePoint usa a _cabeçalho de anfitrião_ valor para procurar o site. Também gera links com base nesse valor. O efeito líquido é que qualquer ligação que gera o SharePoint é um URL publicado que está corretamente definido para utilizar o URL externo. Definindo o valor como **Sim** também permite que o conector encaminhar a solicitação para a aplicação de back-end. No entanto, definindo o valor como **não** significa que o conector não enviará o nome de anfitrião interno. Em vez disso, o conector envia o cabeçalho de anfitrião como o URL publicado para a aplicação de back-end.

   ![Publicar o SharePoint como aplicação](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Assim que a sua aplicação for publicada, configure as definições de início de sessão únicas com os seguintes passos:

   1. Na página da aplicação no portal, selecione **início de sessão único**.
   1. Para o início de sessão em modo único, selecione **a autenticação integrada do Windows**.
   1. Definir o SPN da aplicação interna para o valor que definiu anteriormente. Para este exemplo, isso seria **http/SharePoint**.
   1. Em "identidade de logon delegada", selecione a opção mais adequada para sua configuração de floresta Active Directory. Por exemplo, se você tiver um único domínio do AD em sua floresta, selecione **nome da conta Sam local** (como mostrado abaixo), mas se os usuários não estiverem no mesmo domínio que o SharePoint e os servidores do conector do proxy de aplicativo, selecione **nome principal do usuário local** (não mostrado).

   ![Configurar a autenticação integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Para concluir a configuração a sua aplicação, vá para o **utilizadores e grupos** secção e atribuir utilizadores a aceder a esta aplicação. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Passo 3: Configurar o SharePoint para usar o Kerberos e as URLs de proxy do Azure AD

A próxima etapa é estender o aplicativo Web do SharePoint para uma nova zona, configurada com Kerberos e o mapeamento de acesso alternativo apropriado para permitir que o SharePoint manipule solicitações de entrada enviadas para a URL interna e responda com links criados para a URL externa.

1. Inicie o **Shell de gerenciamento do SharePoint**.
1. Execute o script a seguir para estender o aplicativo Web para a zona de extranet e habilitar a autenticação Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Abra o site de **Administração Central do SharePoint** .
1. Sob **definições do sistema**, selecione **configurar mapeamentos de acesso alternativo**. Abre a caixa de mapeamentos de acesso alternativo.
1. Selecione seu site, por exemplo, **SharePoint-80**. Por enquanto, a zona de extranet ainda não tem a URL interna definida corretamente:

   ![Mostra a caixa mapeamentos alternativos de acesso](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Clique em **Adicionar URLs internas**.
1. Na caixa de texto **protocolo de URL, host e porta** , digite a **URL interna** configurada no proxy <https://SharePoint/>do Azure AD, por exemplo.
1. Selecione **extranet** de zona na lista suspensa.
1. Clique em **Guardar**.
1. Os mapeamentos de acesso alternativo agora devem ser assim:

    ![Corrigir mapeamentos alternativos de acesso](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Passo 4: Verifique se um certificado HTTPS está configurado para o site do IIS da zona da extranet

A configuração do SharePoint agora está concluída, mas como a URL interna da zona de <https://SharePoint/>extranet é, um certificado deve ser definido para esse site.

1. Abra o console do Windows PowerShell.
1. Execute o script a seguir para gerar um certificado autoassinado e adicioná-lo ao meu repositório do computador:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Os certificados autoassinados são adequados apenas para fins de teste. Em ambientes de produção, é altamente recomendável usar certificados emitidos por uma autoridade de certificação em vez disso.

1. Abra o console "Serviços de Informações da Internet Manager".
1. Expanda o servidor no modo de exibição de árvore, expanda "sites", selecione o site "SharePoint – proxy do AAD" e clique em **associações**.
1. Selecione Associação HTTPS e clique em **Editar...** .
1. No campo certificado SSL, escolha certificado **do SharePoint** e clique em OK.

Agora pode acessar o site do SharePoint externamente através do Proxy de aplicações do Azure AD.

## <a name="next-steps"></a>Passos Seguintes

* [Trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md)
* [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)
