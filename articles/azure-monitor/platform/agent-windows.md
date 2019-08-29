---
title: Conectar computadores Windows ao Azure Monitor | Microsoft Docs
description: Este artigo descreve como conectar computadores Windows hospedados em outras nuvens ou locais para Azure Monitor com o agente do Log Analytics para Windows.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: magoedte
ms.openlocfilehash: 5e1fe6252f396a4585b5d7d7190728b79229d5c7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073979"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Conectar computadores Windows ao Azure Monitor

Para monitorar e gerenciar máquinas virtuais ou computadores físicos em seu datacenter local ou em outro ambiente de nuvem com Azure Monitor, você precisa implantar o agente de Log Analytics (também conhecido como o Microsoft Monitoring Agent (MMA)) e configurá-lo como relatar para um ou mais espaços de trabalho do Log Analytics. O agente também dá suporte à função de Hybrid Runbook Worker para a automação do Azure.  

Em um computador Windows monitorado, o agente é listado como o serviço de Microsoft Monitoring Agent. O serviço de Microsoft Monitoring Agent coleta eventos de arquivos de log e log de eventos do Windows, dados de desempenho e outras telemetrias. Mesmo quando o agente não consegue se comunicar com Azure Monitor ele relata, o agente continua a ser executado e enfileira os dados coletados no disco do computador monitorado. Quando a conexão é restaurada, o serviço de Microsoft Monitoring Agent envia os dados coletados para o serviço.

O agente pode ser instalado usando um dos métodos a seguir. A maioria das instalações usa uma combinação desses métodos para instalar diferentes conjuntos de computadores, conforme apropriado.  Detalhes sobre como usar cada método são fornecidos posteriormente neste artigo.

* Instalação manual. A instalação é executada manualmente no computador usando o assistente de instalação, na linha de comando ou implantada usando uma ferramenta de distribuição de software existente.
* DSC (configuração de estado desejado) da automação do Azure. Usando a DSC na automação do Azure com um script para computadores Windows já implantados em seu ambiente.  
* Script do PowerShell.
* Modelo do Resource Manager para máquinas virtuais que executam o Windows local no Azure Stack. 

>[!NOTE]
>A central de segurança do Azure (ASC) depende do Microsoft Monitoring Agent (também conhecido como o Log Analytics agente do Windows) e irá instalá-lo e configurá-lo para relatar a um espaço de trabalho Log Analytics como parte de sua implantação. O ASC inclui uma opção de provisionamento automático que habilita a instalação automática do Log Analytics agente do Windows em todas as VMs em sua assinatura e a configura para relatar a um espaço de trabalho específico. Para obter mais informações sobre essa opção, consulte [habilitar o provisionamento automático do agente de log Analytics](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-the-log-analytics-agent-).
>

