---
title: Gestão do agente Azure Log Analytics
description: Este artigo descreve as diferentes tarefas de gestão que normalmente irá executar durante o ciclo de vida do Log Analytics Windows ou do agente Linux implantados numa máquina.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275103"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Managing and maintaining the Log Analytics agent for Windows and Linux (Gerir e manter o agente do Log Analytics para Windows e Linux)

Após a implementação inicial do Windows de Log Analytics ou do agente Linux no Monitor Azure, poderá ser necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador se tiver atingido a fase de reforma no seu ciclo de vida. Pode gerir facilmente estas tarefas de manutenção de rotina manualmente ou através da automatização, o que reduz tanto o erro operacional como as despesas.

## <a name="upgrading-agent"></a>Agente de upgrade

O agente Log Analytics para Windows e Linux pode ser atualizado para a versão mais recente manual mente ou automaticamente, dependendo do cenário de implementação e ambiente em que o VM está a funcionar. Os seguintes métodos podem ser utilizados para atualizar o agente.

| Ambiente | Método de Instalação | Método de atualização |
|--------|----------|-------------|
| VM do Azure | Extensão VM do agente Delog Analytics para Windows/Linux | O agente é automaticamente atualizado por padrão, a menos que tenha configurado o seu modelo de Gestor de Recursos Azure para optar por não o fazer, definindo a propriedade *autoUpgradeMinorVersion* para **falsa**. |
| Imagens VM Vip personalizadas | Instalação manual do agente Log Analytics para Windows/Linux | A atualização dos VMs para a versão mais recente do agente tem de ser executada a partir da linha de comando que executa o pacote de instalação do Windows ou o pacote de scripts de auto-extracção e instalação do Script Linux.|
| VMs não-Azure | Instalação manual do agente Log Analytics para Windows/Linux | A atualização dos VMs para a versão mais recente do agente tem de ser executada a partir da linha de comando que executa o pacote de instalação do Windows ou o pacote de scripts de auto-extracção e instalação do Script Linux. |

### <a name="upgrade-windows-agent"></a>Atualizar o agente Windows 

Para atualizar o agente num VM do Windows para a versão mais recente não instalada utilizando a extensão VM do Log Analytics,\<\>ou executa a partir do Comando Solicitação, script ou outra solução de automação, ou utilizando o Assistente de Configuração da plataforma MMASetup .msi.  

Pode descarregar a versão mais recente do agente Windows a partir do seu espaço de trabalho Log Analytics, executando os seguintes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

3. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho.

4. No seu espaço de trabalho Log Analytics, selecione **definições avançadas**, em seguida, selecione **Fontes Conectadas**, e, **finalmente, Windows Servers**.

5. A partir da página **do Windows Servers,** selecione a versão de **Download Windows Agent** apropriada para descarregar dependendo da arquitetura do processador do sistema operativo Windows.

