---
title: Implantar a proteção de senha do Azure AD-Azure Active Directory
description: Implantar a proteção de senha do Azure AD para proibir senhas inadequadas no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2328bcd2b2d9fe957df82c46730091ffdf9366
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474296"
---
# <a name="deploy-azure-ad-password-protection"></a>Implementar proteção de palavras-passe do Azure AD

Agora que você entende [como impor a proteção de senha do Azure ad para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md), a próxima etapa é planejar e executar a implantação.

## <a name="deployment-strategy"></a>Estratégia de implantação

Recomendamos que você inicie as implantações no modo de auditoria. O modo de auditoria é a configuração inicial padrão, em que as senhas podem continuar a ser definidas. As senhas que seriam bloqueadas são registradas no log de eventos. Depois de implantar os servidores proxy e os agentes de DC no modo de auditoria, você deve monitorar o impacto que a política de senha terá nos usuários e no ambiente quando a política for imposta.

Durante o estágio de auditoria, muitas organizações acham que:

* Eles precisam melhorar os processos operacionais existentes para usar senhas mais seguras.
* Os usuários geralmente usam senhas não seguras.
* Eles precisam informar os usuários sobre a próxima alteração na imposição de segurança, o possível impacto sobre eles e como escolher senhas mais seguras.

Também é possível que uma validação de senha mais forte afete sua automação de implantação do controlador de domínio Active Directory existente. É recomendável que pelo menos uma promoção de DC e um rebaixamento de DC ocorram durante a avaliação do período de auditoria, a fim de ajudar a descobrir esses problemas com antecedência.  Para obter mais informações, consulte:

