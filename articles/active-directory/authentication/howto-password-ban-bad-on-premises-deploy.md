---
title: Implementar a proteção de palavra-passe do Azure AD - Azure Active Directory
description: Implementar a proteção de palavra-passe do Azure AD proibir o uso de palavras-passe incorretas no local
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
ms.openlocfilehash: 8502ab3257bc1d121e0440ba765dfd19a6722cec
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311973"
---
# <a name="deploy-azure-ad-password-protection"></a>Implementar proteção de palavras-passe do Azure AD

Agora que compreende [como impor proteção de palavra-passe do Azure AD para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md), a próxima etapa é planejar e executar a sua implementação.

## <a name="deployment-strategy"></a>Estratégia de implantação

Recomendamos que comece implementações no modo de auditoria. Modo de auditoria é a configuração inicial do padrão, onde as palavras-passe podem continuar a ser definido. Palavras-passe que seriam bloqueadas são registadas no registo de eventos. Depois de implementar os servidores de proxy e os agentes do DC no modo de auditoria, deve monitorar o impacto que a política de palavra-passe terá sobre o ambiente e os utilizadores quando a política é imposta.

Durante a fase de auditoria, muitas organizações consideram que:

* Eles precisam para melhorar os processos operacionais existentes para utilizar palavras-passe mais seguras.
* Os utilizadores utilizam, frequentemente, as palavras-passe não seguros.
* Terá de informar os utilizadores sobre a alteração futura à imposição de segurança e como escolher as palavras-passe mais seguras possível impacto nos mesmos.

Após a funcionalidade de execução no modo de auditoria durante um período razoável, pode mudar a configuração a partir *auditar* ao *impor* solicitar palavras-passe mais seguras. O monitoramento concentrado durante este período é uma boa idéia.

## <a name="deployment-requirements"></a>Requisitos de implementação

