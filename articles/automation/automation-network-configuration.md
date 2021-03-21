---
title: Detalhes da configuração da rede Azure Automation
description: Este artigo fornece detalhes das informações de rede exigidas pela Azure Automation State Configuration, Azure Automation Hybrid Runbook Worker, Update Management e Change Tracking and Inventory
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708888"
---
# <a name="azure-automation-network-configuration-details"></a>Detalhes da configuração da rede Azure Automation

Esta página fornece detalhes de networking que são necessários para [o Trabalhador de Runbook Híbrido e Configuração do Estado](#hybrid-runbook-worker-and-state-configuration), e para a [Gestão de Atualização e Rastreio e Inventário de Alterações.](#update-management-and-change-tracking-and-inventory)

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Trabalhador de runbook híbrido e configuração do estado

São necessários os seguintes portos e URLs para o Trabalhador de Runbook Híbrido e para a [Configuração do Estado da Automação](automation-dsc-overview.md) comunicar com a Azure Automation.

* Porto: Apenas 443 necessários para acesso à Internet de saída
* URL global: `*.azure-automation.net`
* URL global de Us Gov Virginia: `*.azure-automation.us`
* Serviço de agente: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Planeamento de rede para trabalhador de runbook híbrido

Para que um sistema ou utilizador Hybrid Runbook Worker se conecte e registe com a Azure Automation, deve ter acesso ao número de porta e URLs descritos nesta secção. O trabalhador também deve ter acesso às [portas e URLs necessários para que o agente Log Analytics](../azure-monitor/agents/agent-windows.md) se conecte ao espaço de trabalho Azure Monitor Log Analytics.

Se tiver uma conta Automation que está definida para uma região específica, pode restringir a comunicação do Trabalhador Do Runbook Híbrido a esse datacenter regional. Reveja os [registos DNS utilizados pela Azure Automation](how-to/automation-region-dns-records.md) para obter os registos DNS necessários.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Configuração de redes privadas para configuração do Estado

Se os seus nós estiverem localizados numa rede privada, são necessários os portos e URLs acima definidos. Estes recursos fornecem conectividade de rede para o nó gerido e permitem que a DSC comunique com a Azure Automation.

Se estiver a utilizar recursos DSC que comunicam entre nós, como os [recursos WaitFor*,](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)também precisa de permitir o tráfego entre nós. Consulte a documentação de cada recurso DSC para compreender estes requisitos de rede.

Para compreender os requisitos do cliente para tLS 1.2, consulte [a aplicação TLS 1.2 para a Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Gestão de atualização e rastreio de alterações e inventário

Os endereços desta tabela são necessários tanto para a Gestão de Atualização como para o Rastreio de Alterações e Inventário. O parágrafo que se segue ao quadro aplica-se também a ambos.

A comunicação a estes endereços utiliza a **porta 443**.

|Azure Público  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*.azure-automation.us|

Quando criar regras de segurança do grupo de rede ou configurar o Azure Firewall para permitir o tráfego para o serviço de Automação e para o espaço de trabalho Log Analytics, utilize as [etiquetas](../virtual-network/service-tags-overview.md#available-service-tags) de serviço **GuestAndHybridManagement** e **AzureMonitor**. Isto simplifica a gestão contínua das suas regras de segurança de rede. Para ligar ao serviço Demômes a partir dos seus VMs Azure de forma segura e privada, reveja [o Link Privado Use Azure](./how-to/private-link-security.md). Para obter a etiqueta de serviço atual e informações de alcance para incluir como parte das configurações de firewall no local, consulte [ficheiros JSON descarregados](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a visão geral da Gestão da Atualização de Automação.](update-management\overview.md)
* Saiba mais sobre [o Trabalhador de Runbook Híbrido.](automation-hybrid-runbook-worker.md)
* Saiba mais sobre [o Rastreio e Inventário de Alterações.](change-tracking\overview.md)
* Saiba mais sobre [a Configuração do Estado da Automação.](automation-dsc-overview.md)