Se você precisar configurar o agente para relatar mais de um espaço de trabalho, isso não poderá ser executado durante a configuração inicial, somente depois atualizando as configurações do painel de controle ou do PowerShell, conforme descrito em [adicionando ou removendo um espaço de trabalho](agent-manage.md#adding-or-removing-a-workspace).  

Para compreender a configuração suportada, consulte [sistemas operativos Windows suportados](log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho
Antes de instalar o agente de Log Analytics para Windows, você precisa da ID e da chave do espaço de trabalho para seu espaço de trabalho Log Analytics.  Essas informações são necessárias durante a instalação de cada método de instalação para configurar corretamente o agente e garantir que ele possa se comunicar com êxito com Azure Monitor no Azure comercial e na nuvem do governo dos EUA. 

1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho no qual você pretende configurar o agente para relatar.
3. Selecione **Definições avançadas**.<br><br> ![Definições Avançadas do Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.   
5. Copie e cole em seu editor favorito, a **ID do espaço de trabalho** e a **chave primária**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Configurar o agente para usar o TLS 1,2
Para configurar o uso do protocolo [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) para a comunicação entre o agente do Windows e o serviço de log Analytics, você pode seguir as etapas abaixo para habilitar o antes de o agente ser instalado na máquina virtual ou depois.   

1. Localize a seguinte subchave do registro: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Crie uma subchave em **protocolos** para TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Crie uma subchave do **cliente** na subchave da versão do protocolo TLS 1,2 criada anteriormente. Por exemplo, **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. Crie os seguintes valores DWORD em **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**:

    * **Habilitado** [Valor = 1]
    * **DisabledByDefault** [Valor = 0]  

Configure o .NET Framework 4,6 ou posterior para dar suporte à criptografia segura, pois ele está desabilitado por padrão. A [criptografia forte](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) usa protocolos de rede mais seguros, como o TLS 1,2, e bloqueia protocolos que não são seguros. 

1. Localize a seguinte subchave do registro: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Crie o valor DWORD **SchUseStrongCrypto** sob essa subchave com um valor de **1**.  
3. Localize a seguinte subchave do registro: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Crie o valor DWORD **SchUseStrongCrypto** sob essa subchave com um valor de **1**. 
5. Reinicie o sistema para que as configurações entrem em vigor. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalar o agente usando o assistente para instalação
As etapas a seguir instalam e configuram o agente de Log Analytics no Azure e a nuvem do Azure governamental usando o assistente de instalação para o agente em seu computador. Se você quiser saber como configurar o agente para também relatar a um grupo de gerenciamento de System Center Operations Manager, consulte [implantar o agente de Operations Manager com o assistente de instalação do agente](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. No espaço de trabalho do Log Analytics, na página **servidores do Windows** que você navegou anteriormente, selecione a versão de download apropriada do **agente do Windows** para download, dependendo da arquitetura do processador do sistema operacional Windows.   
2. Execute a Configuração para instalar o agente no seu computador.
2. Na página **Bem-vindo**, clique em **Seguinte**.
3. Na página **Termos de Licenciamento**, leia a licença e clique em **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação predefinida e clique em **Seguinte**.
5. Na página **Opções de Configuração do Agente** , escolha ligar o agente ao Azure Log Analytics e clique em **Seguinte**.   
6. Na página **Azure Log Analytics**, faça o seguinte:
   1. Cole o **ID da Área de Trabalho** e a **Chave da Área de Trabalho (Chave Primária)** que copiou anteriormente.  Caso o computador deva reportar a uma área de trabalho do Log Analytics na cloud do Azure Government, selecione **Azure US Government**, na lista pendente **Cloud do Azure**.  
   2. Se o computador tiver de comunicar através de um servidor proxy com o serviço do Log Analytics, clique em **Avançadas** e forneça o URL e o número da porta do servidor proxy.  Se o seu servidor proxy precisar de autenticação, escreva o nome de utilizador e a palavra-passe para autenticação no mesmo e clique em **Seguinte**.  
7. Depois de indicar as definições de configuração necessárias, clique em **Seguinte**.<br><br> ![colar o ID da Área de Trabalho e a Chave Primária](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na página **Pronto para Instalar**, reveja as suas opções e clique em **Instalar**.
9. Na página **Configuração Concluída com Êxito**, clique em **Concluir**.

Quando terminar, o **Microsoft Monitoring Agent** aparece no **Painel de Controlo**. Para confirmar se está relatando para Log Analytics, examine [verificar a conectividade do agente para log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente usando a linha de comando
O arquivo baixado para o agente é um pacote de instalação independente.  O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisa ser extraído para que você possa instalar corretamente usando a linha de comando mostrada nos exemplos a seguir.    

>[!NOTE]
>Se você quiser atualizar um agente, precisará usar a API de script Log Analytics. Consulte o tópico [Gerenciando e mantendo o agente de log Analytics para Windows e Linux](agent-manage.md) para obter mais informações.

A tabela a seguir realça os parâmetros específicos com suporte na instalação do agente, incluindo quando implantado usando o DSC de Automação.

|Opções específicas do MMA                   |Notas         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parâmetro opcional. Instala o agente sem o monitoramento do desempenho de aplicativos .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = configurar o agente para relatar a um espaço de trabalho                |
|OPINSIGHTS_WORKSPACE_ID                | ID do espaço de trabalho (GUID) para o espaço de trabalho a ser adicionado                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave do espaço de trabalho usada para autenticar inicialmente com o espaço de trabalho |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especifique o ambiente de nuvem onde o espaço de trabalho está localizado <br> 0 = nuvem comercial do Azure (padrão) <br> 1 = Azure governamental |
|OPINSIGHTS_PROXY_URL               | URI para o proxy a ser usado |
|OPINSIGHTS_PROXY_USERNAME               | Nome de usuário para acessar um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Senha para acessar um proxy autenticado |

1. Para extrair os arquivos de instalação do agente, em uma execução `MMASetup-<platform>.exe /c` de prompt de comandos com privilégios elevados, será solicitado que você forneça o caminho para o qual extrair os arquivos.  Como alternativa, você pode especificar o caminho passando os argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Para instalar silenciosamente o agente e configurá-lo para relatar a um espaço de trabalho na nuvem comercial do Azure, na pasta na qual você extraiu os arquivos de instalação para o tipo: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   ou para configurar o agente para relatar para a nuvem do governo dos EUA do Azure, digite: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Os valores de cadeia de caracteres para os parâmetros *OPINSIGHTS_WORKSPACE_ID* e *OPINSIGHTS_WORKSPACE_KEY* precisam ser encapsulados em aspas duplas para instruir Windows Installer a interprit como opções válidas para o pacote. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente usando a DSC na automação do Azure

Você pode usar o exemplo de script a seguir para instalar o agente usando o DSC de Automação do Azure.   Se você não tiver uma conta de automação, consulte Introdução à [automação do Azure](/azure/automation/) para entender os requisitos e as etapas para criar uma conta de automação necessária antes de usar DSC de automação.  Se você não estiver familiarizado com DSC de Automação, consulte [introdução ao DSC de automação](../../automation/automation-dsc-getting-started.md).

O exemplo a seguir instala o agente de 64 bits, identificado pelo `URI` valor. Você também pode usar a versão de 32 bits substituindo o valor do URI. Os URIs para ambas as versões são:

- Agente de bits do Windows 64- https://go.microsoft.com/fwlink/?LinkId=828603
- Agente de bits do Windows 32- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Este exemplo de procedimento e script não oferece suporte à atualização do agente já implantado em um computador Windows.

As versões de 32 bits e 64 bits do pacote do agente têm códigos de produto diferentes e novas versões lançadas também têm um valor exclusivo.  O código do produto é um GUID que é a identificação principal de um aplicativo ou produto e é representado pela propriedade Windows Installer **ProductCode** .  O `ProductId` valor no script **MMAgent. ps1** deve corresponder ao código do produto do pacote do instalador do agente de 32 bits ou 64 bits.

Para recuperar o código do produto do pacote de instalação do agente diretamente, você pode usar o Orca. exe a partir dos [componentes de SDK do Windows para os desenvolvedores de Windows Installer](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) que é um componente do Windows Software Development Kit ou usando o PowerShell após um [ script de exemplo](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) escrito por um MVP (Microsoft valioso Professional).  Para qualquer abordagem, você precisa primeiro extrair o arquivo **MOMAgent. msi** do pacote de instalação do MMASetup.  Isso é mostrado anteriormente na primeira etapa na seção [instalar o agente usando a linha de comando](#install-the-agent-using-the-command-line).  

1. Importe o módulo DSC xPSDesiredStateConfiguration de [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) para a automação do Azure.  
2.  Crie ativos de variável de automação do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Defina *OPSINSIGHTS_WS_ID* como sua ID do espaço de trabalho log Analytics e defina *OPSINSIGHTS_WS_KEY* como a chave primária do seu espaço de trabalho.
3.  Copie o script e salve-o como MMAgent. ps1.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Atualize o `ProductId` valor no script com o código do produto extraído da versão mais recente do pacote de instalação do agente usando os métodos recomendados anteriormente. 
5. [Importe o script de configuração MMAgent. ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) para sua conta de automação. 
5. [Atribua um computador ou nó do Windows](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) à configuração. Dentro de 15 minutos, o nó verifica sua configuração e o agente é enviado por push para o nó.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verificar a conectividade do agente para Log Analytics

Depois que a instalação do agente for concluída, verifique se ele está conectado com êxito e se os relatórios podem ser realizados de duas maneiras.  

No computador, no **Painel de Controlo**, localize o item **Microsoft Monitoring Agent**.  Selecione-o e, no separador **Azure Log Analytics**, o agente deverá apresentar uma mensagem que diz: **O Microsoft Monitoring Agent se conectou com êxito ao serviço de Microsoft Operations Management Suite.**<br><br> ![Estado da ligação do MMA ao Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Você também pode executar uma consulta de log simples no portal do Azure.  

1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, digite **Azure monitor**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Azure monitor**.  
2. Selecione **logs** no menu. 
2. No painel logs, no campo de consulta tipo:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nos resultados da pesquisa retornados, você deverá ver os registros de pulsação do computador indicando que ele está conectado e relatando para o serviço.   

## <a name="next-steps"></a>Passos seguintes

- Examine [o gerenciamento e a manutenção do agente de log Analytics para Windows e Linux](agent-manage.md) para saber mais sobre como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Examine [a solução de problemas do agente do Windows](agent-windows-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente.
