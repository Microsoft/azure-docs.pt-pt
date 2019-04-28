---
title: Gerir o agente de análise de registos do Azure | Documentos da Microsoft
description: Este artigo descreve as tarefas de gestão diferentes que, normalmente, realizará durante o ciclo de vida do Microsoft Monitoring Agent (MMA) implantados numa máquina.
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
ms.date: 04/23/2019
ms.author: magoedte
ms.openlocfilehash: 19530aa676e681f9a6ec50d2cacf77711dcb0110
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764086"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gerenciando e mantendo o agente Log Analytics para Windows e Linux

Após a implementação inicial do Windows do Log Analytics ou agente do Linux no Azure Monitor, terá de reconfigurar o agente, atualizá-lo ou removê-lo a partir do computador se atingiu a fase de extinção no seu ciclo de vida. Pode gerir facilmente essas tarefas de manutenção de rotina manualmente ou através da automatização, o que reduz o erro operacional e as despesas.

## <a name="upgrading-agent"></a>A atualizar agente

O agente do Log Analytics para Windows e Linux pode ser atualizado para a versão mais recente manual ou automaticamente dependendo do cenário de implementação e o ambiente que a VM está em execução numa. Os seguintes métodos podem ser utilizados para atualizar o agente.

| Ambiente | Método de instalação | Método de atualização |
|--------|----------|-------------|
| VM do Azure | Extensão VM do agente de análise de registo para Windows/Linux | Agente é atualizado automaticamente por predefinição, a menos que configurou o modelo do Azure Resource Manager para optar ativamente por participar, definindo a propriedade *autoUpgradeMinorVersion* ao **falso**. |
| Imagens de VM do Azure personalizadas | Instalação manual do agente do Log Analytics para Windows/Linux | A atualizar as VMs para a versão mais recente do agente tem de ser efetuada a partir da linha de comandos a executar o pacote de instalação do Windows ou Linux instalável e auto-extraível o shell script pacote.|
| VMs não pertencente ao Azure | Instalação manual do agente do Log Analytics para Windows/Linux | A atualizar as VMs para a versão mais recente do agente tem de ser efetuada a partir da linha de comandos a executar o pacote de instalação do Windows ou Linux instalável e auto-extraível o shell script pacote. |

### <a name="upgrade-windows-agent"></a>Atualizar o agente do Windows 

Para atualizar o agente numa VM do Windows para a versão mais recente não instalada com a extensão da VM do Log Analytics, optar por executar do Prompt de comando, script ou outra solução de automatização ou utilizando o MMASetup -\<plataforma\>. msi de configuração Assistente.  

Pode transferir a versão mais recente do agente do Windows da sua área de trabalho do Log Analytics, efetuando os seguintes passos.

1. Inicie sessão no Portal do Azure.

2. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **áreas de trabalho do Log Analytics**.

3. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho.

4. Na área de trabalho do Log Analytics, selecione **definições avançadas**, em seguida, selecione **origens ligadas**e finalmente **servidores Windows**.

5. Do **servidores do Windows** , selecione o adequado **transferir o agente do Windows** versão para transferir consoante a arquitetura do processador do sistema operativo Windows.

