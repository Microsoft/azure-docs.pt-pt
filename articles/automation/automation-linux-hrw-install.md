---
title: Implementar um Trabalhador de Livro Híbrido Linux na Automação Azure
description: Este artigo diz como instalar um Trabalhador de Runbook Híbrido de Automação Azure para executar livros de execução em computadores baseados em Linux no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: a6cf348142d694a03da24f32793fc72325701931
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835228"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implante um trabalhador de runbook híbrido Linux

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar livros de execução diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. O Linux Hybrid Runbook Worker executa livros de execução como um utilizador especial que pode ser elevado para comandos de funcionamento que precisam de elevação. Os livros de execução são armazenados e geridos na Azure Automation e depois entregues a um ou mais computadores designados.

Este artigo descreve como instalar o Trabalhador do Livro Híbrido numa máquina Linux, como remover o trabalhador e como remover um grupo híbrido de trabalho de runbook.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

A função Hybrid Runbook Worker suporta as seguintes distribuições:

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="supported-runbook-types"></a>Tipos de livro de corridas suportados

Os trabalhadores do livro de corridas híbridos Linux não suportam todo o conjunto de tipos de livros de corridas na Automação Azure.

Os seguintes tipos de livro funcionam num Linux Hybrid Worker:

* Python 2
* PowerShell

  > [!NOTE]
  > Os livros de execução PowerShell exigem que o PowerShell Core seja instalado na máquina Linux. Consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo.

Os seguintes tipos de livros não funcionam num Linux Hybrid Worker:

* Fluxo de Trabalho do PowerShell
* Gráficos
* Fluxo de trabalho de PowerShell Graphical PowerShell

## <a name="deployment-requirements"></a>Requisitos de implementação

Os requisitos mínimos para um Trabalhador do Livro De Execução Híbrido Linux são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="package-requirements"></a>Requisitos do pacote

| **Pacote obrigatório** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas openssl | 1.0 (TLS 1.1 e TLS 1.2 são suportados)|
|Curl | cURL web cliente | 7.15.5|
|Python-ctypes | Python 2.x é necessário |
|PAM | Módulos de Autenticação Incorporável|
| **Pacote opcional** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar os livros de execução powerShell, o PowerShell precisa de ser instalado, consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instale um Trabalhador do Livro Híbrido Linux

Para instalar e configurar um Trabalhador do Livro de Execução Híbrido no seu computador Linux, siga um processo manual simples. Requer permitir que o Trabalhador do Livro Híbrido no seu espaço de trabalho Azure Log Analytics e, em seguida, executar um conjunto de comandos registe o computador como trabalhador e adicioná-lo a um grupo.

Antes de proceder, note o espaço de trabalho log Analytics a que a sua conta Deautomação está ligada. Note também a chave principal para a sua conta Deautomação. Pode encontrar tanto a partir do portal Azure selecionando a sua conta de Automação, selecionando **workspace** para o ID do espaço de trabalho e selecionando **Chaves** para a chave principal. Para obter informações sobre portas e endereços de que necessita para o Trabalhador do Livro híbrido, consulte [Configurar a sua rede](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> A [conta de nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) com as permissões sudo correspondentes deve estar presente durante a instalação do Linux Hybrid Worker. Se tentar instalar o trabalhador e a conta não estiver presente ou não tiver as permissões adequadas, a instalação falha.

1. Ativar o Trabalhador do Livro Híbrido em Azure utilizando um dos seguintes métodos:

   * Adicione o Trabalhador do Livro Híbrido à sua subscrição utilizando o procedimento nos [registos add Azure Monitor para o seu espaço](../log-analytics/log-analytics-add-solutions.md)de trabalho .
   * Execute o seguinte cmdlet:

        ```azurepowershell-interactive
         Set-AzOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Instale o agente Log Analytics para o Linux executando o seguinte comando. Substitua \< o WorkspaceID \> e o \< WorkspaceKey \> com os valores apropriados do seu espaço de trabalho.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Executar o seguinte comando, alterando os valores para os parâmetros *-w*, *-k*, *-g,* e *-e*. Para o parâmetro *-g,* substitua o valor pelo nome do grupo Hybrid Runbook Worker que o novo Trabalhador de Runbook Híbrido Linux deve aderir. Se o nome não existir na sua conta Automation, um novo grupo híbrido Runbook Worker é feito com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Após a conclusão do comando, a página dos Grupos de Trabalhadores Híbridos no portal Azure mostra o novo grupo e o número de membros. Se este é um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página grupo seletiva de grupos de trabalhadores híbridos e selecionar o azulejo **Híbrido Workers.** Na página dos Trabalhadores Híbridos, vê-se cada membro do grupo listado.

> [!NOTE]
> Se estiver a utilizar a extensão da máquina virtual Azure Monitor para o Linux para um VM Azure, recomendamos que se ajuste `autoUpgradeMinorVersion` a versões falsas, uma vez que versões de atualização automática podem causar problemas ao Hybrid Runbook Worker. Para aprender a atualizar a extensão manualmente, consulte a [implementação do Azure CLI](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Desligue a validação da assinatura

Por padrão, os trabalhadores do livro de corridas híbridos Linux exigem validação de assinaturas. Se publicares um livro de corridas não assinado contra um trabalhador, vês um `Signature validation failed` erro. Para desativar a validação da assinatura, execute o seguinte comando. Substitua o segundo parâmetro pelo id do espaço de trabalho Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Remova o Trabalhador do Livro Híbrido de um computador Linux no local

Pode utilizar o comando do Trabalhador do Livro híbrido para obter o ID do espaço de `ls /var/opt/microsoft/omsagent` trabalho. É criada uma pasta que é nomeada com o ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Este código não remove o agente Log Analytics para o Linux do computador. Apenas remove a funcionalidade e configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido de computadores Linux, utiliza os mesmos passos que para um grupo de trabalhadores híbridos windows. Ver [Remover um grupo de Trabalhadores Híbridos](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros de execução para automatizar processos no seu centro de dados no local ou em outro ambiente em nuvem, consulte [run run run book em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para aprender a resolver os seus Trabalhadores Híbridos, consulte problemas do Trabalhador do [Livro Híbrido de Troubleshoot - Linux](troubleshoot/hybrid-runbook-worker.md#linux).