>[!NOTE]
>Durante a atualização do agente Log Analytics para windows, não suporta configurar ou reconfigurar um espaço de trabalho a que reportar. Para configurar o agente, é necessário seguir um dos métodos suportados listados em [Adicionar ou remover um espaço](#adding-or-removing-a-workspace)de trabalho .
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o Assistente de Configuração

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute **\<mmASetup-\>plataforma .exe** para iniciar o Assistente de Configuração.

3. Na primeira página do Assistente de Configuração, clique em **Seguinte**.

4. Na caixa de diálogo de configuração do **agente de monitorização** da Microsoft, clique em **aceitar** o contrato de licença.

5. Na caixa de diálogo **Configuração do Microsoft Monitoring Agent**, clique em **Atualizar**. A página de estado apresenta o progresso da atualização.

6. Quando a configuração do **Microsoft Monitoring Agent foi concluída com sucesso.** página aparece, clique **Em Terminar**.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Para extrair os ficheiros de instalação `MMASetup-<platform>.exe /c` do agente, a partir de uma execução rápida de comando elevado e irá levá-lo para o caminho para extrair ficheiros para. Em alternativa, pode especificar o caminho `MMASetup-<platform>.exe /c /t:<Full Path>`passando os argumentos.

3. Execute o seguinte comando, onde D:\ é a localização do ficheiro de registo de atualização.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Atualizar agente Linux 

É suportado o upgrade de versões anteriores (>1.0.0-47). A realização da `--upgrade` instalação com o comando irá atualizar todos os componentes do agente para a versão mais recente.

Executar o seguinte comando para atualizar o agente.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Adicionar ou remover um espaço de trabalho

### <a name="windows-agent"></a>Agente do Windows
Os passos nesta secção são necessários quando pretende não só reconfigurar o agente do Windows para reportar a um espaço de trabalho diferente ou remover um espaço de trabalho da sua configuração, mas também quando pretende configurar o agente para reportar a mais de um espaço de trabalho (vulgarmente referido como multi-homing). Configurar o agente do Windows para reportar a vários espaços de trabalho só pode ser realizado após a configuração inicial do agente e utilizando os métodos descritos abaixo.    

#### <a name="update-settings-from-control-panel"></a>Definições de atualização do Painel de Controlo

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. **Painel de Controlo**Aberto .

3. Selecione **Microsoft Monitoring Agent** e, em seguida, clique no separador **Azure Log Analytics.**

4. Se remover um espaço de trabalho, selecione-o e, em seguida, clique em **Remover**. Repita este passo para qualquer outro espaço de trabalho a que pretenda que o agente pare de reportar.

5. Se adicionar um espaço de trabalho, clique em **Adicionar** e na caixa de diálogo log **analytics workspace,** colar o ID do espaço de trabalho e a chave workspace (Chave Primária). Caso o computador deva reportar a uma área de trabalho do Log Analytics na cloud do Azure Government, selecione Azure US Government, na lista pendente Cloud do Azure.

6. Clique em **OK** para guardar as alterações.

#### <a name="remove-a-workspace-using-powershell"></a>Remova um espaço de trabalho usando powerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Adicione um espaço de trabalho no comercial Azure usando powerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Adicione um espaço de trabalho em Azure para governo dos EUA usando powerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se já utilizou a linha de comando ou script anteriormente `EnableAzureOperationalInsights` para `AddCloudWorkspace` instalar `RemoveCloudWorkspace`ou configurar o agente, foi substituído por e .
>

### <a name="linux-agent"></a>Agente Linux
Os seguintes passos demonstram como reconfigurar o agente Linux se decidir registá-lo com um espaço de trabalho diferente ou remover um espaço de trabalho da sua configuração.

1. Para verificar se está registado num espaço de trabalho, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Deve devolver um estatuto semelhante ao seguinte exemplo:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    É importante que o estado também mostre que o agente está em execução, caso contrário os seguintes passos para reconfigurar o agente não serão concluídos com sucesso.

2. Se já estiver registado num espaço de trabalho, remova o espaço de trabalho registado executando o seguinte comando. Caso contrário, se não estiver registado, proceda ao próximo passo.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Para se registar com um espaço de trabalho diferente, executar o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Para verificar se as suas alterações produziram efeito, execute o seguinte comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Deve devolver um estatuto semelhante ao seguinte exemplo:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

O serviço de agente não precisa de ser reiniciado para que as alterações entrem em vigor.

## <a name="update-proxy-settings"></a>Atualizar definições de proxy
Para configurar o agente para comunicar ao serviço através de um servidor proxy ou [gateway Log Analytics](gateway.md) após a implementação, utilize um dos seguintes métodos para completar esta tarefa.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-using-control-panel"></a>Definições de atualização usando painel de controlo

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. **Painel de Controlo**Aberto .

3. Selecione **O Agente de Monitorização** da Microsoft e, em seguida, clique no **separador Definições proxy.**

4. Clique em **Use a proxy server** (Utilizar um servidor proxy) e indique o URL e o número de porta do servidor proxy ou do gateway. Se o seu servidor proxy ou o Log Analytics precisarem de autenticação, escreva o nome de utilizador e a palavra-passe para autenticar e clique em **OK**.

#### <a name="update-settings-using-powershell"></a>Definições de atualização usando powerShell

Copie o seguinte código PowerShell da amostra, atualize-o com informações específicas para o seu ambiente e guarde-a com uma extensão de nome de ficheiro PS1. Executar o script em cada computador que se ligue diretamente ao espaço de trabalho do Log Analytics no Monitor Azure.

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

### <a name="linux-agent"></a>Agente Linux
Execute os seguintes passos se os seus computadores Linux precisarem de comunicar através de um servidor proxy ou gateway Log Analytics. O valor de configuração do proxy tem a seguinte sintaxe `[protocol://][user:password@]proxyhost[:port]`. A propriedade *proxyhost* aceita um nome de domínio completamente qualificado ou o endereço IP do servidor proxy.

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
Utilize um dos seguintes procedimentos para desinstalar o agente Windows ou Linux utilizando a linha de comando ou o assistente de configuração.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar do Painel de Controlo
1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. No **Painel de Controlo,** clique em **Programas e Funcionalidades.**

3. Em **Programas e Funcionalidades,** clique em **Microsoft Monitoring Agent,** clique em **Desinstalar**, e, em seguida, clique **em Sim**.

>[!NOTE]
>O Assistente de Configuração do Agente também pode ser executado por um duplo clique **MMASetup-\<plataforma\>.exe**, que está disponível para download a partir de um espaço de trabalho no portal Azure.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar a partir da linha de comando
O ficheiro descarregado para o agente é um pacote de instalação autossuficiente criado com o IExpress. O programa de configuração do agente e dos ficheiros de suporte estão contidos na embalagem e precisam de ser extraídos para desinstalar corretamente utilizando a linha de comando mostrada no exemplo seguinte.

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Para extrair os ficheiros de instalação `extract MMASetup-<platform>.exe` do agente, a partir de uma execução rápida de comando elevado e irá levá-lo para o caminho para extrair ficheiros para. Em alternativa, pode especificar o caminho `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`passando os argumentos. Para obter mais informações sobre os comutadores da linha de comando suportados pelo IExpress, consulte [os interruptores da linha de comando para o IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e, em seguida, atualize o exemplo de acordo com as suas necessidades.

3. No pedido, `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`escreva.

### <a name="linux-agent"></a>Agente Linux
Para remover o agente, execute o seguinte comando no computador Linux. O argumento *--purge* remove por completo o agente e a respetiva configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configure agente para reportar a um grupo de gestão de Gestor de Operações

### <a name="windows-agent"></a>Agente do Windows
Execute os seguintes passos para configurar o agente Log Analytics para o Windows apresentar-se a um grupo de gestão de Gestão de Operações do System Center.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. **Painel de Controlo**Aberto .

3. Clique no **Monitordo Agente microsoft** e, em seguida, clique no separador Gestor de **Operações.**

4. Se os seus servidores do Gestor de Operações tiverem integração com o Diretório Ativo, clique em **atualizar automaticamente as atribuições do grupo de gestão a partir de DS AD**.

5. Clique **em Adicionar** para abrir a caixa de diálogo Adicionar um Grupo de **Gestão.**

6. No campo **de nome do grupo de gestão,** digite o nome do seu grupo de gestão.

7. No campo do servidor de **gestão primária,** digite o nome do computador do servidor de gestão primária.

8. No campo de porta do **servidor de gestão,** digite o número da porta TCP.

9. Em conta de ação do **agente,** escolha a conta do Sistema Local ou uma conta de domínio local.

10. Clique **em OK** para fechar a caixa de diálogo Adicionar um Grupo de Gestão e, em **seguida,** clique em **OK** para fechar a caixa de diálogo do Agente de Monitorização da **Microsoft Properties.**

### <a name="linux-agent"></a>Agente Linux
Execute os seguintes passos para configurar o agente Log Analytics para o Linux apresentar-se a um grupo de gestão de gestão de operações do System Center.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Editar o ficheiro`/etc/opt/omi/conf/omiserver.conf`

2. Certifique-se de `httpsport=` que a linha que começa com define a porta 1270. Tais como:`httpsport=1270`

3. Reiniciar o servidor OMI:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Passos seguintes

- Reveja [o problema de resolução do agente Linux](agent-linux-troubleshoot.md) se encontrar problemas durante a instalação ou gestão do agente Linux.

- Reveja [o problema do agente Windows](agent-windows-troubleshoot.md) se encontrar problemas durante a instalação ou gestão do agente Windows.
