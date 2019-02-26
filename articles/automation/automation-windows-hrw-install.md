---
title: Função de Trabalho de Runbook Híbrida do Windows da Automatização do Azure
description: Este artigo fornece informações sobre como instalar um Azure Automation Runbook Worker híbrido que pode utilizar para executar runbooks nos computadores baseados em Windows no seu local datacenter ou o ambiente na cloud.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 500d22993830534027c7ba9b372b6880cb608510
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822741"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementar um Runbook Worker híbrido do Windows

Pode utilizar a funcionalidade de trabalho de Runbook híbrida da automatização do Azure para executar runbooks diretamente no computador que aloja a função e relativamente aos recursos no ambiente para gerir esses recursos locais. Runbooks são armazenados e gerenciados na automatização do Azure e, em seguida, entregue a um ou mais computadores designados. Este artigo descreve como instalar a função de trabalho de Runbook híbrida numa máquina Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalar a função de trabalho de Runbook do Windows híbrida

Para instalar e configurar um Runbook Worker híbrido do Windows, pode usar dois métodos. O método recomendado é utilizar um runbook da automatização para automatizar completamente o processo de configuração de um computador Windows. O segundo método está a seguir um procedimento passo a passo para instalar e configurar a função manualmente.

> [!NOTE]
> Para gerir a configuração dos seus servidores que suportam a função de trabalho de Runbook híbrida com o Desired State Configuration (DSC), terá de adicioná-los como nós de DSC.

Os requisitos mínimos para uma função de trabalho de Runbook do Windows híbrida são:

* Windows Server 2012 ou posterior.
* Windows PowerShell 5.1 ou posterior ([transferir WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET framework 4.6.2 ou posterior.
* Dois núcleos.
* 4 GB de RAM.
* Porta 443 (saída).

Para obter mais requisitos de rede para a função de trabalho de Runbook híbrida, veja [configurar a rede](automation-hybrid-runbook-worker.md#network-planning).

Para obter mais informações sobre servidores de integração para a gestão com DSC, veja [integrar computadores para gestão pelo Azure Automation DSC](automation-dsc-onboarding.md).
Se ativar a [solução de gestão de atualizações](../operations-management-suite/oms-solution-update-management.md), qualquer computador Windows que está ligado à sua área de trabalho do Log Analytics do Azure é automaticamente configurado como uma função de trabalho de Runbook híbrida para suportar os runbooks incluídos nesta solução. No entanto, não está registado com quaisquer grupos de função de trabalho híbrida já definidos na sua conta de automatização. 

O computador pode ser adicionado a um grupo de trabalho de Runbook híbrida na conta de automatização para suportar runbooks de automatização, desde que estiver a utilizar a mesma conta para a solução e a associação ao grupo de trabalho de Runbook híbrida. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

Depois de implementar com êxito uma função de trabalho de runbook, reveja [executar runbooks numa função de trabalho de Runbook híbrida](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos no seu datacenter no local ou outro ambiente de cloud.

### <a name="automated-deployment"></a>Implementação automatizada

Execute os seguintes passos para automatizar a instalação e configuração da função da função de trabalho do Windows híbrida:

1. Transferir o script New-OnPremiseHybridWorker.ps1 a partir da [galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) diretamente a partir do computador que executa a função de trabalho de Runbook híbrida ou de outro computador no seu ambiente. Copie o script para a função de trabalho.

   O script New-OnPremiseHybridWorker.ps1 requer os seguintes parâmetros durante a execução:

   * *AutomationAccountName* (obrigatório): O nome da sua conta de automatização.
   * *AAResourceGroupName* (obrigatório): O nome do grupo de recursos que está associada à sua conta de automatização.
   * *OMSResourceGroupName* (opcional): O nome do grupo de recursos para a área de trabalho do Log Analytics. Se este grupo de recursos não for especificado, *AAResourceGroupName* é utilizado.
   * *HybridGroupName* (obrigatório): O nome de um grupo de trabalho de Runbook híbrida que especificar como um destino para os runbooks que suportar este cenário.
   * *SubscriptionID* (obrigatório): O ID de subscrição do Azure que pertença a sua conta de automatização.
   * *WorkspaceName* (opcional): O nome de área de trabalho do Log Analytics. Se não tiver uma área de trabalho do Log Analytics, o script cria e configura uma.

     > [!NOTE]
     > Atualmente, são as regiões de automatização apenas suportadas para a integração com os registos do Azure Monitor **Sudeste da Austrália**, **E.U.A. Leste 2**, **Sudeste asiático**e **Europa Ocidental**. Se a sua conta de automatização não estiver em uma dessas regiões, o script cria uma área de trabalho do Log Analytics, mas avisa-o de que ele não é possível vinculá-las em conjunto.

2. No seu computador, abra **Windows PowerShell** partir do **iniciar** ecrã no modo de administrador.
3. A partir da shell de linha de comandos do PowerShell, navegue até à pasta que contém o script que transferiu. Alterar os valores para os parâmetros *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, e *- WorkspaceName*. Em seguida, execute o script.

     > [!NOTE]
     > Lhe for pedido para autenticar com o Azure depois de executar o script. *Tem* iniciar sessão com uma conta que seja membro da função administradores da subscrição e coadministrador da subscrição.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Lhe for pedido para aceitar instalar o NuGet, e se lhe for pedido para autenticar com as suas credenciais do Azure.

5. Depois do script estiver concluído, o **grupos de trabalho híbrido** página mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na **grupos de trabalho híbrido** página e selecione o **funções de trabalho híbridas** mosaico. Sobre o **funções de trabalho híbridas** página, verá cada membro do grupo listado.

### <a name="manual-deployment"></a>Implementação manual

Execute os primeiros dois passos uma vez para o seu ambiente de automatização e, em seguida, repita os passos restantes para cada computador de trabalho.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Criar uma área de trabalho do Log Analytics

Se ainda não tiver uma área de trabalho do Log Analytics, crie uma utilizando as instruções em [gerir a sua área de trabalho](../azure-monitor/platform/manage-access.md). Pode utilizar uma área de trabalho existente se já tiver um.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Adicionar a solução de automatização à área de trabalho do Log Analytics

As soluções acrescentam funcionalidades aos registos do Azure Monitor. A solução de automatização adiciona funcionalidade para a automatização do Azure, incluindo suporte para o Runbook Worker híbrido. Quando adiciona a solução para a área de trabalho, envia automaticamente componentes de trabalho para o computador de agente que pretende instalar no próximo passo.

Para adicionar o **automatização** solução para a área de trabalho do Log Analytics, siga as instruções em [para adicionar uma solução utilizando a Galeria de soluções](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalar o Microsoft Monitoring Agent

O Microsoft Monitoring Agent liga-se os computadores para os registos do Azure Monitor. Quando instalar o agente no seu computador no local e ligá-la à sua área de trabalho, transfere automaticamente os componentes que são necessários para o Runbook Worker híbrido.

Para instalar o agente no computador no local, siga as instruções em [computadores Windows ligar aos registos do Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Pode repetir este processo para vários computadores adicionar múltiplos workers para o seu ambiente.

Quando o agente foi ligado com êxito para os registos do Azure Monitor, é apresentada no **origens ligadas** separador da análise de registos **definições** página. Pode verificar que o agente transferiu corretamente a solução de automatização quando tem uma pasta denominada **AzureAutomationFiles** em C:\Program Files\Microsoft Monitoring Agent\Agent. Para confirmar a versão da função de trabalho de Runbook híbrida, pode procurar em C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ e tenha em atenção a \\ *versão* subpasta.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalar o ambiente de runbook e ligar a automatização do Azure

Quando adiciona um agente para registos do Azure Monitor, a solução de automatização envia por push para baixo a **HybridRegistration** módulo do PowerShell, que contém o **Add-HybridRunbookWorker** cmdlet. Utilize este cmdlet para instalar o ambiente de runbook no computador e registá-lo a automatização do Azure.

Abra uma sessão do PowerShell no modo de administrador e execute os seguintes comandos para importar o módulo:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Em seguida, execute o **Add-HybridRunbookWorker** cmdlet utilizando a seguinte sintaxe:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Pode obter as informações necessárias para este cmdlet a partir da **gerir chaves** página no portal do Azure. Abra a página ao selecionar o **chaves** opção da **definições** página na conta de automatização.

![Página "Gerenciar chaves"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** é o nome do grupo de trabalho de Runbook híbrida. Se este grupo já existir na conta de automatização, o computador atual é adicionado a ele. Se este grupo não existir, é adicionado.
* **Ponto final** é o **URL** entrada sobre o **gerir chaves** página.
* **Token** é o **chave de acesso primária** entrada sobre o **gerir chaves** página.

Para receber informações detalhadas sobre a instalação, utilize o **-verboso** mudar com **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Instalar os módulos do PowerShell

Runbooks, pode utilizar qualquer uma das atividades e cmdlets definidos nos módulos que estão instalados no seu ambiente de automatização do Azure. Estes módulos não são implementados automaticamente em computadores no local, tem das instalar manualmente. A exceção é o módulo do Azure, o que é instalado por padrão e fornece acesso a cmdlets para todos os serviços do Azure e as atividades para a automatização do Azure.

Como é a principal finalidade da funcionalidade do Runbook Worker híbrido gerir recursos locais, é mais provável que precisa instalar os módulos que suportam esses recursos. Para obter informações sobre como instalar os módulos do Windows PowerShell, consulte [instalar módulos](https://msdn.microsoft.com/library/dd878350.aspx). 

Tem de ser num local referenciado por módulos que estão instalados os **PSModulePath** variável de ambiente para que a função de trabalho híbrida pode importá-los automaticamente. Para obter mais informações, consulte [modificar o caminho de instalação PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Resolução de problemas

Para saber como resolver problemas de suas funções de trabalho de Runbook híbridas, consulte [resolução de problemas do Windows os Runbook Workers híbridos](troubleshoot/hybrid-runbook-worker.md#windows)

Para obter passos adicionais sobre como resolver problemas de gerenciamento de atualizações, consulte [gestão de atualizações: Resolução de problemas](troubleshoot/update-management.md).

## <a name="next-steps"></a>Passos Seguintes

* Para saber como configurar os seus runbooks para automatizar processos no seu datacenter no local ou outro ambiente de cloud, veja [executar runbooks numa função de trabalho de Runbook híbrida](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os Runbook Workers híbridos, consulte [remover Azure Automation os Runbook Workers híbridos](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).

