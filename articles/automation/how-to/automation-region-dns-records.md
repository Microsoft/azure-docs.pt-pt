---
title: Registos DNSS do Azure Datacenter utilizados pela Azure Automation | Microsoft Docs
description: Este artigo fornece os registos DNS exigidos pela Azure Automation ao restringir a comunicação a uma região específica do Azure que alberga essa conta Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: 8630afa7410aad81a7a3c61540fc74702fc6481c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575988"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Registos dns para regiões Azure utilizadas pela Azure Automation

O serviço [Azure Automation](../automation-intro.md) utiliza uma série de registos DNS para funcionalidades que se ligam ao serviço. Se tiver uma conta Automation que está definida para uma região específica, pode restringir a comunicação a esse datacenter regional. Poderá ser necessário conhecer estes registos para permitir que as seguintes funcionalidades de Automação funcionem quando estão hospedadas atrás de uma firewall:

* Função de Trabalho de Runbook Híbrida
* Configuração de Estado
* Webhooks

>[!NOTE]
>O registo do Trabalhador de Runbook Híbrido Linux falhará com os novos registos, a menos que seja a versão 1.6.10.2 ou superior. Tem de fazer upgrade para uma versão mais recente do agente Log Analytics para o [Linux,](../../azure-monitor/agents/agent-linux.md) para que a máquina receba uma versão atualizada da função do trabalhador e utilize estes novos registos. As máquinas existentes continuarão a funcionar sem qualquer problema.  

## <a name="dns-records-per-region"></a>Registos dns por região

A tabela seguinte fornece o registo de DNS para cada região.

>[!NOTE]
>Embora a lista de registos DNS da Automação aqui fornecidos tenha sido retirada, continuam funcionais para que possa migrar para os novos registos listados no [suporte do Private Link](#support-for-private-link) e evitar falhas nos seus processos de automação.

| **Região** | **Registo de DNS** |
| --- | --- |
| Austrália Central |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Ásia Sudeste |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| E.U.A. Oeste 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Suporte para Ligação Privada

Para suportar o [Private Link](../../private-link/private-link-overview.md) in Azure Automation, os registos DNS de todos os datacenters suportados foram atualizados. Em vez de URLs específicos da região, os URLs são específicos da conta de automação.

| **Região** | **Registo de DNS** |
| --- | --- |
| E.U.A. Centro-Oeste |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| E.U.A. Oeste |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| E.U.A. Oeste 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| E.U.A. Central |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| E.U.A. Centro-Sul |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| E.U.A. Centro-Norte |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| E.U.A. Leste |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| E.U.A. Leste 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Canadá Central |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Europa Ocidental |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Europa do Norte |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Ásia Sudeste |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Ásia Leste |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Índia Central |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Leste do Japão |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Coreia do Sul Central |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Sudeste da Austrália |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Leste da Austrália |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Austrália Central |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Sul do Reino Unido |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| França Central |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Norte da África do Sul |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Sul do Brasil |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Norte da China |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| China Norte 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| China Leste 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov - Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov - Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov - Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Substitua `<accountId>` no registo DNS pelo GUID representando o seu ID de Conta de Automação a partir do **URL** de valor . Pode obter o ID necessário a partir de **Chaves** em **Definições** de Conta no portal Azure.

![Página chave primária da conta de automação](./media/automation-region-dns-records/automation-account-keys.png)

Copiar o valor após *contas/* a partir do campo **URL** - `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Todos os registos DNS do Webhook e do agentservice foram atualizados para os novos registos DNS de estilo para suportar o Private Link. Para os registos DNS JRDS, os registos DNS de estilo antigo e novo são suportados. Se não estiver a utilizar o Private Link, verá os registos DNS de estilo antigo, enquanto os que usam o Private Link verão um novo estilo de registos DNS.

Recomendamos que utilize os endereços listados ao definir [exceções](../automation-runbook-execution.md#exceptions). Para obter uma lista de endereços IP da região em vez de nomes de região, descarregue o ficheiro JSON do Microsoft Download Center para os seguintes ambientes em nuvem:

* [Gamas Azure IP e Tags de Serviço - Público Azure](https://www.microsoft.com/download/details.aspx?id=56519)
* [Gamas Azure IP e tags de serviço- Governo Azure](https://www.microsoft.com/download/details.aspx?id=57063)
* [Gamas Azure IP e Tags de Serviço - Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
* [Gamas Azure IP e Tags de Serviço – Azure China Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

O ficheiro de endereço IP lista os intervalos de endereços IP utilizados nos datacenters do Microsoft Azure. Inclui gamas de cálculo, SQL e armazenamento, e reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são utilizadas nos datacenters durante pelo menos uma semana.

É uma boa ideia descarregar o novo ficheiro de endereço IP todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em execução em Azure.

> [!NOTE]
> Se estiver a utilizar o Azure ExpressRoute, lembre-se que o ficheiro de endereço IP é utilizado para atualizar o anúncio do Border Gateway Protocol (BGP) do espaço Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas.](../troubleshoot/hybrid-runbook-worker.md#general)

* Para aprender a resolver problemas com a Configuração do Estado, consulte [problemas de configuração do estado de resolução de problemas](../troubleshoot/desired-state-configuration.md).