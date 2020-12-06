---
title: Relatórios padrão de proteção Azure DDoS e registos de fluxo
description: Saiba como configurar relatórios e registos de fluxo.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c3bfbdf133777f0bc219d1306f80bd4d38b56ea
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746272"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Configurar registos de fluxos e relatórios de mitigação de ataques de DDoS 

A padrão de proteção DDoS Azure fornece informações detalhadas sobre ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques de DDoS têm uma visibilidade detalhada no tráfego de ataques e as ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques & registos de fluxo de mitigação. A telemetria rica é exposta através do Azure Monitor, incluindo métricas detalhadas durante a duração de um ataque DDoS. O alerta pode ser configurado para qualquer uma das métricas do Monitor Azure expostas pela Proteção DDoS. O registo pode ser integrado com [Azure Sentinel,](../sentinel/connect-azure-ddos-protection.md)Splunk (Azure Event Hubs), OMS Log Analytics e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Ver e configurar relatórios de mitigação de ataques DDoS
> * Ver e configurar registos de fluxo de atenuação de ataques DDoS

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Ver e configurar relatórios de mitigação de ataques DDoS

Os relatórios de mitigação de ataques utilizam os dados do protocolo Netflow que são agregados para fornecer informações detalhadas sobre o ataque ao seu recurso. Sempre que um recurso IP público estiver a ser atacado, a geração do relatório começará assim que a mitigação começar. Haverá um relatório incremental gerado a cada 5 minutos e um relatório pós-mitigação para todo o período de mitigação. Isto é para garantir que, caso o ataque do DDoS continue por um período mais longo, você será capaz de ver o instantâneo mais atual do relatório de atenuação a cada 5 minutos e um resumo completo uma vez que a mitigação do ataque termina. 

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Em **DEFINIÇÕES**, selecione **Definições de diagnóstico**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público que pretende registar.
5. Selecione **o Endereço IP Público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais pretende registar métricas.
6. Selecione **Ligue os diagnósticos para recolher o registo DDoSMitigationReports** e, em seguida, selecione quantas opções for necessária:

    - **Arquivar para uma conta de armazenamento**: Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [os registos de recursos do Arquivo.](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)
    - **Stream para um centro de eventos**: Permite que um recetor de log recolha registos usando um Azure Event Hub. Os centros de eventos permitem a integração com a Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de recursos do Stream para um centro de eventos.](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Tanto os relatórios incrementais de mitigação & pós-ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas de tráfego
- Razão para pacotes abandonados
- Protocolos envolvidos
- Top 10 países ou regiões de origem
- Top 10 asns de origem

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Ver e configurar registos de fluxo de atenuação de ataques DDoS
Os registos de fluxo de mitigação de ataques permitem-lhe rever o tráfego desatado, tráfego reencaminhado e outros pontos de dados interessantes durante um ataque DDoS ativo em tempo quase real. Pode ingerir o fluxo constante destes dados no Azure Sentinel ou nos seus sistemas SIEM de terceiros através do centro de eventos para monitorização em tempo real, tomar potenciais ações e responder à necessidade das suas operações de defesa.

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Em **DEFINIÇÕES**, selecione **Definições de diagnóstico**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público que pretende registar.
5. Selecione **o Endereço IP Público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais pretende registar métricas.
6. Selecione **Ligue os diagnósticos para recolher o registo DDoSMitigationFlowLogs** e, em seguida, selecione quantas opções for necessária:

    - **Arquivar para uma conta de armazenamento**: Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [os registos de recursos do Arquivo.](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)
    - **Stream para um centro de eventos**: Permite que um recetor de log recolha registos usando um Azure Event Hub. Os centros de eventos permitem a integração com a Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de recursos do Stream para um centro de eventos.](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Este [modelo](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) cria uma definição de Política Azure para permitir a exploração de registo de diagnóstico.

### <a name="azure-sentinel-data-connector"></a>Conector de dados Azure Sentinel

Pode ligar os registos de fluxo de mitigação de ataques ao Azure Sentinel, ver e analisar os seus dados em livros de trabalho, criar alertas personalizados e incorporá-los em processos de investigação. Para ligar ao Azure Sentinel, consulte [Connect to Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Conector Azure Sentinel DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Livro de proteção Azure DDos

Para visualizar os dados dos registos de fluxo no painel de análise Azure, pode importar o painel de amostras a partir de https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Os registos de fluxo terão os seguintes campos: 
- IP de origem
- IP de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Medidas tomadas durante a mitigação

![Livro de Proteção dDos](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

A análise de ataque só funcionará se a Norma de Proteção DDoS estiver ativada na rede virtual do endereço IP público. 

## <a name="sample-log-outputs"></a>Saídas de registo de amostras

As seguintes imagens são saídas de registo de exemplo:

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotificações

![Proteção DDoSProtectionNotificações](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

- Ver e configurar relatórios de mitigação de ataques DDoS
- Ver e configurar registos de fluxo de atenuação de ataques DDoS

Para aprender a testar e simular um ataque DDoS, consulte o guia de testes de simulação:

> [!div class="nextstepaction"]
> [Testar através de simulações](test-through-simulations.md)