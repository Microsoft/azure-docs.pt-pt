---
title: Função de Trabalho de Runbook Híbrida do Linux da Automatização do Azure
description: Este artigo fornece informações sobre como instalar uma função de trabalho de Runbook de híbrida de automatização do Azure, para que possa executar runbooks em computadores baseados em Linux no seu local datacenter ou o ambiente na cloud.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3bdf642c37fe5f64b7d853da6a050ec8c2a7d44d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834664"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implementar um Runbook Worker híbrido do Linux

Pode utilizar a funcionalidade de trabalho de Runbook híbrida da automatização do Azure para executar runbooks diretamente no computador que aloja a função e relativamente aos recursos no ambiente para gerir esses recursos locais. O Runbook Worker do Linux híbrida executa runbooks, como um utilizador especial que pode ser elevando para executar comandos que precisam de privilégio. Runbooks são armazenados e gerenciados na automatização do Azure e, em seguida, entregue a um ou mais computadores designados.

Este artigo descreve como instalar a função de trabalho de Runbook híbrida numa máquina Linux.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

A funcionalidade de trabalho de Runbook híbrida suporta as distribuições seguintes:

* Amazon Linux 2012.09 para 2015.09 (x86/x64)
* Linux centOS 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS e 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalar uma função de trabalho do Runbook de híbrida do Linux

Para instalar e configurar uma função de trabalho de Runbook híbrida no seu computador Linux, siga um processo simples para instalar e configurar a função manualmente. Necessita de ativar a **função de trabalho de híbrida de automatização** solução na sua área de trabalho do Log Analytics do Azure e, em seguida, executar um conjunto de comandos para registar o computador como uma função de trabalho e adicioná-lo a um grupo.

Os requisitos mínimos para uma função de trabalho de Runbook do Linux híbrida são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="package-requirements"></a>Requisitos do pacote

| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca de C do GNU| 2.5-12 |
|OpenSSL| Bibliotecas de OpenSSL | 1.0 (TLS 1.1 e TLS 1.2 são suportados|
|Curl | cliente de web de cURL | 7.15.5|
|Ctypes de Python | |
|PAM | Módulos de autenticação conectável|
| **Pacote opcional** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar runbooks do PowerShell, PowerShell tem de ser instalado, veja [instalar o PowerShell Core no Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) para saber como instalá-lo.  | 6.0.0 |

### <a name="installation"></a>Instalação

Antes de continuar, tenha em atenção a área de trabalho do Log Analytics, ligado à sua conta de automatização. Observe também a chave primária para a sua conta de automatização. Pode encontrar ambas no portal do Azure ao selecionar a automação de contas, selecionando **área de trabalho** para o ID de área de trabalho e selecionando **chaves** para a chave primária. Para obter informações sobre portas e endereços que precisa para a função de trabalho de Runbook híbrida, veja [configurar a rede](automation-hybrid-runbook-worker.md#network-planning).

1. Ativar a **função de trabalho de híbrida de automatização** solução no Azure através de um dos seguintes métodos:

   * Adicionar a **função de trabalho de híbrida de automatização** solução à sua subscrição, utilizando o procedimento na [Azure Monitor adicionar registos de soluções à sua área de trabalho](../log-analytics/log-analytics-add-solutions.md).
   * Execute o seguinte cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Instale o agente do Log Analytics para Linux ao executar o seguinte comando. Substitua \<WorkspaceID\> e \<WorkspaceKey\> com os valores apropriados da sua área de trabalho.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Execute o seguinte comando, alterando os valores para os parâmetros *-w*, *-k*, *-g*, e *-e*. Para o *-g* parâmetro, substitua o valor com o nome do grupo de trabalho de Runbook híbrida que o novo Runbook Worker do Linux híbrida deve aderir. Se o nome não existir na sua conta de automatização, um novo grupo de trabalho de Runbook híbrida é feito com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Depois do comando é concluído, o **grupos de trabalho híbrido** página no portal do Azure mostra o novo grupo e o número de membros. Se se tratar de um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na **grupos de trabalho híbrido** página e selecione o **funções de trabalho híbridas** mosaico. Sobre o **funções de trabalho híbridas** página, verá cada membro do grupo listado.

## <a name="turning-off-signature-validation"></a>Desativar validação da assinatura

Por predefinição, os Runbook Workers do Linux híbridos necessitam de validação da assinatura. Se executar um runbook não assinado em relação a uma função de trabalho, verá um erro que diz "Falha na validação de assinatura." Para desativar a validação da assinatura, execute o seguinte comando. Substitua o segundo parâmetro com o ID de área de trabalho de análise do registo.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipos de runbook suportados

Os Runbook Workers híbridos de Linux não suportam o conjunto completo de tipos de runbooks na automatização do Azure.

Os seguintes tipos de runbook trabalham numa função de trabalho híbrida Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Os runbooks do PowerShell requer o PowerShell Core ser instalado na máquina Linux. Ver [instalar o PowerShell Core no Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) para saber como instalá-lo.

Os seguintes tipos de runbook não funcionam numa função de trabalho híbrida Linux:

* Fluxo de trabalho do PowerShell
* Gráfico
* Fluxo de trabalho de PowerShell gráfico

## <a name="next-steps"></a>Passos Seguintes

* Para saber como configurar os seus runbooks para automatizar processos no seu datacenter no local ou outro ambiente de cloud, veja [executar runbooks numa função de trabalho de Runbook híbrida](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os Runbook Workers híbridos, consulte [remover Azure Automation os Runbook Workers híbridos](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como resolver problemas de suas funções de trabalho de Runbook híbridas, consulte [resolução de problemas Linux os Runbook Workers híbridos](troubleshoot/hybrid-runbook-worker.md#linux)