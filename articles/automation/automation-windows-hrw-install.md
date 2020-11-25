---
title: Implementar um trabalhador de runbook híbrido do Windows na Azure Automation
description: Este artigo diz como implementar um Trabalhador De Runbook Híbrido que pode usar para executar livros em máquinas baseadas no Windows no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: 31bb0fac302a81c2523a2d2855ca1930cb266ac4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95906624"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementar um trabalhador de runbook híbrido windows

Pode utilizar a funcionalidade de Trabalhador de Runbook Híbrido do utilizador da Azure Automation para executar livros de execução diretamente na máquina Azure ou não-Azure, incluindo servidores registados com [servidores ativados Azure Arc](../azure-arc/servers/overview.md). A partir da máquina ou servidor que está hospedando o papel, você pode executar runbooks diretamente e contra recursos no ambiente para gerir esses recursos locais.

A Azure Automation armazena e gere os runbooks e depois entrega-os a uma ou mais máquinas designadas. Este artigo descreve como implementar um utilizador Hybrid Runbook Worker numa máquina Windows, como remover o trabalhador e como remover um grupo híbrido de Runbook Worker.

Depois de implementar com sucesso um trabalhador de runbook, [reveja os runbooks de runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter o seguinte.

### <a name="a-log-analytics-workspace"></a>Um espaço de trabalho Log Analytics

O papel de Trabalhador de Runbook Híbrido depende de um espaço de trabalho Azure Monitor Log Analytics para instalar e configurar o papel. Pode criá-lo através [do Azure Resource Manager,](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)através [do PowerShell,](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)ou no [portal Azure](../azure-monitor/learn/quick-create-workspace.md).

Se não tiver um espaço de trabalho do Azure Monitor Log Analytics, reveja a orientação de [design do Registo do Monitor Azure](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="log-analytics-agent"></a>Agente do Log Analytics

A função De Trabalhador de Runbook Híbrido requer o [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) para o sistema operativo Windows suportado. Para servidores ou máquinas hospedadas fora do Azure, pode instalar o agente Log Analytics utilizando [servidores ativados pelo Azure Arc](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows suportado

A funcionalidade Hybrid Runbook Worker suporta os seguintes sistemas operativos:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (incluindo multi-sessão) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um sistema Windows e para o utilizador Hybrid Runbook Worker são:

* Windows PowerShell 5.1 ([descarregue WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core não é suportado.
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porto 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter requisitos de networking para o Trabalhador de Runbook Híbrido, consulte [configurar a sua rede.](automation-hybrid-runbook-worker.md#network-planning)

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Adicionar uma máquina a um grupo híbrido de trabalhadores de runbook

Pode adicionar a máquina de trabalhadores a um grupo híbrido de trabalhadores numa das suas contas de Automação. Para as máquinas que hospedam o sistema Hybrid Runbook trabalhador gerido pela Update Management, podem ser adicionadas a um grupo híbrido de trabalhador runbook. Mas deve utilizar a mesma conta de Automação tanto para a Gestão de Atualização como para a associação híbrida do grupo de trabalhadores runbook.

>[!NOTE]
>A Azure Automation [Update Management](update-management/update-mgmt-overview.md) instala automaticamente o sistema Hybrid Runbook Worker numa máquina Azure ou não-Azure que está ativada para a Gestão de Atualização. No entanto, este trabalhador não está registado em nenhum grupo híbrido de trabalhadores da runbook na sua conta de Automação. Para executar os seus livros de execução nessas máquinas, precisa adicioná-los a um grupo híbrido de runbook worker. Siga o passo 6 sob a secção [A colocação manual](#manual-deployment) para adicioná-lo a um grupo.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Habilitar para gestão com Configuração do Estado da Automação Azure

Para obter informações sobre a ativação de máquinas para gestão com a Configuração do Estado da Automação Azure, consulte [Ativar máquinas para gestão através da Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

> [!NOTE]
> Para gerir a configuração das máquinas que suportam a função de Trabalhador de Runbook Híbrido com Configuração de Estado Desejada (DSC), deve adicionar as máquinas como nós DSC.

## <a name="installation-options"></a>Opções de instalação

Para instalar e configurar um Trabalhador de Runbook Híbrido do utilizador do Windows, pode utilizar um dos seguintes métodos.

* Utilize um script PowerShell fornecido para [automatizar](#automated-deployment) completamente o processo de configuração de uma ou mais máquinas Windows. Este é o método recomendado para máquinas no seu datacenter ou outro ambiente em nuvem.
* Importe manualmente a solução Automation, instale o agente Log Analytics para windows e configuure o papel do trabalhador na máquina.

## <a name="automated-deployment"></a>Implementação automatizada

O método de implementação automatizado utiliza oNew-OnPremiseHybridWorker.ps1 **de** script PowerShell para automatizar e configurar o papel de Trabalhador runbook híbrido do Windows. Executa o seguinte:

* Instala os módulos necessários
* Assina com a sua conta Azure
* Verifica a existência de grupo de recursos especificados e conta de Automação
* Cria referências a atributos de conta de Automação
* Cria um espaço de trabalho Azure Monitor Log Analytics se não for especificado
* Ativar a solução Azure Automation no espaço de trabalho
* Descarregue e instale o agente Log Analytics para windows
* Registe a máquina como Trabalhador de Runbook Híbrido

Execute os seguintes passos para instalar a função na sua máquina Windows utilizando o script.

1. Descarregue **o** New-OnPremiseHybridWorker.ps1script da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Depois de ter descarregado o script, copie-o ou execute-o na máquina alvo. O **New-OnPremiseHybridWorker.ps1** script utiliza os seguintes parâmetros durante a execução.

    | Parâmetro | Estado | Descrição |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos associado à sua conta de Automação. |
    | `AutomationAccountName` | Obrigatório | O nome da sua conta de Automação.
    | `Credential` | Opcional | As credenciais a utilizar ao iniciar sessão no ambiente Azure. |
    | `HybridGroupName` | Obrigatório | O nome de um grupo híbrido de trabalho de runbook que você especifica como um alvo para os runbooks que suportam este cenário. |
    | `OMSResourceGroupName` | Opcional | O nome do grupo de recursos para o espaço de trabalho Log Analytics. Se este grupo de recursos não for especificado, o valor de `AAResourceGroupName` é utilizado. |
    | `SubscriptionID` | Obrigatório | O identificador da subscrição Azure associado à sua conta Automation. |
    | `TenantID` | Opcional | O identificador da organização de inquilinos associado à sua conta de Automação. |
    | `WorkspaceName` | Opcional | O nome do espaço de trabalho Log Analytics. Se não tiver um espaço de trabalho log analytics, o script cria e configura um. |

2. Abra uma ômada ômada 64 bits de comando PowerShell.

3. A partir do pedido de comando PowerShell, navegue para a pasta que contém o script que descarregou. Alterar os valores para os `AutomationAccountName` parâmetros, , , , , e `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Então, executar o guião.

    É-lhe pedido que autentica com o Azure depois de publicar o guião. Tem de iniciar scontabilidade com uma conta que é membro da **função de Administrador de Subscrição** e coadministradora da subscrição.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. É-lhe pedido que concorde em instalar o NuGet e autenticar com as suas credenciais Azure. Se não tiver a versão mais recente do NuGet, pode descarregá-la a partir das [versões de distribuição disponíveis do NuGet.](https://www.nuget.org/downloads)

5. Verifique a implementação após o envio do script. A partir da página **Hybrid Runbook Worker Groups** na sua conta Automation, no separador de grupo de **trabalhadores de runbook híbridos do utilizador,** mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página, a partir do menu à esquerda, escolha **Trabalhadores Híbridos.** Na página **"Trabalhadores Híbridos",** pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implementação manual

Para instalar e configurar um Trabalhador de Runbook Híbrido do Windows, execute os seguintes passos.

1. Ativar a solução Azure Automation no seu espaço de trabalho Log Analytics executando o seguinte comando numa pressão de comando PowerShell elevada ou em Cloud Shell no [portal Azure](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Insaque o agente Log Analytics na máquina alvo.

    * Para os VMs Azure, instale o agente Log Analytics para Windows utilizando a [extensão da máquina virtual para](../virtual-machines/extensions/oms-windows.md)o Windows . A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho log analytics existente. Pode utilizar um modelo de Gestor de Recursos Azure, PowerShell ou Azure Policy para atribuir o agente Deploy Log Analytics para a política incorporada [ *do Linux* ou *do Windows* VMs.](../governance/policy/samples/built-in-policies.md#monitoring) Uma vez instalado o agente, a máquina pode ser adicionada a um grupo híbrido de runbook na sua conta Automation.
    
    * Para máquinas não-Azure, pode instalar o agente Log Analytics utilizando [servidores ativados pelo Arco Azure](../azure-arc/servers/overview.md). Os servidores ativados pelo Arco suportam a implementação do agente Log Analytics utilizando os seguintes métodos:
    
        - Utilizando o quadro de extensões VM.
        
            Esta funcionalidade nos servidores ativados do Azure Arc permite-lhe implementar a extensão VM do agente Log Analytics para um servidor windows e/ou Linux não-Azure. As extensões VM podem ser geridas utilizando os seguintes métodos nas suas máquinas híbridas ou servidores geridos por servidores ativados pelo Arc:
        
            - O [portal Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - O [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Modelos de gestor de recursos Azure](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Usando a política de Azure.
        
            Utilizando esta abordagem, utiliza o agente Azure Policy [Deploy Log Analytics para máquinas Linux ou Windows Azure Arc incorporadas](../governance/policy/samples/built-in-policies.md#monitoring) para auditar se o servidor ativado pelo Arco tiver o agente Log Analytics instalado. Se o agente não estiver instalado, implanta-o automaticamente utilizando uma tarefa de reparação. Em alternativa, se planeia monitorizar as máquinas com o Azure Monitor para VMs, em vez disso, utilize o [Enable Azure Monitor para](../governance/policy/samples/built-in-initiatives.md#monitoring) a iniciativa VMs para instalar e configurar o agente Log Analytics.

    Recomendamos a instalação do agente Log Analytics para Windows ou Linux utilizando a Política Azure.

3. Verifique se o agente está a reportar-se ao espaço de trabalho

    O agente Log Analytics para Windows liga as máquinas a um espaço de trabalho Azure Monitor Log Analytics. Quando instala o agente na sua máquina e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador de Runbook Híbrido.

    Quando o agente tiver ligado com sucesso ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nos resultados da pesquisa, deverá consultar os registos de batimentos cardíacos da máquina, indicando que está ligado e a reportar ao serviço. Por defeito, cada agente encaminha um registo de batimentos cardíacos para o seu espaço de trabalho atribuído. Utilize os seguintes passos para completar a instalação e configuração do agente.

4. Confirme a versão do Trabalhador de Runbook Híbrido na máquina que hospeda o agente Log Analytics, navegue `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e note a subpasta da **versão.** Esta pasta aparecerá na máquina vários minutos depois de a solução estar ativada no espaço de trabalho.

5. Instale o ambiente do runbook e ligue-se à Azure Automation. Quando configura um agente para reportar a um espaço de trabalho log Analytics e importar a solução **Automation,** a solução empurra para baixo o `HybridRegistration` módulo PowerShell. Este módulo contém o `Add-HybridRunbookWorker` cmdlet. Utilize este cmdlet para instalar o ambiente do livro de recortes na máquina e registá-lo com a Azure Automation.

    Abra uma sessão PowerShell no modo administrador e execute os seguintes comandos para importar o módulo.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Executar o `Add-HybridRunbookWorker` cmdlet especificando os valores para os parâmetros `Url` , e `Key` `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Pode obter as informações necessárias para os parâmetros `Url` e a partir da página `Key` **Chaves** na sua conta Dem automação. Selecione **Teclas** na secção **de definições** de conta a partir do lado esquerdo da página.

    ![Gerir página Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Para o `Url` parâmetro, copie o valor para **URL**.

    * Para o `Key` parâmetro, copie o valor **da chave de acesso primário**.

    * Para o `GroupName` parâmetro, utilize o nome do grupo híbrido Runbook Worker. Se este grupo já existir na conta Automation, a máquina atual é-lhe adicionada. Se este grupo não existe, é adicionado.

    * Se necessário, desa estale o `Verbose` parâmetro para receber detalhes sobre a instalação.

7. Verifique a implantação após a conclusão do comando. A partir da página **Grupos de Trabalhadores de Runbook Híbridos** na sua conta Automation, no separador **de grupo de trabalhadores de runbook híbridos do utilizador,** mostra o grupo novo ou existente e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página, a partir do menu à esquerda, escolha **Trabalhadores Híbridos.** Na página **"Trabalhadores Híbridos",** pode ver cada membro do grupo listado.

## <a name="install-powershell-modules"></a>Instalar módulos PowerShell

Os runbooks podem utilizar qualquer uma das atividades e cmdlets definidos nos módulos instalados no seu ambiente Azure Automation. Uma vez que estes módulos não são automaticamente implantados em máquinas no local, deve instalá-los manualmente. A exceção é o módulo Azure. Este módulo é instalado por predefinição e fornece acesso a cmdlets para todos os serviços e atividades da Azure Automation.

Como o principal objetivo do Trabalhador de Runbook Híbrido é gerir os recursos locais, é provável que seja necessário instalar os módulos que suportam estes recursos, nomeadamente o `PowerShellGet` módulo. Para obter informações sobre a instalação de módulos Windows PowerShell, consulte [o Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar num local referenciado pela `PSModulePath` variável ambiental para que o trabalhador híbrido possa importar automaticamente. Para obter mais informações, consulte [instalar módulos em PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remova o Trabalhador de Runbook Híbrido

1. No portal Azure, aceda à sua conta de Automação.

2. Em **Definições de Conta**, selecione **Teclas** e note os valores para **URL** e **Tecla de acesso primário**.

3. Abra uma sessão PowerShell no modo Administrador e execute o seguinte comando com o seu URL e valores de chave de acesso primário. Utilize o `Verbose` parâmetro para um registo detalhado do processo de remoção. Para remover máquinas velhas do seu grupo Operário Híbrido, utilize o `machineName` parâmetro opcional.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido de runbook worker, primeiro precisa remover o Trabalhador de Runbook Híbrido de todas as máquinas que são membros do grupo. Em seguida, utilize os seguintes passos para remover o grupo:

1. Abra a conta Automation no portal Azure.

2. Selecione **grupos de trabalhadores híbridos** em **Automação de Processos.** Selecione o grupo que pretende eliminar. A página de propriedades para este grupo aparece.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Delete**. Uma mensagem pede-lhe que confirme esta ação. Selecione **Sim** se tiver a certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este processo pode demorar vários segundos a terminar. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem, consulte [runbooks Runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md).

* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas.](troubleshoot/hybrid-runbook-worker.md#general)
