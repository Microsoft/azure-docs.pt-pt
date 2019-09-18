---
title: Função de Trabalho de Runbook Híbrida do Linux da Automatização do Azure
description: Este artigo fornece informações sobre como instalar uma Hybrid Runbook Worker de automação do Azure para que você possa executar runbooks em computadores baseados em Linux em seu datacenter local ou ambiente de nuvem.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 878e79097114f60aff084d60c835661196cec5ce
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076011"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implantar um Hybrid Runbook Worker do Linux

Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. O Hybrid Runbook Worker do Linux executa runbooks como um usuário especial que pode ser elevado para executar comandos que precisam de elevação. Os Runbooks são armazenados e gerenciados na automação do Azure e, em seguida, entregues a um ou mais computadores designados.

Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Linux.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

O recurso Hybrid Runbook Worker dá suporte às seguintes distribuições:

* Amazon Linux 2012, 9 a 2015, 9 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12, 4 LTS, 14, 4 LTS e 16, 4 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalando um Hybrid Runbook Worker Linux

Para instalar e configurar um Hybrid Runbook Worker em seu computador Linux, siga um processo direto para instalar e configurar manualmente a função. Ele requer a habilitação da solução **Hybrid Worker de automação** no espaço de trabalho log Analytics do Azure e, em seguida, a execução de um conjunto de comandos para registrar o computador como um trabalho e adicioná-lo a um grupo.

Os requisitos mínimos para um Hybrid Runbook Worker do Linux são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="package-requirements"></a>Requisitos do pacote

| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|OpenSSL| Bibliotecas OpenSSL | 1,0 (há suporte para o TLS 1,1 e o TLS 1,2|
|curl | enrolando o cliente Web | 7.15.5|
|Python-ctypes | O Python 2. x é obrigatório |
|PAM | Módulos de autenticação conectáveis|
| **Pacote opcional** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar runbooks do PowerShell, o PowerShell precisa ser instalado, consulte [instalando o PowerShell Core no Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) para saber como instalá-lo.  | 6.0.0 |

### <a name="installation"></a>Instalação

Antes de prosseguir, observe o espaço de trabalho Log Analytics ao qual sua conta de automação está vinculada. Observe também a chave primária para sua conta de automação. Você pode encontrar ambos no portal do Azure selecionando sua conta de automação, selecionando **espaço de trabalho** para a ID do espaço de trabalho e selecionando **chaves** para a chave primária. Para obter informações sobre portas e endereços necessários para o Hybrid Runbook Worker, consulte [Configurando sua rede](automation-hybrid-runbook-worker.md#network-planning).

1. Habilite a solução **Hybrid Worker de automação** no Azure usando um dos seguintes métodos:

   * Adicione a solução **Hybrid Worker de automação** à sua assinatura usando o procedimento em [Adicionar soluções de logs de Azure monitor ao seu espaço de trabalho](../log-analytics/log-analytics-add-solutions.md).
   * Execute o seguinte cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Instale o agente de Log Analytics para Linux executando o comando a seguir. Substitua \< workspaceid\<e WorkspaceKeypelos\> valores apropriados do seu espaço de trabalho.\>

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Execute o comando a seguir, alterando os valores para os parâmetros *-w*, *-k*, *-g*e *-e*. Para o parâmetro *-g* , substitua o valor pelo nome do grupo de Hybrid runbook Worker ao qual o novo Hybrid runbook Worker Linux deve ingressar. Se o nome não existir em sua conta de automação, um novo grupo de Hybrid Runbook Worker será criado com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Depois que o comando for concluído, a página **grupos de Hybrid Worker** na portal do Azure mostrará o novo grupo e o número de membros. Se esse for um grupo existente, o número de membros será incrementado. Você pode selecionar o grupo na lista na página **grupos de Hybrid Worker** e selecionar o bloco **Hybrid Workers** . Na página **Hybrid Workers** , você verá cada membro do grupo listado.

> [!NOTE]
> Se você estiver usando a extensão de máquina virtual Azure monitor para Linux para uma VM do Azure, `autoUpgradeMinorVersion` é recomendável definir como false, pois as versões de atualização automática podem causar problemas na Hybrid runbook Worker. Para saber como atualizar a extensão manualmente, consulte [CLI do Azure implantação ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Desativando a validação de assinatura

Por padrão, os Hybrid runbook Workers do Linux exigem validação de assinatura. Se você executar um runbook não assinado em um trabalho, verá um erro que diz "falha na validação da assinatura." Para desativar a validação de assinatura, execute o comando a seguir. Substitua o segundo parâmetro pela ID do espaço de trabalho do log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipos de runbook com suporte

Os Hybrid runbook Workers do Linux não dão suporte ao conjunto completo de tipos de runbook na automação do Azure.

Os seguintes tipos de runbook funcionam em um Hybrid Worker do Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Os runbooks do PowerShell exigem que o PowerShell Core seja instalado no computador Linux. Consulte [instalando o PowerShell Core no Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) para saber como instalá-lo.

Os seguintes tipos de runbook não funcionam em um Hybrid Worker Linux:

* Fluxo de trabalho do PowerShell
* Corrompi
* Fluxo de trabalho gráfico do PowerShell

## <a name="next-steps"></a>Passos Seguintes

* Para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem, consulte [executar runbooks em um Hybrid runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover Hybrid runbook Workers, consulte [remover Hybrid runbook Workers da automação do Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como solucionar problemas de seus Hybrid runbook Workers, confira [solução de problemas de Hybrid runbook Workers do Linux](troubleshoot/hybrid-runbook-worker.md#linux)