* Todos os controladores de domínio que obtenham o agente de controlador de domínio de serviço para proteção de palavra-passe do Azure AD instalada deve executar o Windows Server 2012 ou posterior.
* Todas as máquinas que obtenham o proxy de serviço para proteção de palavra-passe do Azure AD instalada deve executar o Windows Server 2012 R2 ou posterior.
* Todas as máquinas onde será instalado o serviço de Proxy de proteção de palavra-passe do Azure AD tem de ter .NET 4.7 instalado.
  .NET 4.7 já deve estar instalado num servidor do Windows totalmente atualizado. Se não for este o caso, transfira e execute o instalador adrese [o .NET Framework 4.7 instalador offline para Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todas as máquinas, incluindo controladores de domínio, o que obtém os componentes de proteção de palavra-passe do Azure AD instalados tem de ter o tempo de execução do C Universal instalado. Pode obter o tempo de execução, tornando-se de que tem todas as atualizações do Windows Update. Ou pode obtê-lo num pacote de atualização específicas do sistema operacional. Para obter mais informações, consulte [atualização para o tempo de execução do C Universal no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Conectividade de rede tem de existir entre, pelo menos, um controlador de domínio em cada domínio e pelo menos um servidor que aloja o serviço de proxy para a proteção de palavra-passe. Essa conectividade tem de permitir o controlador de domínio aceder a porta de mapeador de ponto final do RPC 135 e a porta do servidor RPC no serviço de proxy. Por predefinição, a porta do servidor RPC é uma porta dinâmica da RPC, mas pode ser configurado para [utilizar uma porta estática](#static).
* Todos os computadores que alojam o serviço de proxy tem de ter acesso à rede para os seguintes pontos finais:

    |**Endpoint**|**Objetivo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Pedidos de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de proteção de palavra-passe do Azure AD|

* Todos os computadores que alojam o serviço de proxy para a proteção de palavra-passe tem de ser configurados para permitir tráfego de saída HTTP do TLS 1.2.
* Uma conta de Administrador Global para registar o serviço de proxy para a proteção de palavra-passe e a floresta com o Azure AD.
* Uma conta que tenha privilégios de administrador de domínio do Active Directory no domínio de raiz de floresta para registar a floresta do Active Directory do Windows Server com o Azure AD.
* Qualquer domínio do Active Directory que executa o software de serviço do agente de controlador de domínio tem de utilizar a replicação de sistema de ficheiros (DFSR) distribuído na replicação sysvol.
* O serviço de distribuição de chaves tem de estar ativado em todos os controladores de domínio no domínio que executam o Windows Server 2012. Por predefinição, este serviço é ativado através do início do acionador manual.

## <a name="single-forest-deployment"></a>Implementação de floresta única

O diagrama seguinte mostra como os componentes básicos de proteção de palavra-passe do Azure AD funcionam em conjunto num ambiente do Active Directory no local.

![Como os componentes de proteção de palavra-passe do Azure AD funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa idéia verificar como o software funciona antes de o implementar. Ver [descrição geral Conceptual de proteção de palavra-passe do Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Transferir o software

Existem dois programas de instalação necessários para proteção de palavra-passe do Azure AD. Eles estão disponíveis a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalar e configurar o serviço de proxy para a proteção de palavra-passe

1. Escolha um ou mais servidores para hospedar o serviço de proxy para a proteção de palavra-passe.
   * Cada serviço como esse pode fornecer apenas as políticas de palavra-passe para uma única floresta. A máquina host deve ser associada a um domínio dessa floresta. Domínios de raiz e subordinadas são ambas suportados. Tem de ter conectividade de rede entre pelo menos um controlador de domínio em cada domínio da floresta e a máquina de proteção de palavra-passe.
   * Pode executar o serviço de proxy num controlador de domínio para fins de teste. Mas esse controlador de domínio, em seguida, precisa de conectividade de internet, que pode ser uma preocupação de segurança. Recomendamos esta configuração apenas para teste.
   * Recomendamos, pelo menos, dois servidores de proxy para redundância. Ver [elevada disponibilidade](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Instalar o Azure AD com serviço de Proxy de proteção de palavra-passe a `AzureADPasswordProtectionProxySetup.exe` instalador de software.
   * A instalação de software não requer um reinício. A instalação de software pode ser automatizada com procedimentos MSI padrão, por exemplo:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > O serviço de Firewall do Windows tem de ser executado antes de instalar o pacote de AzureADPasswordProtectionProxySetup.msi para evitar um erro de instalação. Se a Firewall do Windows é configurado para não ser executado, a solução é temporariamente ativar e executar o serviço de Firewall durante a instalação. O software de proxy não tem nenhuma dependência específica na Firewall do Windows após a instalação. Se estiver a utilizar uma firewall de terceiros, ele ainda deve ser configurado para satisfazer os requisitos de implementação. Estes incluem que permite acesso de entrada para porta 135 e o porta do servidor RPC de proxy. Ver [requisitos de implementação](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Abra uma janela do PowerShell como administrador.
   * O software de proxy de proteção de palavra-passe inclui um novo módulo do PowerShell *AzureADPasswordProtection*. Os seguintes passos executam vários cmdlets a partir deste módulo do PowerShell. Importe o novo módulo da seguinte forma:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Para verificar que o serviço está em execução, utilize o seguinte comando do PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     O resultado deve mostrar uma **estado** "Em execução".

1. Registre-se o proxy.
   * Depois de concluído o passo 3, o serviço de proxy está em execução na máquina. Mas o serviço ainda não tem as credenciais necessárias para comunicar com o Azure AD. É necessário o registo com o Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     Este cmdlet requer credenciais de administrador global do seu inquilino do Azure. Também precisa de privilégios de administrador local do Active Directory domínio no domínio raiz de floresta. Após este comando ser bem sucedida assim que um serviço de proxy, invocações adicionais do mesmo serão concluída com êxito, mas são desnecessárias.

      O `Register-AzureADPasswordProtectionProxy` cmdlet suporta os seguintes modos de autenticação de três.

     * Modo de autenticação interativa:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Este modo não funciona em sistemas de operativos do Server Core. Em vez disso, utilize um dos seguintes modos de autenticação. Além disso, este modo pode falhar se a configuração de segurança avançada do Internet Explorer está ativada. A solução é desativar essa configuração, registe-se o proxy e, em seguida, reativá-la.

     * Modo de autenticação de código de dispositivo:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Em seguida, conclua a autenticação ao seguir as instruções apresentadas num dispositivo diferente.

     * Modo de autenticação silenciosa (baseado em palavra-passe):

        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se for necessário o Azure multi-factor Authentication. Nesse caso, utilize um dos modos de autenticação de dois anterior.

       Ainda não tem de especificar o *- ForestCredential* parâmetro, que está reservado para futuras funcionalidades.

   
   Registo do serviço de proxy para a proteção de palavra-passe é necessário apenas uma vez durante a vida útil do serviço. Depois disso, o serviço de proxy efetuará automaticamente qualquer outra manutenção necessária.

   > [!TIP]
   > Pode haver um atraso considerável antes da conclusão na primeira vez que este cmdlet é executado para um inquilino do Azure específico. A menos que é comunicada uma falha, não se preocupe este atraso.

1. Registre-se a floresta.
   * É preciso inicializar a floresta do Active Directory no local com as credenciais necessárias para comunicar com o Azure, utilizando o `Register-AzureADPasswordProtectionForest` cmdlet do PowerShell. O cmdlet requer credenciais de administrador global do seu inquilino do Azure. Também requer privilégios de administrador local do Active Directory domínio no domínio raiz de floresta. Este passo é executado uma vez por floresta.

      O `Register-AzureADPasswordProtectionForest` cmdlet suporta os seguintes modos de autenticação de três.

     * Modo de autenticação interativa:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Este modo não funcionará em sistemas de operativos do Server Core. Em vez disso, utilize um dos seguintes modos de autenticação de dois. Além disso, este modo pode falhar se a configuração de segurança avançada do Internet Explorer está ativada. A solução é desativar essa configuração, registe-se o proxy e, em seguida, reativá-la.  

     * Modo de autenticação de código de dispositivo:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Em seguida, conclua a autenticação ao seguir as instruções apresentadas num dispositivo diferente.

     * Modo de autenticação silenciosa (baseado em palavra-passe):
        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se for necessário o Azure multi-factor Authentication. Nesse caso, utilize um dos modos de autenticação de dois anterior.

       Estes exemplos êxito apenas se o utilizador tem atualmente sessão iniciada também for um administrador de domínio do Active Directory para o domínio de raiz. Se isto não for o caso, pode fornecer credenciais de domínio alternativo através da *- ForestCredential* parâmetro.

   > [!NOTE]
   > Se vários servidores de proxy estão instalados no seu ambiente, não importa qual o servidor proxy utiliza para registar a floresta.

   > [!TIP]
   > Pode haver um atraso considerável antes da conclusão na primeira vez que este cmdlet é executado para um inquilino do Azure específico. A menos que é comunicada uma falha, não se preocupe este atraso.

   Registo de floresta do Active Directory é necessário apenas uma vez durante a vida útil da floresta. Depois disso, os agentes de controlador de domínio na floresta efetuará automaticamente qualquer outra manutenção necessária. Depois de `Register-AzureADPasswordProtectionForest` execuções com êxito para uma floresta, invocações adicionais do cmdlet com êxito, mas são desnecessárias.

   Para `Register-AzureADPasswordProtectionForest` para ter êxito, pelo menos um controlador de domínio com o Windows Server 2012 ou posterior tem de estar disponível no domínio do servidor de proxy. Mas o software do agente DC não tem de ser instalado em quaisquer controladores de domínio antes deste passo.

1. Configure o serviço de proxy para a proteção de palavra-passe de comunicar através de um proxy de HTTP.

   Se o seu ambiente requer a utilização de um proxy HTTP específico para comunicar com o Azure, utilize este método: Criar uma *AzureADPasswordProtectionProxy.exe.config* ficheiro na pasta %ProgramFiles%\Azure AD Proxy\Service de proteção de palavra-passe. Incluem o seguinte conteúdo:

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

   Se o proxy HTTP requer autenticação, adicione a *useDefaultCredentials* etiqueta:

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

   Em ambos os casos, substitua `http://yourhttpproxy.com:8080` com o endereço e porta do seu servidor proxy HTTP.

   Se o proxy HTTP estiver configurado para nós uma política de autorização, tem de conceder acesso à conta de computador do Active Directory da máquina que aloja o serviço de proxy para a proteção de palavra-passe.

   Recomendamos que para e reiniciar o serviço de proxy depois de criar ou atualizar o *AzureADPasswordProtectionProxy.exe.config* ficheiro.

   O serviço de proxy não suporta a utilização de credenciais específicas para conectar a um proxy de HTTP.

1. Opcional: Configure o serviço de proxy para a proteção de palavra-passe escutar numa porta específica.
   * O software do agente de controlador de domínio para a proteção de palavra-passe nos controladores de domínio utiliza o RPC sobre TCP para comunicar com o serviço de proxy. Por predefinição, o serviço de proxy escuta em qualquer ponto de extremidade RPC disponível dinâmico. Mas pode configurar o serviço para escutar numa porta TCP específica, se isso é necessário devido a topologia de redes ou requisitos de firewall no seu ambiente.
      * <a id="static" /></a>Para configurar o serviço para ser executado sob uma porta estática, utilize o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Tem de parar e reiniciar o serviço para que essas alterações entrem em vigor.

      * Para configurar o serviço para ser executado sob uma porta dinâmica, utilize o mesmo procedimento mas definido *StaticPort* a zero:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Tem de parar e reiniciar o serviço para que essas alterações entrem em vigor.

   > [!NOTE]
   > O serviço de proxy para a proteção de palavra-passe requer um reinício manual após qualquer alteração na configuração da porta. Mas não é necessário reiniciar o software do serviço de agente de DC em controladores de domínio depois de efetuar estas alterações de configuração.

   * Para consultar a configuração atual do serviço, utilize o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instalar o serviço de agente de DC

   Instalar o serviço de agente do controlador de domínio para a proteção de palavra-passe utilizando o `AzureADPasswordProtectionDCAgentSetup.msi` pacote.

   A instalação de software, ou a desinstalação, requer um reinício. Isto acontece porque o filtro de palavra-passe DLLs são apenas carregado ou descarregado por um reinício.

   Pode instalar o serviço do agente DC num computador que ainda não é um controlador de domínio. Neste caso, o serviço irá iniciar e executar mas permanecer inativo até que a máquina é promovida para ser um controlador de domínio.

   Pode automatizar a instalação de software utilizando os procedimentos MSI padrão. Por exemplo:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > O comando de msiexec do exemplo faz com que um reinício imediato. Para evitar isso, utilize o `/norestart` sinalizador.

A instalação estiver concluída depois do software do agente de controlador de domínio está instalado num controlador de domínio, e esse computador for reiniciado. Nenhuma outra configuração é necessária ou possíveis.

## <a name="multiple-forest-deployments"></a>Várias implementações de floresta

Não existem não requisitos adicionais para implementar a proteção de palavra-passe do Azure AD em várias florestas. Cada floresta independentemente está configurada conforme descrito na seção "implantação de floresta única". Cada proxy de proteção de palavra-passe só pode suportar os controladores de domínio da floresta de que ele está associado. O software de proteção de palavra-passe em qualquer floresta tem conhecimento do software de proteção de palavra-passe que é implementada nas outras florestas, independentemente das configurações de confiança do Active Directory.

## <a name="read-only-domain-controllers"></a>Controladores de domínio só de leitura

As alterações/conjuntos de palavra-passe não são processados e persistentes nos controladores de domínio só de leitura (RODCs). Eles são reencaminhados para controladores de domínio gravável. Por isso, não tem de instalar o software do agente DC em RODCs.

## <a name="high-availability"></a>Elevada disponibilidade

A preocupação de disponibilidade principal para a proteção de palavra-passe é a disponibilidade dos servidores de proxy quando os controladores de domínio numa floresta por tentar transferir novas políticas ou outros dados do Azure. Cada agente de controlador de domínio utiliza um algoritmo de round-robin-estilo simples ao decidir qual o servidor proxy para chamar. O agente ignora os servidores de proxy que não estão a responder. Para implementações do Active Directory mais totalmente conectadas com replicação bom estado de funcionamento do Estado de pasta de diretório e sysvol, dois servidores proxy é o suficiente para garantir a disponibilidade. Isso resulta em download oportuna de novas políticas e outros dados. Mas pode implementar servidores de proxy adicionais.

O design de software do agente DC atenua os problemas comuns que estão associados a elevada disponibilidade. O agente de DC mantém um cache local da política de palavra-passe mais recentemente transferida. Mesmo se registrado todos os servidores de proxy se tornar indisponíveis, os agentes do DC continuar para impor a política de palavra-passe em cache. Uma frequência de atualização razoável para políticas de palavra-passe numa implantação grande é normalmente *dias*, não horas ou menos. Assim, interrupções breves dos servidores proxy não afetam significativamente a proteção de palavra-passe do Azure AD.

## <a name="next-steps"></a>Passos Seguintes

Agora que instalou os serviços que precisa para a proteção de palavra-passe do Azure AD nos seus servidores no local, [executar a configuração de pós-instalação e coleta informações de relatórios](howto-password-ban-bad-on-premises-operations.md) para concluir a implementação.

[Descrição geral conceptual de proteção de palavra-passe do Azure AD](concept-password-ban-bad-on-premises.md)
