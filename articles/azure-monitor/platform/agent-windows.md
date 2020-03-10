---
title: Ligue os computadores Windows ao Monitor Azure [ Monitor] Microsoft Docs
description: Este artigo descreve como ligar computadores Windows hospedados em outras nuvens ou no local ao Monitor Azure com o agente Log Analytics para Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 21efb16cf519d4bcad520af1c7d8818f36a77218
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372944"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Ligar computadores Windows ao Monitor Azure

Para monitorizar e gerir máquinas virtuais ou computadores físicos no seu datacenter local ou outro ambiente na nuvem com o Monitor Azure, é necessário implementar o agente Log Analytics (também referido como O Agente de Monitorização da Microsoft (MMA)) e configurá-lo para reportar a um ou mais espaços de trabalho log Analytics. O agente também apoia o papel de Trabalhador de Runbook Híbrido para a Automação Azure.  

Num computador Windows monitorizado, o agente está listado como o serviço do Microsoft Monitoring Agent. O serviço Microsoft Monitoring Agent recolhe eventos a partir de ficheiros de registo e registo de eventos do Windows, dados de desempenho e outratelemetria. Mesmo quando o agente não consegue comunicar com o Azure Monitor a que reporta, o agente continua a executar e faz fila dos dados recolhidos no disco do computador monitorizado. Quando a ligação é restaurada, o serviço do Microsoft Monitoring Agent envia dados recolhidos para o serviço.

O agente pode ser instalado utilizando um dos seguintes métodos. A maioria das instalações utiliza uma combinação destes métodos para instalar diferentes conjuntos de computadores, conforme apropriado.  Os detalhes sobre a utilização de cada método são fornecidos mais tarde no artigo.

* Instalação manual. A configuração é executada manualmente no computador utilizando o assistente de configuração, a partir da linha de comando, ou implantado utilizando uma ferramenta de distribuição de software existente.
* Configuração do Estado desejada pela automatização azure (DSC). Utilizar o DSC em Automatização Azure com um script para computadores Windows já implantados no seu ambiente.  
* Roteiro PowerShell.
* Modelo de Gestor de Recursos para máquinas virtuais que executam o Windows no local em Azure Stack. 

