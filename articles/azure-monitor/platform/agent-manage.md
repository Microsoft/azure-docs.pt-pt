---
title: Gerenciando o agente de Log Analytics do Azure | Microsoft Docs
description: Este artigo descreve as diferentes tarefas de gerenciamento que normalmente serão executadas durante o ciclo de vida do Log Analytics o agente Windows ou Linux implantado em um computador.
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
ms.openlocfilehash: 0c128aaf8102b3072b6a63c80ea860ceefbf5124
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67146298"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gerenciando e mantendo o agente de Log Analytics para Windows e Linux

Após a implantação inicial do agente Log Analytics Windows ou Linux no Azure Monitor, talvez seja necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador se ele tiver atingido o estágio de aposentadoria em seu ciclo de vida. Você pode facilmente gerenciar essas tarefas de manutenção rotineiras manualmente ou por meio da automação, o que reduz o erro operacional e as despesas.

## <a name="upgrading-agent"></a>Atualizando agente

O agente de Log Analytics para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo do cenário de implantação e do ambiente em que a VM está sendo executada. Os métodos a seguir podem ser usados para atualizar o agente.

| Ambiente | Método de instalação | Método de atualização |
|--------|----------|-------------|
| Azure VM | Extensão de VM do agente de Log Analytics para Windows/Linux | O Agent é atualizado automaticamente por padrão, a menos que você tenha configurado seu modelo de Azure Resource Manager para recusar definindo a propriedade *autoUpgradeMinorVersion* como **false**. |
| Imagens personalizadas de VM do Azure | Instalação manual do agente do Log Analytics para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux.|
| VMs não Azure | Instalação manual do agente do Log Analytics para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux. |

### <a name="upgrade-windows-agent"></a>Atualizar o agente do Windows 

Para atualizar o agente em uma VM do Windows para a versão mais recente não instalada usando a extensão de VM log Analytics, você pode executar no prompt de comando, script ou outra solução de automação ou usando a\<instalação\>MMASetup-Platform. msi Para.  

Você pode baixar a versão mais recente do agente do Windows do seu espaço de trabalho Log Analytics, executando as etapas a seguir.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.

3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho.

4. No espaço de trabalho Log Analytics, selecione **Configurações avançadas**, selecione **fontes conectadas**e, por fim, **Windows Servers**.

5. Na página **servidores Windows** , selecione a versão de **Download** apropriada do agente do Windows para download, dependendo da arquitetura do processador do sistema operacional Windows.