* [O Ntdsutil. exe não pode definir uma senha fraca do modo de reparo de serviços de diretório](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma senha fraca do modo de reparo do serviços de diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [O rebaixamento do controlador de domínio falha devido a uma senha de administrador local fraca](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois que o recurso estiver sendo executado no modo de auditoria por um período razoável, você poderá alternar a configuração de *auditoria* para *impor* para exigir senhas mais seguras. O monitoramento focado durante esse tempo é uma boa ideia.

## <a name="deployment-requirements"></a>Requisitos de implantação

* Os requisitos de licenciamento para proteção de senha do Azure AD podem ser encontrados no artigo [eliminar senhas inadequadas em sua organização](concept-password-ban-bad.md#license-requirements).
* Todos os computadores em que o software do agente DC da proteção de senha do Azure AD serão instalados devem executar o Windows Server 2012 ou posterior. Esse requisito não significa que o domínio ou floresta de Active Directory também deve estar no nível funcional de domínio ou floresta do Windows Server 2012. Conforme mencionado nos [princípios de design](concept-password-ban-bad-on-premises.md#design-principles), não há nenhum DFL ou FFL mínimo necessário para a execução do agente DC ou do software proxy.
* Todos os computadores que obtêm o serviço de agente de DC instalado devem ter o .NET 4,5 instalado.
* Todos os computadores nos quais o serviço proxy de proteção de senha do Azure AD será instalado devem executar o Windows Server 2012 R2 ou posterior.
   > [!NOTE]
   > A implantação do serviço de proxy é um requisito obrigatório para implantar a proteção de senha do Azure AD, embora o controlador de domínio possa ter conectividade de Internet direta de saída. 
   >
* Todas as máquinas em que o serviço proxy de proteção de senha do Azure AD será instalado devem ter o .NET 4,7 instalado.
  O .NET 4,7 já deve estar instalado em um Windows Server totalmente atualizado. Se necessário, baixe e execute o instalador encontrado no [instalador offline do .NET Framework 4,7 para Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todas as máquinas, incluindo controladores de domínio, que têm os componentes de proteção de senha do Azure AD instalados, devem ter o tempo de execução universal C instalado. Você pode obter o tempo de execução, certificando-se de que você tenha todas as atualizações de Windows Update. Ou você pode obtê-lo em um pacote de atualização específico do sistema operacional. Para obter mais informações, consulte [atualizar para tempo de execução C universal no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* A conectividade de rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospede o serviço de proxy para proteção por senha. Essa conectividade deve permitir que o controlador de domínio acesse a porta 135 do mapeador de ponto de extremidade RPC e a porta do servidor RPC no serviço de proxy. Por padrão, a porta do servidor RPC é uma porta RPC dinâmica, mas pode ser configurada para [usar uma porta estática](#static).
* Todas as máquinas em que o serviço proxy de proteção de senha do Azure AD será instalado devem ter acesso à rede para os seguintes pontos de extremidade:

    |**Extremidade**|**Objetivo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Solicitações de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de proteção de senha do Azure AD|
 
* Pré-requisitos do atualizador do agente do Microsoft Azure AD Connect

  O serviço atualizador do agente do Microsoft Azure AD Connect é instalado lado a lado com o serviço de proxy de proteção de senha do Azure AD. A configuração adicional é necessária para que o serviço de atualizador do agente do Microsoft Azure AD Connect seja capaz de funcionar:

  Se o seu ambiente usa um servidor proxy http, você deve seguir as diretrizes especificadas em [trabalhar com servidores proxy locais existentes](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

  O serviço de atualizador do agente do Microsoft Azure AD Connect também requer as etapas do TLS 1,2 especificadas nos [requisitos de TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

  O acesso à rede deve ser habilitado para o conjunto de portas e URLs especificadas nos [procedimentos de configuração do ambiente de proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

  > [!WARNING]
  > Proxy de proteção por senha do Azure AD e proxy de aplicativo instalam versões diferentes do serviço de atualizador do agente Microsoft Azure AD Connect, que é o motivo pelo qual as instruções se referem ao conteúdo do proxy de aplicativo. Essas versões diferentes são incompatíveis quando instaladas lado a lado, portanto, não é recomendável instalar o proxy de proteção de senha do Azure AD e o proxy de aplicativo lado a lado no mesmo computador.

* Todos os computadores que hospedam o serviço de proxy para proteção por senha devem ser configurados para conceder aos controladores de domínio a capacidade de fazer logon no serviço de proxy. Essa capacidade é controlada por meio da atribuição de privilégio "acessar este computador pela rede".
* Todos os computadores que hospedam o serviço de proxy para proteção por senha devem ser configurados para permitir o tráfego HTTP 1,2 de saída do TLS.
* Uma conta de administrador global para registrar o serviço de proxy para a proteção e a floresta de senha com o Azure AD.
* Uma conta que tem Active Directory privilégios de administrador de domínio no domínio raiz da floresta para registrar a floresta do Windows Server Active Directory com o Azure AD.
* Qualquer domínio Active Directory que execute o software do serviço de agente de controlador de domínio deve usar a replicação de Sistema de Arquivos Distribuído (DFSR) para replicação do SYSVOL.

  Se o seu domínio ainda não estiver usando o DFSR, você deverá migrá-lo para usar o DFSR antes de instalar a proteção de senha do Azure AD. Para obter mais informações, consulte o seguinte link:

  [Guia de migração de replicação do SYSVOL: FRS para Replicação do DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > O software de agente DC de proteção de senha do Azure AD será instalado no momento em controladores de domínio em domínios que ainda estão usando o FRS (a tecnologia predecessora para DFSR) para replicação de SYSVOL, mas o software não funcionará corretamente nesse ambiente. Efeitos colaterais negativos adicionais incluem arquivos individuais que falham na replicação e os procedimentos de restauração de SYSVOL que aparecem com êxito, mas falham silenciosamente na replicação de todos os arquivos. Você deve migrar seu domínio para usar o DFSR assim que possível, tanto para os benefícios inerentes do DFSR quanto para desbloquear a implantação da proteção de senha do Azure AD. Versões futuras do software serão desabilitadas automaticamente quando executadas em um domínio que ainda esteja usando o FRS.

* O serviço de distribuição de chaves deve ser habilitado em todos os controladores de domínio no domínio que executam o Windows Server 2012. Por padrão, esse serviço é habilitado por meio do início do gatilho manual.

## <a name="single-forest-deployment"></a>Implantação de floresta única

O diagrama a seguir mostra como os componentes básicos da proteção de senha do Azure AD funcionam juntos em um ambiente de Active Directory local.

![Como os componentes de proteção de senha do Azure AD funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia examinar como o software funciona antes de implantá-lo. Consulte [visão geral conceitual da proteção de senha do Azure ad](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Transferir o software

Há dois instaladores necessários para a proteção de senha do Azure AD. Eles estão disponíveis no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalar e configurar o serviço de proxy para proteção por senha

1. Escolha um ou mais servidores para hospedar o serviço de proxy para proteção por senha.
   * Cada serviço desse tipo só pode fornecer políticas de senha para uma única floresta. O computador host deve ser Unido a um domínio nessa floresta. Os domínios raiz e filho têm suporte. Você precisa de conectividade de rede entre pelo menos um DC em cada domínio da floresta e o computador de proteção de senha.
   * Você pode executar o serviço de proxy em um controlador de domínio para teste. Mas esse controlador de domínio requer conectividade com a Internet, o que pode ser uma preocupação de segurança. Recomendamos essa configuração apenas para teste.
   * Recomendamos pelo menos dois servidores proxy para redundância. Consulte [alta disponibilidade](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Instale o serviço de proxy de proteção de senha do Azure AD usando o instalador de software `AzureADPasswordProtectionProxySetup.exe`.
   * A instalação do software não requer uma reinicialização. A instalação do software pode ser automatizada usando procedimentos MSI padrão, por exemplo:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > O serviço de firewall do Windows deve estar em execução antes de instalar o pacote AzureADPasswordProtectionProxySetup. msi para evitar um erro de instalação. Se o Firewall do Windows estiver configurado para não ser executado, a solução alternativa será habilitar e executar temporariamente o serviço de firewall durante a instalação. O software proxy não tem dependência específica no firewall do Windows após a instalação. Se você estiver usando um firewall de terceiros, ele ainda deverá ser configurado para atender aos requisitos de implantação. Isso inclui permitir o acesso de entrada à porta 135 e à porta do servidor RPC de proxy. Consulte [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Abra uma janela do PowerShell como administrador.
   * O software proxy de proteção por senha inclui um novo módulo do PowerShell, *AzureADPasswordProtection*. As etapas a seguir executam vários cmdlets deste módulo do PowerShell. Importe o novo módulo da seguinte maneira:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Para verificar se o serviço está em execução, use o seguinte comando do PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     O resultado deve mostrar o **status** "Running".

1. Registre o proxy.
   * Após a conclusão da etapa 3, o serviço de proxy está em execução no computador. Mas o serviço ainda não tem as credenciais necessárias para se comunicar com o Azure AD. O registro com o Azure AD é necessário:

     `Register-AzureADPasswordProtectionProxy`

     Este cmdlet requer credenciais de administrador global para seu locatário do Azure. Você também precisa do local Active Directory privilégios de administrador de domínio no domínio raiz da floresta. Depois que esse comando for executado uma vez para um serviço de proxy, as invocações adicionais serão realizadas com sucesso, mas são desnecessárias.

      O cmdlet `Register-AzureADPasswordProtectionProxy` dá suporte aos três modos de autenticação a seguir.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Esse modo não funciona em sistemas operacionais Server Core. Em vez disso, use um dos modos de autenticação a seguir. Além disso, esse modo poderá falhar se a configuração de segurança reforçada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa configuração, registrar o proxy e, em seguida, habilitá-lo novamente.

     * Modo de autenticação de código do dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Em seguida, conclua a autenticação seguindo as instruções exibidas em um dispositivo diferente.

     * Modo de autenticação silencioso (baseado em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a autenticação multifator do Azure for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou, em vez disso, use uma conta diferente que não exija MFA.
        >
        > Você também poderá ver a MFA necessária se o registro de dispositivo do Azure (que é usado na capa pela proteção de senha do Azure AD) tiver sido configurado para exigir globalmente a MFA. Para solucionar esse problema, você pode usar uma conta diferente que dá suporte a MFA com um dos dois modos de autenticação anteriores, ou pode também relaxar temporariamente o requisito de MFA de registro de dispositivo do Azure. Para fazer isso, vá para o portal de gerenciamento do Azure, vá para Azure Active Directory, depois dispositivos, configurações do dispositivo e, em seguida, defina "exigir autenticação multifator para unir dispositivos" como não. Certifique-se de reconfigurar essa configuração de volta para Sim quando o registro for concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

       No momento, você não precisa especificar o parâmetro *-ForestCredential* , que é reservado para funcionalidade futura.

   O registro do serviço de proxy para proteção por senha é necessário apenas uma vez no tempo de vida do serviço. Depois disso, o serviço de proxy executará automaticamente qualquer outra manutenção necessária.

   > [!TIP]
   > Pode haver um atraso perceptível antes da conclusão na primeira vez em que esse cmdlet for executado para um locatário específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

1. Registre a floresta.
   * Você deve inicializar a floresta Active Directory local com as credenciais necessárias para se comunicar com o Azure usando o cmdlet `Register-AzureADPasswordProtectionForest` PowerShell. O cmdlet requer credenciais de administrador global para seu locatário do Azure. Ele também requer privilégios de administrador corporativo Active Directory local. Esta etapa é executada uma vez por floresta.

      O cmdlet `Register-AzureADPasswordProtectionForest` dá suporte aos três modos de autenticação a seguir.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Esse modo não funcionará em sistemas operacionais Server Core. Em vez disso, use um dos dois modos de autenticação a seguir. Além disso, esse modo poderá falhar se a configuração de segurança reforçada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa configuração, registrar a floresta e, em seguida, habilitá-la novamente.  

     * Modo de autenticação de código do dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Em seguida, conclua a autenticação seguindo as instruções exibidas em um dispositivo diferente.

     * Modo de autenticação silencioso (baseado em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a autenticação multifator do Azure for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou, em vez disso, use uma conta diferente que não exija MFA.
        >
        > Você também poderá ver a MFA necessária se o registro de dispositivo do Azure (que é usado na capa pela proteção de senha do Azure AD) tiver sido configurado para exigir globalmente a MFA. Para solucionar esse problema, você pode usar uma conta diferente que dá suporte a MFA com um dos dois modos de autenticação anteriores, ou pode também relaxar temporariamente o requisito de MFA de registro de dispositivo do Azure. Para fazer isso, vá para o portal de gerenciamento do Azure, vá para Azure Active Directory, depois dispositivos, configurações do dispositivo e, em seguida, defina "exigir autenticação multifator para unir dispositivos" como não. Certifique-se de reconfigurar essa configuração de volta para Sim quando o registro for concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

       Esses exemplos só serão bem-sucedidos se o usuário conectado no momento for também um administrador de domínio Active Directory para o domínio raiz. Se esse não for o caso, você poderá fornecer credenciais de domínio alternativas por meio do parâmetro *-ForestCredential* .

   > [!NOTE]
   > Se vários servidores proxy estiverem instalados em seu ambiente, não importa qual servidor proxy você usa para registrar a floresta.
   >
   > [!TIP]
   > Pode haver um atraso perceptível antes da conclusão na primeira vez em que esse cmdlet for executado para um locatário específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

   O registro da floresta Active Directory é necessário apenas uma vez no tempo de vida da floresta. Depois disso, os agentes do controlador de domínio na floresta executarão automaticamente qualquer outra manutenção necessária. Depois que `Register-AzureADPasswordProtectionForest` é executado com êxito para uma floresta, as invocações adicionais do cmdlet são bem-sucedidas, mas são desnecessárias.

   Para que `Register-AzureADPasswordProtectionForest` tenha sucesso, pelo menos um controlador de domínio que executa o Windows Server 2012 ou posterior deve estar disponível no domínio do servidor proxy. O software do agente de DC não precisa ser instalado em nenhum controlador de domínio antes desta etapa.

1. Configure o serviço de proxy para proteção por senha para se comunicar por meio de um proxy HTTP.

   Se o seu ambiente exigir o uso de um proxy HTTP específico para se comunicar com o Azure, use este método: Crie um arquivo *AzureADPasswordProtectionProxy. exe. config* na pasta%ProgramFiles%\Azure Proxy\Service de proteção de senha do AD. Inclua o seguinte conteúdo:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Se o proxy HTTP exigir autenticação, adicione a marca *UseDefaultCredentials* :

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Em ambos os casos, substitua `http://yourhttpproxy.com:8080` pelo endereço e porta do seu servidor proxy HTTP específico.

   Se o proxy HTTP estiver configurado para usar uma política de autorização, você deverá conceder acesso à conta de computador Active Directory da máquina que hospeda o serviço de proxy para proteção por senha.

   Recomendamos que você pare e reinicie o serviço de proxy depois de criar ou atualizar o arquivo *AzureADPasswordProtectionProxy. exe. config* .

   O serviço de proxy não dá suporte ao uso de credenciais específicas para se conectar a um proxy HTTP.

1. Opcional: Configure o serviço de proxy para proteção por senha para escutar em uma porta específica.
   * O software do agente de DC para proteção por senha nos controladores de domínio usa RPC sobre TCP para se comunicar com o serviço de proxy. Por padrão, o serviço de proxy escuta em qualquer ponto de extremidade RPC dinâmico disponível. Mas você pode configurar o serviço para escutar em uma porta TCP específica, se isso for necessário devido à topologia de rede ou aos requisitos de firewall em seu ambiente.
      * <a id="static" /></a>configurar o serviço para ser executado em uma porta estática, use o cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Você deve parar e reiniciar o serviço para que essas alterações entrem em vigor.

      * Para configurar o serviço para ser executado em uma porta dinâmica, use o mesmo procedimento, mas defina *StaticPort* de volta para zero:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Você deve parar e reiniciar o serviço para que essas alterações entrem em vigor.

   > [!NOTE]
   > O serviço de proxy para proteção por senha requer uma reinicialização manual após qualquer alteração na configuração de porta. Mas você não precisa reiniciar o software do serviço de agente de DC em controladores de domínio depois de fazer essas alterações de configuração.

   * Para consultar a configuração atual do serviço, use o cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instalar o serviço de agente de controlador de domínio

   Instale o serviço de agente de controlador de domínio para proteção por senha usando o pacote `AzureADPasswordProtectionDCAgentSetup.msi`.

   A instalação do software, ou desinstalação, requer uma reinicialização. Esse requisito é porque as DLLs de filtro de senha são carregadas ou descarregadas apenas por uma reinicialização.

   Você pode instalar o serviço de agente de DC em um computador que ainda não é um controlador de domínio. Nesse caso, o serviço será iniciado e executado, mas permanecerá inativo até que o computador seja promovido a controlador de domínio.

   Você pode automatizar a instalação do software usando procedimentos MSI padrão. Por exemplo:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Você pode omitir o sinalizador `/norestart` se preferir que o instalador reinicialize o computador automaticamente.

A instalação é concluída depois que o software do agente DC é instalado em um controlador de domínio e esse computador é reinicializado. Nenhuma outra configuração é necessária ou possível.

## <a name="upgrading-the-proxy-agent"></a>Atualizando o agente de proxy

Quando uma versão mais recente do software de proxy de proteção de senha do Azure AD estiver disponível, a atualização será realizada executando a versão mais recente do instalador de software `AzureADPasswordProtectionProxySetup.exe`. A versão mais recente do software está disponível no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software proxy-o instalador executará uma atualização in-loco. Nenhuma reinicialização deve ser necessária ao atualizar o software proxy. A atualização de software pode ser automatizada usando procedimentos MSI padrão, por exemplo: `AzureADPasswordProtectionProxySetup.exe /quiet`.

O agente de proxy dá suporte à atualização automática. A atualização automática usa o serviço de atualizador do agente Microsoft Azure AD Connect, que é instalado lado a lado com o serviço de proxy. A atualização automática está ativada por padrão e pode ser habilitada ou desabilitada usando o cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`. A configuração atual pode ser consultada usando o cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`. A Microsoft recomenda que a configuração de atualização automática sempre esteja habilitada.

O cmdlet `Get-AzureADPasswordProtectionProxy` pode ser usado para consultar a versão do software de todos os agentes de proxy atualmente instalados em uma floresta.

## <a name="upgrading-the-dc-agent"></a>Atualizando o agente de DC

Quando uma versão mais recente do software de agente de DC de proteção de senha do Azure AD estiver disponível, a atualização será realizada executando a versão mais recente do pacote de software `AzureADPasswordProtectionDCAgentSetup.msi`. A versão mais recente do software está disponível no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente de DC-o instalador executará uma atualização in-loco. Uma reinicialização é sempre necessária ao atualizar o software do agente de DC-esse requisito é causado pelo comportamento principal do Windows. 

A atualização de software pode ser automatizada usando procedimentos MSI padrão, por exemplo: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Você pode omitir o sinalizador `/norestart` se preferir que o instalador reinicialize o computador automaticamente.

O cmdlet `Get-AzureADPasswordProtectionDCAgent` pode ser usado para consultar a versão do software de todos os agentes de DC atualmente instalados em uma floresta.

## <a name="multiple-forest-deployments"></a>Várias implantações de floresta

Não há requisitos adicionais para implantar a proteção de senha do Azure AD em várias florestas. Cada floresta é configurada independentemente conforme descrito na seção "implantação de floresta única". Cada proxy de proteção por senha só pode dar suporte a controladores de domínio da floresta à qual ele está associado. O software de proteção por senha em qualquer floresta não está ciente do software de proteção por senha implantado em outras florestas, independentemente de Active Directory configurações de confiança.

## <a name="read-only-domain-controllers"></a>Controladores de domínio somente leitura

As alterações/conjuntos de senhas não são processados e persistidos em controladores de domínio somente leitura (RODCs). Eles são encaminhados para controladores de domínio graváveis. Portanto, você não precisa instalar o software do agente de DC em RODCs.

## <a name="high-availability"></a>Elevada disponibilidade

A principal preocupação de disponibilidade para proteção por senha é a disponibilidade de servidores proxy quando os controladores de domínio em uma floresta tentam baixar novas políticas ou outros dados do Azure. Cada agente de DC usa um algoritmo simples de estilo Round Robin ao decidir qual servidor proxy deve ser chamado. O agente ignora os servidores proxy que não estão respondendo. Para implantações de Active Directory totalmente conectadas que têm replicação íntegra de diretório e estado de pasta SYSVOL, dois servidores proxy são suficientes para garantir a disponibilidade. Isso resulta no download oportuno de novas políticas e outros dados. Mas você pode implantar servidores proxy adicionais.

O design do software do agente de DC atenua os problemas comuns associados à alta disponibilidade. O agente de DC mantém um cache local da política de senha baixada mais recentemente. Mesmo se todos os servidores proxy registrados ficarem indisponíveis, os agentes de DC continuarão impõem a política de senha armazenada em cache. Uma frequência de atualização razoável para diretivas de senha em uma implantação grande geralmente é dias, não horas ou menos. Portanto, poucas interrupções dos servidores proxy não afetam significativamente a proteção de senha do Azure AD.

## <a name="next-steps"></a>Passos seguintes

Agora que você instalou os serviços necessários para a proteção de senha do Azure AD nos servidores locais, [Execute a configuração pós-instalação e colete as informações de relatório](howto-password-ban-bad-on-premises-operations.md) para concluir a implantação.

[Visão geral conceitual da proteção de senha do Azure AD](concept-password-ban-bad-on-premises.md)
