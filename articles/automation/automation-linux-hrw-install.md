---
title: Função de Trabalho de Runbook Híbrida do Linux da Automatização do Azure
description: Este artigo fornece informações sobre a instalação de um Trabalhador de Runbook Híbrido de Automação Azure para que possa executar livros de execução em computadores baseados em Linux no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2579748d9c68512e51fe46ec70084c30d06953bc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246296"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implante um trabalhador de runbook híbrido Linux

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar livros de execução diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. O Linux Hybrid Runbook Worker executa livros de execução como um utilizador especial que pode ser elevado para comandos de funcionamento que precisam de elevação. Os livros de execução são armazenados e geridos na Azure Automation e depois entregues a um ou mais computadores designados.

Este artigo descreve como instalar o Trabalhador do Livro Híbrido numa máquina Linux.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

A função Hybrid Runbook Worker suporta as seguintes distribuições:

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalação de um Trabalhador do Livro de Corridas Híbrido Linux

Para instalar e configurar um Trabalhador de Livro Híbrido no seu computador Linux, siga um processo simples para instalar e configurar manualmente a função. Requer permitir a solução **Automation Hybrid Worker** no seu espaço de trabalho Azure Log Analytics e, em seguida, executar um conjunto de comandos para registar o computador como trabalhador e adicioná-lo a um grupo.

Os requisitos mínimos para um Trabalhador do Livro De Execução Híbrido Linux são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="package-requirements"></a>Requisitos do pacote

| **Pacote obrigatório** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Abre-nis| Bibliotecas openssl | 1.0 (TLS 1.1 e TLS 1.2 são suportados|
|Caracol | cURL web cliente | 7.15.5|
|Python-ctypes | Python 2.x é necessário |
|PAM | Módulos de autenticação pluggable|
| **Pacote opcional** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar os livros de execução powerShell, o PowerShell precisa de ser instalado, consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo.  | 6.0.0 |

### <a name="installation"></a>Instalação

Antes de proceder, note o espaço de trabalho log Analytics a que a sua conta Deautomação está ligada. Note também a chave principal para a sua conta Deautomação. Pode encontrar tanto a partir do portal Azure selecionando a sua conta de Automação, selecionando **workspace** para o ID do espaço de trabalho e selecionando **Chaves** para a chave principal. Para obter informações sobre portas e endereços de que necessita para o Trabalhador do Livro híbrido, consulte [Configurar a sua rede](automation-hybrid-runbook-worker.md#network-planning).

1. Ativar a solução **Automation Hybrid Worker** em Azure utilizando um dos seguintes métodos:

   * Adicione a solução **Automation Hybrid Worker** à sua subscrição utilizando o procedimento no Add [Azure Monitor soluçãos de logs para o seu espaço de trabalho](../log-analytics/log-analytics-add-solutions.md).
   * Execute o seguinte cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Instale o agente Log Analytics para o Linux executando o seguinte comando. Substitua \<workspaceID\> e \<WorkspaceKey\> com os valores apropriados do seu espaço de trabalho.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Executar o seguinte comando, alterando os valores para os parâmetros *-w*, *-k*, *-g,* e *-e*. Para o parâmetro *-g,* substitua o valor pelo nome do grupo Hybrid Runbook Worker que o novo Trabalhador de Runbook Híbrido Linux deve aderir. Se o nome não existir na sua conta Automation, um novo grupo híbrido Runbook Worker é feito com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Após a conclusão do comando, a página dos **Grupos de Trabalhadores Híbridos** no portal Azure mostra o novo grupo e o número de membros. Se este é um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página grupo seletiva de grupos de **trabalhadores híbridos** e selecionar o azulejo **Híbrido Workers.** Na página dos **Trabalhadores Híbridos,** vê-se cada membro do grupo listado.

> [!NOTE]
> Se estiver a utilizar a extensão da máquina virtual Azure Monitor para o Linux para um VM Azure, recomendamos que `autoUpgradeMinorVersion` falsas, uma vez que versões de atualização automática podem causar problemas ao Hybrid Runbook Worker. Para aprender a atualizar a extensão manualmente, consulte a [implementação do Azure CLI ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Desativação da validação de assinaturas

Por padrão, os trabalhadores do livro de corridas híbridos Linux exigem validação de assinaturas. Se publicar um livro de corridas não assinado contra um trabalhador, verá um erro que diz "Validação de assinatura falhou". Para desativar a validação da assinatura, execute o seguinte comando. Substitua o segundo parâmetro pelo id do espaço de trabalho de análise de registo.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipos de livro de corridas suportados

Os trabalhadores do livro de corridas híbridos Linux não suportam todo o conjunto de tipos de livros de corridas na Automação Azure.

Os seguintes tipos de livro funcionam num Linux Hybrid Worker:

* Python 2
* PowerShell

  > [!NOTE]
  > Os livros de execução PowerShell exigem que o PowerShell Core seja instalado na máquina Linux. Consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo.

Os seguintes tipos de livros não funcionam num Linux Hybrid Worker:

* Fluxo de trabalho do PowerShell
* Gráfico
* Fluxo de trabalho de PowerShell Graphical PowerShell

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros de execução para automatizar processos no seu centro de dados no local ou em outro ambiente em nuvem, consulte [run run run book em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os trabalhadores híbridos do livro de corridas, consulte [Remove Azure Automation Hybrid Runbook Workers](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para aprender a resolver os seus Trabalhadores Híbridos, consulte os trabalhadores do [livro híbrido Linux Hybrid Runbook](troubleshoot/hybrid-runbook-worker.md#linux)