>[!NOTE]
>Durante a atualização do agente do Log Analytics para Windows, não suporta configurar nem reconfigurar para reportar a uma área de trabalho. Para configurar o agente, tem de seguir um dos métodos suportados listados na [adição ou remoção de uma área de trabalho](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Atualizar com o Assistente de configuração

1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Execute **MMASetup -\<plataforma\>.exe** para iniciar o Assistente de configuração.

3. Na primeira página do Assistente de configuração, clique em **seguinte**.

4. Na **configuração do Microsoft Monitoring Agent** caixa de diálogo, clique em **concordo** para aceitar o contrato de licença.

5. Na **configuração do Microsoft Monitoring Agent** caixa de diálogo, clique em **atualizar**. A página de estado apresenta o progresso da atualização.

6. Quando o **configuração do Microsoft Monitoring Agent concluída com êxito.** é apresentada a página, clique em **concluir**.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comandos

1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Para extrair os ficheiros de instalação do agente de uma linha de comandos elevada, execute `MMASetup-<platform>.exe /c` e que irá solicitar que o caminho extrair ficheiros para. Em alternativa, pode especificar o caminho pela transmissão de argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Execute o seguinte comando, onde D:\ é a localização para o ficheiro de registo de atualização.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Atualizar o agente do Linux 

Atualização de versões anteriores (> 1.0.0-47) é suportada. Efetuar a instalação com o `--upgrade` comando atualizará todos os componentes do agente para a versão mais recente.

Execute o seguinte comando para atualizar o agente.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Adicionar ou remover uma área de trabalho

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-from-control-panel"></a>Atualizar as definições no painel de controlo

1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controlo**.

3. Selecione **Microsoft Monitoring Agent** e, em seguida, clique nas **Azure Log Analytics** separador.

4. Se remover uma área de trabalho, selecione-o e, em seguida, clique em **remover**. Repita este passo para qualquer outra área de trabalho que pretende que o agente pare de reportar para.

5. Se adicionar uma área de trabalho, clique em **Add** e, no **adicionar uma área de trabalho do Log Analytics** caixa de diálogo, colar o ID de área de trabalho e chave da área de trabalho (chave primária). Se o computador deva reportar a uma área de trabalho do Log Analytics na cloud do Azure Government, selecione Azure US Government na lista pendente na Cloud do Azure.

6. Clique em **OK** para guardar as alterações.

#### <a name="remove-a-workspace-using-powershell"></a>Remover uma área de trabalho com o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Adicionar uma área de trabalho no Azure comercial com o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Adicionar uma área de trabalho no Azure dos EUA com o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se já usou a linha de comando ou script anteriormente para instalar ou configurar o agente `EnableAzureOperationalInsights` foi substituído por `AddCloudWorkspace` e `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agente do Linux
Os passos seguintes demonstram como reconfigurar o agente do Linux, se optar por registá-lo com uma área de trabalho diferente ou mesmo remover uma área de trabalho da sua configuração.

1. Para verificar se que está registado para uma área de trabalho, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Ele deverá retornar um status semelhante ao seguinte exemplo:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    É importante que o estado mostra também o agente está em execução, caso contrário, os seguintes passos para reconfigurar o agente não serão concluída com êxito.

2. Se já está registado com uma área de trabalho, remova a área de trabalho registada executando o seguinte comando. Caso contrário, se não estiver registado, avance para o passo seguinte.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Para registar com uma área de trabalho diferente, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Para verificar que as suas alterações demorou efeito, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Ele deverá retornar um status semelhante ao seguinte exemplo:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

O serviço de agente não precisa de ser reiniciados para que as alterações entrem em vigor.

## <a name="update-proxy-settings"></a>Atualizar as definições de proxy
Para configurar o agente para comunicar com o serviço através de um servidor proxy ou [gateway do Log Analytics](gateway.md) após a implementação, utilize um dos seguintes métodos para concluir esta tarefa.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-using-control-panel"></a>Atualizar as definições com painel de controlo

1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controlo**.

3. Selecione **Microsoft Monitoring Agent** e, em seguida, clique nas **definições de Proxy** separador.

4. Clique em **Use a proxy server** (Utilizar um servidor proxy) e indique o URL e o número de porta do servidor proxy ou do gateway. Se o seu servidor proxy ou o Log Analytics precisarem de autenticação, escreva o nome de utilizador e a palavra-passe para autenticar e clique em **OK**.

#### <a name="update-settings-using-powershell"></a>Definições de atualização com o PowerShell

Copie o código do PowerShell de exemplo seguinte, atualize-o com informações específicas do ambiente e guarde-o com uma extensão de nome de ficheiro PS1. Execute o script em cada computador que estabelece ligação direta à área de trabalho do Log Analytics no Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Agente do Linux
Se seus computadores com Linux precisarem de comunicar através de um servidor proxy ou gateway do Log Analytics, execute os seguintes passos. O valor de configuração do proxy tem a seguinte sintaxe `[protocol://][user:password@]proxyhost[:port]`. A propriedade *proxyhost* aceita um nome de domínio completamente qualificado ou o endereço IP do servidor proxy.

1. Edite o ficheiro `/etc/opt/microsoft/omsagent/proxy.conf` ao executar os comandos seguintes e altere os valores para as definições específicas.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Reinicie o agente ao executar o seguinte comando:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Desinstalar agente
Utilize um dos seguintes procedimentos para desinstalar o agente do Windows ou Linux utilizando o Assistente de linha de comando ou de configuração.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar no painel de controlo
1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Na **painel de controlo**, clique em **programas e funcionalidades**.

3. Na **programas e funcionalidades**, clique em **Microsoft Monitoring Agent**, clique em **desinstalação**e, em seguida, clique em **Sim**.

>[!NOTE]
>O Assistente de configuração do agente também pode ser executado fazendo duplo clique em **MMASetup -\<plataforma\>.exe**, que está disponível para transferência a partir de uma área de trabalho no portal do Azure.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar a partir da linha de comandos
O ficheiro transferido para o agente é um pacote de instalação autónoma e contém criado com IExpress. O programa de configuração para o agente e os ficheiros de suporte estão contidos no pacote e precisa extraídos para desinstalar corretamente com a linha de comando mostrada no exemplo a seguir.

1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Para extrair os ficheiros de instalação do agente de uma linha de comandos elevada, execute `extract MMASetup-<platform>.exe` e que irá solicitar que o caminho extrair ficheiros para. Em alternativa, pode especificar o caminho pela transmissão de argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Para obter mais informações sobre as opções da linha de comandos suportados pelo IExpress, consulte [comutadores da linha de comandos para IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e, em seguida, atualize o exemplo de acordo com as suas necessidades.

3. Na linha de comandos, escreva `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Agente do Linux
Para remover o agente, execute o seguinte comando no computador Linux. O argumento *--purge* remove por completo o agente e a respetiva configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurar o agente para reportar a um grupo de gestão do Operations Manager

### <a name="windows-agent"></a>Agente do Windows
Execute os seguintes passos para configurar o agente do Log Analytics para Windows reportar a um grupo de gestão do System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Inicie sessão computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controlo**.

3. Clique em **Microsoft Monitoring Agent** e, em seguida, clique nas **Operations Manager** separador.

4. Se os servidores do Operations Manager tem integração com o Active Directory, clique em **atualizar automaticamente atribuições de grupo de gestão do AD DS**.

5. Clique em **Add** para abrir o **adicionar um grupo de gestão** caixa de diálogo.

6. Na **nome do grupo de gestão** , digite o nome do seu grupo de gestão.

7. Na **servidor de gestão principal** , digite o nome do computador do servidor de gestão principal.

8. Na **porta do servidor de gestão** , digite o número da porta TCP.

9. Sob **conta de ação agente**, escolha a conta Sistema Local ou uma conta de domínio local.

10. Clique em **OK** para fechar a **adicionar um grupo de gestão** caixa de diálogo e clique em **OK** para fechar o **propriedades do agente de monitorização Microsoft** caixa de diálogo.

### <a name="linux-agent"></a>Agente do Linux
Execute os seguintes passos para configurar o agente do Log Analytics para Linux reportar a um grupo de gestão do System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Edite o ficheiro. `/etc/opt/omi/conf/omiserver.conf`

2. Certifique-se de que a linha que começa com `httpsport=` define a porta 1270. Tal como: `httpsport=1270`

3. Reinicie o servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Passos Seguintes

Revisão [resolução de problemas do agente Linux](agent-linux-troubleshoot.md) se ocorrerem problemas ao instalar ou gerir o agente.