>[!NOTE]
>Durante a atualização do agente de Log Analytics para Windows, ele não oferece suporte à configuração ou reconfiguração de um espaço de trabalho para relatar. Para configurar o agente, você precisa seguir um dos métodos com suporte listados em [adicionando ou removendo um espaço de trabalho](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o assistente de instalação

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **MMASetup-\<Platform\>. exe** para iniciar o assistente de instalação.

3. Na primeira página do assistente de instalação, clique em **Avançar**.

4. Na caixa de diálogo **Microsoft Monitoring Agent configuração** , clique em **concordo** para aceitar o contrato de licença.

5. Na caixa de diálogo **Microsoft Monitoring Agent instalação** , clique em **Atualizar**. A página de status exibe o progresso da atualização.

6. Quando a **configuração de Microsoft Monitoring Agent foi concluída com êxito.** aparecerá, clique em **concluir**.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para extrair os arquivos de instalação do agente, em uma execução `MMASetup-<platform>.exe /c` de prompt de comandos com privilégios elevados, será solicitado que você forneça o caminho para o qual extrair os arquivos. Como alternativa, você pode especificar o caminho passando os argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Execute o comando a seguir, em que D:\ é o local para o arquivo de log de atualização.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Atualizar agente do Linux 

Há suporte para a atualização de versões anteriores (> 1.0.0-47). A execução da instalação com `--upgrade` o comando atualizará todos os componentes do agente para a versão mais recente.

Execute o comando a seguir para atualizar o agente.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Adicionando ou removendo um espaço de trabalho

### <a name="windows-agent"></a>Agente do Windows
As etapas nesta seção são necessárias quando você deseja não apenas reconfigurar o agente do Windows para reportar para um espaço de trabalho diferente ou remover um espaço de trabalho de sua configuração, mas também quando você deseja configurar o agente para reportar para mais de um espaço de trabalho (normalmente chamado de hospedagem múltipla). A configuração do agente do Windows para relatar para vários espaços de trabalho só pode ser executada após a configuração inicial do agente e o uso dos métodos descritos abaixo.    

#### <a name="update-settings-from-control-panel"></a>Atualizar configurações no painel de controle

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controlo**.

3. Selecione **Microsoft Monitoring Agent** e, em seguida, clique na guia **log Analytics do Azure** .

4. Se estiver removendo um espaço de trabalho, selecione-o e clique em **remover**. Repita essa etapa para qualquer outro espaço de trabalho para o qual você deseja que o agente pare de relatar.

5. Se estiver adicionando um espaço de trabalho, clique em **Adicionar** e, na caixa de diálogo **Adicionar um log Analytics espaço** de trabalho, Cole a ID do espaço de trabalho e a chave do espaço de trabalho (chave primária). Se o computador deve se reportar a um espaço de trabalho Log Analytics na nuvem do Azure governamental, selecione Azure EUA governamental na lista suspensa nuvem do Azure.

6. Clique em **OK** para guardar as alterações.

#### <a name="remove-a-workspace-using-powershell"></a>Remover um espaço de trabalho usando o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Adicionar um espaço de trabalho no Azure comercial usando o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Adicionar um espaço de trabalho no Azure para o governo dos EUA usando o PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se você usou a linha de comando ou o script anteriormente para instalar ou configurar o agente `EnableAzureOperationalInsights` , o foi `AddCloudWorkspace` substituído `RemoveCloudWorkspace`por e.
>

### <a name="linux-agent"></a>Agente do Linux
As etapas a seguir demonstram como reconfigurar o agente do Linux se você decidir registrá-lo em um espaço de trabalho diferente ou remover um espaço de trabalho de sua configuração.

1. Para verificar se ele está registrado em um espaço de trabalho, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Ele deve retornar um status semelhante ao exemplo a seguir:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    É importante que o status também mostre que o agente está em execução, caso contrário, as etapas a seguir para reconfigurar o agente não serão concluídas com êxito.

2. Se já estiver registrado com um espaço de trabalho, remova o espaço de trabalho registrado executando o comando a seguir. Caso contrário, se não estiver registrado, vá para a próxima etapa.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Para registrar com um espaço de trabalho diferente, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Para verificar se as alterações entraram em vigor, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Ele deve retornar um status semelhante ao exemplo a seguir:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

O serviço Agent não precisa ser reiniciado para que as alterações entrem em vigor.

## <a name="update-proxy-settings"></a>Atualizar configurações de proxy
Para configurar o agente para se comunicar com o serviço por meio de um servidor proxy ou [log Analytics gateway](gateway.md) após a implantação, use um dos métodos a seguir para concluir essa tarefa.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-using-control-panel"></a>Atualizar configurações usando o painel de controle

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controlo**.

3. Selecione **Microsoft Monitoring Agent** e, em seguida, clique na guia **configurações de proxy** .

4. Clique em **Use a proxy server** (Utilizar um servidor proxy) e indique o URL e o número de porta do servidor proxy ou do gateway. Se o seu servidor proxy ou o Log Analytics precisarem de autenticação, escreva o nome de utilizador e a palavra-passe para autenticar e clique em **OK**.

#### <a name="update-settings-using-powershell"></a>Atualizar configurações usando o PowerShell

Copie o código do PowerShell de exemplo a seguir, atualize-o com informações específicas para seu ambiente e salve-o com uma extensão de nome de arquivo PS1. Execute o script em cada computador que se conecta diretamente ao espaço de trabalho do Log Analytics no Azure Monitor.

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
Execute as etapas a seguir se os computadores Linux precisarem se comunicar por meio de um servidor proxy ou Log Analytics gateway. O valor de configuração do proxy tem a seguinte sintaxe `[protocol://][user:password@]proxyhost[:port]`. A propriedade *proxyhost* aceita um nome de domínio completamente qualificado ou o endereço IP do servidor proxy.

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
Use um dos procedimentos a seguir para desinstalar o agente do Windows ou Linux usando a linha de comando ou o assistente de instalação.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar do painel de controle
1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. No **painel de controle**, clique em **programas e recursos**.

3. Em **programas e recursos**, clique em **Microsoft Monitoring Agent**, em **desinstalar**e em **Sim**.

>[!NOTE]
>O assistente para instalação do agente também pode ser executado clicando duas vezes em **MMASetup\>-\<Platform. exe**, que está disponível para download em um espaço de trabalho no portal do Azure.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar da linha de comando
O arquivo baixado para o agente é um pacote de instalação independente criado com o IExpress. O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisa ser extraído para ser desinstalado corretamente usando a linha de comando mostrada no exemplo a seguir.

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para extrair os arquivos de instalação do agente, em uma execução `extract MMASetup-<platform>.exe` de prompt de comandos com privilégios elevados, será solicitado que você forneça o caminho para o qual extrair os arquivos. Como alternativa, você pode especificar o caminho passando os argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Para obter mais informações sobre as opções de linha de comando com suporte pelo IExpress, consulte [Opções de linha de comando para o IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e, em seguida, atualize o exemplo para atender às suas necessidades.

3. No prompt, digite `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Agente do Linux
Para remover o agente, execute o seguinte comando no computador Linux. O argumento *--purge* remove por completo o agente e a respetiva configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurar o agente para relatar a um grupo de gerenciamento de Operations Manager

### <a name="windows-agent"></a>Agente do Windows
Execute as etapas a seguir para configurar o agente de Log Analytics do Windows para reportar a um grupo de gerenciamento de System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Abra o **Painel de Controlo**.

3. Clique em **Microsoft Monitoring Agent** e, em seguida, clique na guia **Operations Manager** .

4. Se seus servidores de Operations Manager tiverem integração com o Active Directory, clique em **atualizar automaticamente as atribuições do grupo de gerenciamento de AD DS**.

5. Clique em **Adicionar** para abrir a caixa de diálogo **Adicionar um grupo de gerenciamento** .

6. No campo **nome do grupo de gerenciamento** , digite o nome do grupo de gerenciamento.

7. No campo **servidor de gerenciamento primário** , digite o nome do computador do servidor de gerenciamento primário.

8. No campo **porta do servidor de gerenciamento** , digite o número da porta TCP.

9. Em **conta de ação do agente**, escolha a conta sistema local ou uma conta de domínio local.

10. Clique em **OK** para fechar a caixa de diálogo **Adicionar um grupo de gerenciamento** e, em seguida, clique em **OK** para fechar a caixa de diálogo **Propriedades do Microsoft Monitoring Agent** .

### <a name="linux-agent"></a>Agente do Linux
Execute as etapas a seguir para configurar o agente de Log Analytics para Linux para relatar a um grupo de gerenciamento de System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Editar o arquivo`/etc/opt/omi/conf/omiserver.conf`

2. Verifique se a linha que começa `httpsport=` com define a porta 1270. Como:`httpsport=1270`

3. Reinicie o servidor OMI:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Passos seguintes

- Examine [solução de problemas do agente do Linux](agent-linux-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente do Linux.

- Examine [a solução de problemas do agente do Windows](agent-windows-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente do Windows.
