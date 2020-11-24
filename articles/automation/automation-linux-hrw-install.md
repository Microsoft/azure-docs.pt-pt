---
title: Implementar um Trabalhador de Runbook Híbrido Linux na Azure Automation
description: Este artigo diz como instalar um Azure Automation Hybrid Runbook Worker para executar livros em máquinas baseadas em Linux no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: 07c04274829530942bab0e3267200f6524c4ddc7
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95793411"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implementar um Trabalhador de Runbook Híbrido Linux

Pode utilizar a funcionalidade de Trabalhador de Runbook Híbrido do utilizador da Azure Automation para executar livros de execução diretamente na máquina Azure ou não-Azure, incluindo servidores registados com [servidores ativados Azure Arc](../azure-arc/servers/overview.md). A partir da máquina ou servidor que está hospedando o papel, você pode executar runbooks diretamente e contra recursos no ambiente para gerir esses recursos locais.

O Trabalhador de Runbook Híbrido Linux executa runbooks como um utilizador especial que pode ser elevado para executar comandos que precisam de elevação. A Azure Automation armazena e gere os runbooks e depois entrega-os a uma ou mais máquinas designadas. Este artigo descreve como instalar o Trabalhador de Runbook Híbrido numa máquina Linux, como remover o trabalhador e como remover um grupo híbrido de runbook worker.