>[!NOTE]
>O Azure Security Center (ASC) depende do Agente de Monitorização da Microsoft (também referido como o agente Do Windows de Log Analytics) e irá instalá-lo e configurá-lo para reportar a um espaço de trabalho do Log Analytics como parte da sua implementação. O ASC inclui uma opção de provisionamento automático que permite a instalação automática do agente Windows log Analytics em todos os VMs da sua subscrição e configura-o para reportar a um espaço de trabalho específico. Para obter mais informações sobre esta opção, consulte [Ativar o fornecimento automático do agente Log Analytics](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Se necessitar de configurar o agente para reportar a mais de um espaço de trabalho, este não pode ser realizado durante a configuração inicial, apenas depois atualizando as definições do Painel de Controlo ou powerShell conforme descrito na [Adição ou remoção](agent-manage.md#adding-or-removing-a-workspace)de um espaço de trabalho .  

Para compreender a configuração suportada, consulte [sistemas operativos Windows suportados](log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho
Antes de instalar o agente Log Analytics para windows, precisa do ID do espaço de trabalho e da chave para o seu espaço de trabalho Log Analytics.  Estas informações são necessárias durante a configuração de cada método de instalação para configurar corretamente o agente e garantir que pode comunicar com sucesso com o Azure Monitor na nuvem comercial e do Governo dos EUA. 

1. No portal Azure, procure e selecione espaços de **trabalho Log Analytics**.
2. Na sua lista de espaços de trabalho log analytics, selecione o espaço de trabalho a que pretende configurar o agente para reportar.
3. Selecione **Definições avançadas**.<br><br> ![Definições Avançadas do Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.   
5. Copie e cole no seu editor favorito, o **Id workspace** e **a chave primária.**    
   
## <a name="configure-agent-to-use-tls-12"></a>Configure agente para usar TLS 1.2
Para configurar a utilização do protocolo [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) para comunicação entre o agente Windows e o serviço Log Analytics, pode seguir os passos abaixo para ativar antes de o agente ser instalado na máquina virtual ou depois.

>[!NOTE]
>Se estiver a configurar um VM a executar o Windows Server 2008 SP2 x64 para utilizar o TLS 1.2, primeiro tem de instalar a seguinte atualização de suporte de assinatura de [código SHA-2](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) antes de executar os passos abaixo. 
>

1. Localize a seguinte subchave de registo: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Criar uma subchave ao abrigo de **protocolos** para TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Crie uma subchave **cliente** sob a versão de protocolo TLS 1.2 que criou anteriormente. Por exemplo, **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Crie os seguintes valores DWORD em **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client:**

    * **Habilitado** [Valor = 1]
    * **DesativadoByDefault** [Valor = 0]  

Configure a .NET Framework 4.6 ou posteriormente para suportar a criptografia segura, uma vez que por padrão é desativada. A [criptografia forte](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) utiliza protocolos de rede mais seguros como o TLS 1.2, e bloqueia protocolos que não são seguros. 

1. Localize a seguinte subchave de registo: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319**.  
2. Crie o valor DWORD **SchUseStrongCrypto** sob esta subchave com um valor de **1**.  
3. Localize a seguinte subchave de registo: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Crie o valor DWORD **SchUseStrongCrypto** sob esta subchave com um valor de **1**. 
5. Reiniciar o sistema para que as definições entrem em vigor. 

## <a name="install-the-agent-using-setup-wizard"></a>Instale o agente utilizando o assistente de configuração
Os seguintes passos instalam e configuram o agente Log Analytics na nuvem azure e azure government utilizando o assistente de configuração para o agente no seu computador. Se quiser aprender a configurar o agente para também reportar a um grupo de gestão de Gestão de Operações do System Center, consulte o agente do Gestor de [Operações com o Assistente de Configuração do Agente](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. No seu espaço de trabalho log Analytics, a partir da página **do Windows Servers** para a qual navegou anteriormente, selecione a versão adequada do **Download Windows Agent** para descarregar dependendo da arquitetura do processador do sistema operativo Windows.   
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

Quando terminar, o **Microsoft Monitoring Agent** aparece no **Painel de Controlo**. Para confirmar que está a reportar ao Log Analytics, reveja a conectividade do [agente para o Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instale o agente utilizando a linha de comando
O ficheiro descarregado para o agente é um pacote de instalação independente.  O programa de configuração para o agente e ficheiros de suporte estão contidos na embalagem e precisam de ser extraídos para instalar corretamente utilizando a linha de comando mostrada nos seguintes exemplos.    

>[!NOTE]
>Se quiser atualizar um agente, tem de utilizar a API de scripts Log Analytics. Consulte o tópico [De Gestão e Manutenção do agente Log Analytics para Windows e Linux](agent-manage.md) para mais informações.

A tabela seguinte destaca os parâmetros específicos suportados pela configuração do agente, incluindo quando implantado utilizando o DSC da Automação.

|Opções específicas do MMA                   |Notas         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parâmetro opcional. Instala o agente sem monitorização de desempenho da aplicação .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Configure o agente para reportar a um espaço de trabalho                |
|OPINSIGHTS_WORKSPACE_ID                | ID do espaço de trabalho (guia) para o espaço de trabalho adicionar                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave espaço de trabalho usado para autenticar inicialmente com o espaço de trabalho |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especifique o ambiente de nuvem onde o espaço de trabalho está localizado <br> 0 = Nuvem comercial Azure (padrão) <br> 1 = Governo Azure |
|OPINSIGHTS_PROXY_URL               | URI para o proxy usar |
|OPINSIGHTS_PROXY_USERNAME               | Nome de utilizador para aceder a um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Senha para aceder a um proxy autenticado |

1. Para extrair os ficheiros de instalação do agente, a partir de um comando elevado, `MMASetup-<platform>.exe /c` e irá levá-lo para o caminho para extrair ficheiros para.  Em alternativa, pode especificar o caminho passando os argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Para instalar silenciosamente o agente e configurá-lo para reportar a um espaço de trabalho na nuvem comercial Azure, a partir da pasta que extraiu os ficheiros de configuração para escrever: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   ou para configurar o agente para reportar à nuvem do Governo dos EUA, escreva: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Os valores das cordas para os parâmetros *OPINSIGHTS_WORKSPACE_ID* e *OPINSIGHTS_WORKSPACE_KEY* precisam de ser encapsulados em duas citações para instruir o Instalador do Windows a interpor opções válidas para o pacote. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instale o agente que utiliza o DSC na Automação Azure

Pode utilizar o seguinte exemplo de script para instalar o agente utilizando o Azure Automation DSC.   Se não tiver uma conta de Automação, consulte O Get start com a [Azure Automation](/azure/automation/) para compreender os requisitos e passos necessários para criar uma conta de Automação necessária antes de utilizar o Automation DSC.  Se não está familiarizado com a Automation DSC, reveja [Começar com A Automação DSC](../../automation/automation-dsc-getting-started.md).

O exemplo seguinte instala o agente de 64 bits, identificado pelo valor `URI`. Também pode utilizar a versão de 32 bits substituindo o valor URI. Os URIs para ambas as versões são:

- Agente windows 64-bit - https://go.microsoft.com/fwlink/?LinkId=828603
- Agente do Windows 32-bit - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Este procedimento e o exemplo do script não suportam a atualização do agente já implantado para um computador Windows.

As versões de 32 bits e 64 bits do pacote de agente têm diferentes códigos de produto e as novas versões lançadas também têm um valor único.  O código do produto é um GUID que é a principal identificação de uma aplicação ou produto e está representado pela propriedade do **ProductCode** do Instalador do Windows.  O valor `ProductId` no script **MMAgent.ps1** tem de coincidir com o código do produto do pacote de instalação de agente de 32 bits ou 64 bits.

Para recuperar o código de produto do pacote de instalação do agente diretamente, pode utilizar Orca.exe a partir dos [Componentes SDK do Windows para desenvolvedores](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) de instaladores windows que é um componente do Kit de Desenvolvimento de Software windows ou usando powerShell seguindo um script de [exemplo](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) escrito por um Microsoft Valuable Professional (MVP).  Para qualquer uma das abordagens, primeiro é necessário extrair o ficheiro **MOMagent.msi** do pacote de instalação MMASetup.  Isto é mostrado no início do primeiro passo sob a secção [Instale o agente utilizando a linha de comando](#install-the-agent-using-the-command-line).  

1. Importar o módulo DSC de [configuração](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do Estado xPSDesired Configuração de https://www.powershellgallery.com/packages/xPSDesiredStateConfigurationpara a Automação Azure.  
2.  Crie ativos variáveis da Automação Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY.* *Detete OPSINSIGHTS_WS_ID* para o seu ID de espaço de trabalho Log Analytics e detete *OPSINSIGHTS_WS_KEY* a chave principal do seu espaço de trabalho.
3.  Copie o script e guarde-o como MMAgent.ps1.

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

4. Atualize o valor `ProductId` no script com o código do produto extraído da versão mais recente do pacote de instalação do agente utilizando os métodos recomendados anteriormente. 
5. [Importe o script de configuração MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na sua conta De automação. 
5. [Atribuir um computador ou nó do Windows](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) à configuração. Dentro de 15 minutos, o nó verifica a sua configuração e o agente é empurrado para o nó.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verifique a conectividade do agente com o Log Analytics

Uma vez concluída a instalação do agente, verificar se está ligado com sucesso e o relatório pode ser realizado de duas formas.  

No computador, no **Painel de Controlo**, localize o item **Microsoft Monitoring Agent**.  Selecione-o e no separador **Azure Log Analytics,** o agente deve apresentar uma mensagem indicando: o Agente de Monitorização da **Microsoft ligou-se com sucesso ao serviço Microsoft Operations Management Suite.**<br><br> ![Estado da ligação do MMA ao Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Também pode realizar uma simples consulta de log no portal Azure.  

1. No portal Azure, procure e selecione **Monitor**.
1. Selecione **Registos** no menu.
1. No painel de **registos,** no tipo de campo de consulta:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nos resultados da pesquisa devolvidos, deve ver registos de batimentos cardíacos para o computador indicando que está ligado e reportando ao serviço.   

## <a name="next-steps"></a>Passos Seguintes

- Reveja [a gestão e manutenção do agente Log Analytics para windows e Linux](agent-manage.md) para aprender como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Reveja [o problema do agente Windows](agent-windows-troubleshoot.md) se encontrar problemas durante a instalação ou gestão do agente.
