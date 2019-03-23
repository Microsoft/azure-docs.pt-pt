---
title: Ligar computadores Windows ao Log Analytics do Azure | Documentos da Microsoft
description: Este artigo descreve como ligar computadores Windows alojados em outras clouds ou no local para o Log Analytics com o Microsoft Monitoring Agent (MMA).
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
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: f9df65d143fbb0eaf6276a0f38971e19c0741786
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370963"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Ligar computadores Windows para o Azure Monitor

Para monitorizar e gerir máquinas virtuais ou computadores físicos no seu datacenter local ou outro ambiente de cloud com o Azure Monitor, precisa implementar o agente do Log Analytics (também referido como o Microsoft Monitoring Agent (MMA)) e configurá-lo para reportar a um ou mais áreas de trabalho do Log Analytics. O agente também suporta a função de trabalho de Runbook híbrida de automatização do Azure.  

Num computador Windows monitorizado, o agente é listado como o serviço Microsoft Monitoring Agent. O serviço Microsoft Monitoring Agent recolhe os eventos de ficheiros de registo e registo de eventos do Windows, dados de desempenho e outra telemetria. Mesmo quando o agente não consegue comunicar com o Azure Monitor reporta a, o agente continua a ser executado e coloca em fila os dados recolhidos no disco do computador monitorizado. Quando a ligação é restaurada, o serviço Microsoft Monitoring Agent envia os dados recolhidos para o serviço.

O agente pode ser instalado utilizando um dos seguintes métodos. A maioria das instalações usar uma combinação dos seguintes métodos para instalar diferentes conjuntos de computadores, conforme apropriado.  São fornecidos detalhes sobre como utilizar cada método posteriormente neste artigo.

* Instalação manual. A configuração é executada manualmente no computador utilizando o Assistente de configuração da linha de comando, ou implementado através de uma ferramenta de distribuição de software existente.
* O Azure Automation Desired State Configuration (DSC). Utilizar a DSC na automatização do Azure com um script de Windows computadores já implantados no seu ambiente.  
* Script do PowerShell.
* Modelo do Resource Manager para máquinas virtuais em execução no local do Windows no Azure Stack.  

>[!NOTE]
>Centro de segurança do Azure (ASC) depende do Microsoft Monitoring Agent (também referido como o agente do Log Analytics Windows) e irão instalar e configurá-lo para comunicar-se para uma área de trabalho do Log Analytics como parte da sua implementação. ASC inclui uma opção de aprovisionamento automática que permite que a instalação automática do agente do Log Analytics Windows em todas as VMs na sua subscrição e configura-o para reportar a uma área de trabalho específica. Para obter mais informações sobre esta opção, consulte [ativar o aprovisionamento automático do agente do Log Analytics](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-microsoft-monitoring-agent-).
>