Depois de implementar com sucesso um trabalhador de runbook, [reveja os runbooks de runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter o seguinte.

### <a name="a-log-analytics-workspace"></a>Um espaço de trabalho Log Analytics

O papel de Trabalhador de Runbook Híbrido depende de um espaço de trabalho Azure Monitor Log Analytics para instalar e configurar o papel. Pode criá-lo através [do Azure Resource Manager,](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)através [do PowerShell,](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)ou no [portal Azure](../azure-monitor/learn/quick-create-workspace.md).

Se não tiver um espaço de trabalho do Azure Monitor Log Analytics, reveja a orientação de [design do Registo do Monitor Azure](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="log-analytics-agent"></a>Agente do Log Analytics

O papel de Trabalhador de Runbook Híbrido requer o [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) para o sistema operativo Linux suportado. Para servidores ou máquinas hospedadas fora do Azure, pode instalar o agente Log Analytics utilizando [servidores ativados pelo Azure Arc](../azure-arc/servers/overview.md).

>[!NOTE]
>Depois de instalar o agente Log Analytics para o Linux, não deverá alterar as permissões da pasta ou da `sudoers.d` sua propriedade. A permissão de sudo é necessária para a conta **de nxautomation,** que é o contexto de utilizador que o Trabalhador de Runbook Híbrido executa. As permissões não devem ser removidas. Restringir isto a determinadas pastas ou comandos pode resultar numa mudança de rutura.
>

### <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

A funcionalidade Hybrid Runbook Worker suporta as seguintes distribuições. Todos os sistemas operativos são assumidos como x64. x86 não é suportado para qualquer sistema operativo.

* Amazon Linux 2012.09 a 2015.09
* CentOS Linux 5, 6 e 7
* Oracle Linux 5, 6 e 7
* Red Hat Enterprise Linux Server 5, 6 e 7
* Debian GNU/Linux 6, 7 e 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um sistema Linux e para o utilizador Hybrid Runbook Worker são:

* Dois núcleos
* 4 GB de RAM
* Porto 443 (saída)

| **Pacote obrigatório** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 1.0 (TLS 1.1 e TLS 1.2 são suportados)|
|Curl | cURL cliente web | 7.15.5|
|Pitão-ctypes | Python 2.x é necessário |
|PAM | Módulos de Autenticação Incorporável|
| **Pacote opcional** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar os livros powerShell, o PowerShell Core precisa de ser instalado. Consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Adicionar uma máquina a um grupo híbrido de trabalhadores de runbook

Pode adicionar a máquina de trabalhadores a um grupo híbrido de trabalhadores numa das suas contas de Automação. Para as máquinas que hospedam o sistema Hybrid Runbook trabalhador gerido pela Update Management, podem ser adicionadas a um grupo híbrido de trabalhador runbook. Mas deve utilizar a mesma conta de Automação tanto para a Gestão de Atualização como para a associação híbrida do grupo de trabalhadores runbook.

>[!NOTE]
>A Azure Automation [Update Management](update-management/update-mgmt-overview.md) instala automaticamente o sistema Hybrid Runbook Worker numa máquina Azure ou não-Azure que está ativada para a Gestão de Atualização. No entanto, este trabalhador não está registado em nenhum grupo híbrido de trabalhadores da runbook na sua conta de Automação. Para executar os seus livros de execução nessas máquinas, precisa adicioná-los a um grupo híbrido de runbook worker. Siga o passo 4 sob a secção [Instale um Trabalhador de Runbook Híbrido Linux](#install-a-linux-hybrid-runbook-worker) para adicioná-lo a um grupo.

## <a name="supported-linux-hardening"></a>Endurecimento de Linux apoiado

Os seguintes ainda não são apoiados:

* CIS

## <a name="supported-runbook-types"></a>Tipos de runbook suportados

Os Trabalhadores de Runbook Híbridos Linux suportam um conjunto limitado de tipos de runbook na Azure Automation, e são descritos na tabela seguinte.

|Tipo de livro de execução | Suportado |
|-------------|-----------|
|Python 2 |Sim |
|PowerShell |Sim<sup>1</sup> |
|Fluxo de Trabalho do PowerShell |Não |
|Gráficos |Não |
|Fluxo de trabalho gráfico powershell |Não |

<sup>1</sup> Os livros powerShell requerem que o PowerShell Core seja instalado na máquina Linux. Consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo.

### <a name="network-configuration"></a>Configuração de rede

Para obter requisitos de networking para o Trabalhador de Runbook Híbrido, consulte [configurar a sua rede.](automation-hybrid-runbook-worker.md#network-planning)

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instale um Trabalhador de Runbook Híbrido Linux

Para instalar e configurar um Trabalhador de Runbook Híbrido Linux, execute os seguintes passos.

1. Ativar a solução Azure Automation no seu espaço de trabalho Log Analytics executando o seguinte comando numa pressão de comando PowerShell elevada ou em Cloud Shell no [portal Azure:](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Insaque o agente Log Analytics na máquina alvo.

    * Para Azure VMs, instale o agente Log Analytics para Linux utilizando a [extensão da máquina virtual para Linux](../virtual-machines/extensions/oms-linux.md). A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho log analytics existente. Pode utilizar um modelo de Gestor de Recursos Azure, o Azure CLI ou Azure Policy para atribuir o agente Deploy Log Analytics para a política incorporada [ *do Linux* ou *Do Windows* VMs.](../governance/policy/samples/built-in-policies.md#monitoring) Uma vez instalado o agente, a máquina pode ser adicionada a um grupo híbrido de runbook na sua conta Automation.

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

    > [!NOTE]
    > Para gerir a configuração das máquinas que suportam a função de Trabalhador de Runbook Híbrido com Configuração de Estado Desejada (DSC), deve adicionar as máquinas como nós DSC.

    > [!NOTE]
    > A [conta de nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) com as permissões de sudo correspondentes deve estar presente durante a instalação do Trabalhador Híbrido Linux. Se tentar instalar o trabalhador e a conta não estiver presente ou não tiver as permissões adequadas, a instalação falha.

3. Verifique se o agente está a reportar-se ao espaço de trabalho.

    O agente Log Analytics do Linux liga as máquinas a um espaço de trabalho Azure Monitor Log Analytics. Quando instala o agente na sua máquina e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador de Runbook Híbrido.

    Quando o agente tiver ligado com sucesso ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nos resultados da pesquisa, deverá consultar os registos de batimentos cardíacos da máquina, indicando que está ligado e a reportar ao serviço. Por defeito, cada agente encaminha um registo de batimentos cardíacos para o seu espaço de trabalho atribuído.

4. Executar o seguinte comando para adicionar a máquina a um grupo híbrido de trabalhadores runbook, especificando os valores para os parâmetros `-w` , `-k` , e `-g` `-e` .

    Pode obter as informações necessárias para os parâmetros `-k` e a partir da página `-e` **Chaves** na sua conta Dem automação. Selecione **Teclas** na secção **de definições** de conta a partir do lado esquerdo da página.

    ![Gerir página Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Para o `-e` parâmetro, copie o valor para **URL**.

    * Para o `-k` parâmetro, copie o valor **da chave de acesso primário**.

    * Para o `-g` parâmetro, especifique o nome do grupo híbrido Runbook Worker que o novo trabalhador do Linux Hybrid Runbook deve aderir. Se este grupo já existir na conta Automation, a máquina atual é-lhe adicionada. Se este grupo não existe, é criado com esse nome.

    * Para o `-w` parâmetro, especifique o seu ID do espaço de trabalho Do Log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Verifique a implementação após a conclusão do script. A partir da página **Grupos de Trabalhadores de Runbook Híbridos** na sua conta Automation, no separador **de grupo de trabalhadores de runbook híbridos do utilizador,** mostra o grupo novo ou existente e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página, a partir do menu à esquerda, escolha **Trabalhadores Híbridos.** Na página **"Trabalhadores Híbridos",** pode ver cada membro do grupo listado.

    > [!NOTE]
    > Se estiver a utilizar a extensão da máquina virtual Log Analytics para o Linux para um Azure VM, recomendamos que a definição `autoUpgradeMinorVersion` `false` para versões de auto-actualização possa causar problemas com o Trabalhador de Runbook Híbrido. Para aprender a atualizar a extensão manualmente, consulte a [implementação do CLI Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Desativar a validação da assinatura

Por padrão, os Trabalhadores do Runbook Híbrido Linux requerem validação de assinaturas. Se fizeres um livro de corridas não assinado contra um trabalhador, vês um `Signature validation failed` erro. Para desligar a validação da assinatura, executar o seguinte comando. Substitua o segundo parâmetro pelo seu ID do espaço de trabalho Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Remova o Trabalhador de Runbook Híbrido

Pode utilizar o comando `ls /var/opt/microsoft/omsagent` do Trabalhador de Runbook Híbrido para obter o ID do espaço de trabalho. É criada uma pasta que é nomeada com o ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Este script não remove o agente Log Analytics para Linux da máquina. Apenas remove a funcionalidade e configuração da função De Trabalhador de Runbook Híbrido.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido de runbook worker de máquinas Linux, você usa os mesmos passos que para um grupo de trabalhadores híbridos Windows. Ver [Remover um grupo de trabalhadores híbridos](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem, consulte [runbooks Runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md).

* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas - Linux](troubleshoot/hybrid-runbook-worker.md#linux).