Para compreender a configuração suportada, consulte [sistemas operativos Windows suportados](log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho
Antes de instalar o agente do Log Analytics para Windows, terá do ID de área de trabalho e a chave para a área de trabalho do Log Analytics.  Estas informações são necessárias durante a configuração de cada método de instalação para configurar corretamente o agente e certifique-se de que pode comunicar com êxito com o Azure Monitor no Azure comercial e na cloud do Governo dos EUA.  

1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. Na sua lista de áreas de trabalho do Log Analytics, selecione a área de trabalho que tenciona sobre como configurar o agente para reportar a.
3. Selecione **Definições avançadas**.<br><br> ![Definições Avançadas do Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Windows**.   
5. Copie e cole no seu editor favorito, o **ID da área de trabalho** e **chave primária**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Configurar o agente para utilizar o TLS 1.2
Para configurar a utilização do [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protocolo para comunicação entre o agente do Windows e o serviço Log Analytics, pode seguir os passos abaixo para ativar antes do agente está instalado na máquina virtual ou posteriormente.   

1. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Criar uma subchave no **protocolos** para o TLS 1.2 **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Criar uma **cliente** subchave subchave de versão do protocolo TLS 1.2 que criou anteriormente. Por exemplo, **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Criar os seguintes valores DWORD em **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Ativado** [valor = 1]
    * **DisabledByDefault** [valor = 0]  

Configurar o .NET Framework 4.6 ou posterior para dar suporte seguro criptografia, como, por padrão, ele está desabilitada. O [criptografia segura](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) utiliza protocolos de rede mais seguros, como o TLS 1.2 e bloqueia protocolos que não são seguros. 

1. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Criar o valor DWORD **SchUseStrongCrypto** esta subchave com um valor de **1**.  
3. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Criar o valor DWORD **SchUseStrongCrypto** esta subchave com um valor de **1**. 
5. Reinicie o sistema para as definições entrem em vigor. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalar o agente utilizando o Assistente de configuração
Os seguintes passos instale e configure o agente Log Analytics na cloud do Azure e o Azure Government, utilizando o Assistente de configuração para o agente no seu computador. Se quiser saber como configurar o agente para também reportar a um grupo de gestão do System Center Operations Manager, consulte [implementar o agente do Operations Manager com o Assistente de configuração do agente](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Na área de trabalho do Log Analytics, do **servidores do Windows** página navegada anteriormente, selecionar o **transferir o agente do Windows** versão para transferir consoante a arquitetura de processador do o sistema operativo do Windows.   
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

Quando terminar, o **Microsoft Monitoring Agent** aparece no **Painel de Controlo**. Para confirmar que está a reportar para o Log Analytics, reveja [verificar a conectividade de agente para o Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente utilizando a linha de comandos
O ficheiro transferido para o agente é um pacote de instalação autônomo.  O programa de configuração para o agente e os ficheiros de suporte estão contidos no pacote e precisa extraídos para instalar corretamente com a linha de comando mostrada nos exemplos a seguir.    

>[!NOTE]
>Se quiser atualizar um agente, terá de utilizar a API de scripting do Log Analytics. Consulte o tópico [gerir e manter o agente Log Analytics para Windows e Linux](agent-manage.md) para obter mais informações.

A tabela seguinte realça os parâmetros específicos suportados pelo programa de configuração para o agente, incluindo quando implementado utilizando o DSC de automatização.

|Opções específicas do MMA                   |Notas         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parâmetro opcional. Instala o agente sem monitorização de desempenho de aplicações de .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = configurar o agente para reportar a uma área de trabalho                |
|OPINSIGHTS_WORKSPACE_ID                | ID de área de trabalho (guid) para a área de trabalho para adicionar                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave da área de trabalho utilizado para autenticar inicialmente com a área de trabalho |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especificar o ambiente de nuvem onde está localizada a área de trabalho <br> 0 = cloud comercial do azure (predefinição) <br> 1 = o azure Government |
|OPINSIGHTS_PROXY_URL               | URI de proxy a utilizar |
|OPINSIGHTS_PROXY_USERNAME               | Nome de utilizador para aceder a um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Palavra-passe para aceder a um proxy autenticado |

1. Para extrair os ficheiros de instalação do agente de uma linha de comandos elevada, execute `MMASetup-<platform>.exe /c` e que irá solicitar que o caminho extrair ficheiros para.  Em alternativa, pode especificar o caminho pela transmissão de argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Para instalar o agente e configurá-lo para reportar a uma área de trabalho na cloud comercial do Azure, a partir da pasta silenciosamente extraiu os ficheiros de configuração para o tipo: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   ou para configurar o agente para reportar a cloud do Azure US Government, escreva: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente através do DSC na automatização do Azure

Pode utilizar o seguinte exemplo de script para instalar o agente utilizando o DSC de automatização do Azure.   Se não tiver uma conta de automatização, consulte [introdução à automatização do Azure](/azure/automation/) para compreender os requisitos e passos para criar uma conta de automatização necessária antes de utilizar o DSC de automatização.  Se não estiver familiarizado com o DSC de automatização, reveja [introdução ao DSC de automatização](../../automation/automation-dsc-getting-started.md).

O exemplo seguinte instala o agente de 64 bits, identificado pelo `URI` valor. Também pode utilizar a versão de 32 bits, substituindo o valor do URI. Os URIs para as duas versões são:

- Agente de 64 bits do Windows- https://go.microsoft.com/fwlink/?LinkId=828603
- Agente de 32 bits do Windows- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Neste exemplo de procedimento e o script não suporta a atualização do agente já implementado num computador Windows.

As versões de 32 bits e 64 bits do pacote de agente têm códigos de produto diferente e novas versões lançadas também tem um valor exclusivo.  O código de produto é um GUID que é a identificação do principal de um aplicativo ou o produto e é representado pelo instalador do Windows **ProductCode** propriedade.  O `ProductId` valor de **MMAgent.ps1** script tem de corresponder ao código de produto do pacote de instalador do agente de 32 bits ou 64 bits.

Para obter o código de produto a partir do pacote de instalação do agente diretamente, pode usar Orca.exe do [Windows componentes para o Windows Installer programadores do SDK](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) ou seja, um componente do Kit de desenvolvimento de Software do Windows ou a utilizar PowerShell seguem uma [script de exemplo](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) escrito por um Microsoft Valuable Professional (MVP).  Para qualquer uma das abordagens, tem primeiro de extrair os **momagent. msi** ficheiro do pacote de instalação MMASetup.  Isso é mostrado anteriormente no primeiro passo na secção [instalar o agente utilizando a linha de comandos](#install-the-agent-using-the-command-line).  

1. Módulo de importação a DSC xPSDesiredStateConfiguration da [ https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) para a automatização do Azure.  
2.  Criar recursos de variável da automatização do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Definir *OPSINSIGHTS_WS_ID* para o seu ID de área de trabalho do Log Analytics e o conjunto *OPSINSIGHTS_WS_KEY* para a chave primária da sua área de trabalho.
3.  Copie o script e guarde-o como MMAgent.ps1.

    ```PowerShell
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

4. Atualização do `ProductId` valor no script com o código de produto extraído da versão mais recente do agente de instalar o pacote usando os métodos recomendados anteriormente. 
5. [Importe o script de configuração MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na sua conta de automatização. 
5. [Atribuir um computador Windows ou o nó](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) para a configuração. Em 15 minutos, o nó verifica a respetiva configuração e o agente é enviado para o nó.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verificar a conectividade de agente para o Log Analytics

Após a conclusão da instalação do agente, verificar é ligado com êxito e de relatórios podem ser feito de duas formas.  

No computador no **painel de controlo**, localize o item **Microsoft Monitoring Agent**.  Selecione-o e, no **do Azure Log Analytics** guia, o agente deve exibir uma mensagem a indicar: **O Microsoft Monitoring Agent foi ligado com êxito para o serviço do Microsoft Operations Management Suite.**<br><br> ![Estado da ligação do MMA ao Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Também pode executar uma consulta de registo simples no portal do Azure.  

1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **do Azure Monitor**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **o Azure Monitor**.  
2. Selecione **registos** no menu. 
2. No painel de registos, no tipo de campo de consulta:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nos resultados da pesquisa retornados, deverá ver registos de heartbeat para o computador que indica que está ligado e relatórios para o serviço.   

## <a name="next-steps"></a>Passos Seguintes

Revisão [gerir e manter o agente Log Analytics para Windows e Linux](agent-manage.md) para saber mais sobre como gerir o agente durante seu ciclo de vida de implementação nas suas máquinas.  
