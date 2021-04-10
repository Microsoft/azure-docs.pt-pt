---
title: Relatórios padrão de proteção Azure DDoS e registos de fluxo
description: Saiba como configurar relatórios e registos de fluxo.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 7f8e3df927b74cff7e4dc8bf1456600740c07088
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567683"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Ver e configurar o registo de diagnósticos do DDoS

A padrão de proteção DDoS Azure fornece informações detalhadas sobre ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques de DDoS têm uma visibilidade detalhada no tráfego de ataques e as ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques & registos de fluxo de mitigação. A telemetria rica é exposta através do Azure Monitor, incluindo métricas detalhadas durante a duração de um ataque DDoS. Os alertas podem ser configurados para qualquer uma das métricas do Azure Monitor expostas pelo DDoS Protection. O registo pode ser integrado com [Azure Sentinel,](../sentinel/connect-azure-ddos-protection.md)Splunk (Azure Event Hubs), OMS Log Analytics e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

Estão disponíveis os seguintes registos de diagnóstico para a Norma de Proteção DDoS Azure: 

- **DDoSProtectionNotifications**: As notificações notificam-no sempre que um recurso IP público estiver a ser atacado e quando a mitigação do ataque terminar.
- **DDoSMitigationFlowLogs**: Os registos de fluxo de mitigação de ataques permitem-lhe rever o tráfego desatado, o tráfego reencaminhado e outros pontos de dados interessantes durante um ataque DDoS ativo em tempo quase real. Pode ingerir o fluxo constante destes dados no Azure Sentinel ou nos seus sistemas SIEM de terceiros através do centro de eventos para monitorização em tempo real, tomar potenciais ações e responder à necessidade das suas operações de defesa.
- **DDoSMitigationReports**: Os relatórios de mitigação de ataques utilizam os dados do protocolo Netflow que são agregados para fornecer informações detalhadas sobre o ataque ao seu recurso. Sempre que um recurso IP público estiver a ser atacado, a geração do relatório começará assim que a mitigação começar. Haverá um relatório incremental gerado a cada 5 minutos e um relatório pós-mitigação para todo o período de mitigação. Isto é para garantir que, caso o ataque do DDoS continue por um período mais longo, você será capaz de ver o instantâneo mais atual do relatório de atenuação a cada 5 minutos e um resumo completo uma vez que a mitigação do ataque termina. 
- **AllMetrics**: Fornece todas as métricas possíveis disponíveis durante a duração de um ataque DDoS. 

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Configure registos de diagnóstico DDoS, incluindo notificações, relatórios de mitigação e registos de fluxos de mitigação. 
> * Permitir o registo de diagnóstico em todos os IPs públicos num âmbito definido.
> * Ver registos de dados em livros de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md) e o DDoS Protection Standard deve ser ativado numa rede virtual.
- O DDoS monitoriza endereços IP públicos atribuídos a recursos dentro de uma rede virtual. Se não tiver recursos com endereços IP públicos na rede virtual, terá primeiro de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos implantados através do Gestor de Recursos (não clássico) listados na [rede Virtual para serviços Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo Equiliadores de Carga Azure onde as máquinas virtuais de backend estão na rede virtual), exceto para Ambientes de Serviço de Aplicações Azure. Para continuar com este tutorial, pode rapidamente criar uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)    

## <a name="configure-ddos-diagnostic-logs"></a>Configurar registos de diagnóstico DDoS

Se pretender ativar automaticamente o registo de diagnóstico em todos os IPs públicos dentro de um ambiente, ignore para ativar a [sessão de diagnóstico em todos os IPs públicos](#enable-diagnostic-logging-on-all-public-ips).

1. No canto superior esquerdo do portal, selecione **Todos os serviços**.
2. Introduza *Monitor* na caixa **Filtro**. Quando **Monitor** aparecer nos resultados, selecione-o.
3. Em **Definições**, selecione **Definições de Diagnóstico**.
4. Selecione a **Subscrição** e o **Grupo de Recursos** que contêm o endereço IP público que quer registar.
5. Selecione **endereço IP público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o seguinte para ativar registos.
6. Selecione **Adicionar definição de diagnóstico**. Em **Detalhes da Categoria**, selecione quantas das seguintes opções quiser e, em seguida, selecione **Guardar**.

    ![Definições de diagnóstico DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. Nos **detalhes do Destino,** selecione quantas opções for necessária:

    - **Arquivar para uma conta de armazenamento**: Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [os registos de recursos do Arquivo.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)
    - **Stream para um centro de eventos**: Permite que um recetor de log recolha registos usando um Azure Event Hub. Os centros de eventos permitem a integração com a Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de recursos do Stream para um centro de eventos.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Esquemas de registo

A tabela que se segue lista os nomes e descrições do campo:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotificações](#tab/DDoSProtectionNotifications)

| Nome do campo | Description |
| --- | --- |
| **TimeGenerated** | A data e a hora na UTC quando a notificação foi criada. |
| **ResourceId** | Identificação de recursos do seu endereço IP público. |
| **Categoria** | Para notificações, esta `DDoSProtectionNotifications` será.|
| **Grupo de Recursos** | O grupo de recursos que contém o seu endereço IP público e rede virtual. |
| **SubscriçãoId** | O seu ID de subscrição do plano de proteção DDoS. |
| **Recurso** | O nome do seu endereço IP público. |
| **ResourceType** | Isto será `PUBLICIPADDRESS` sempre. |
| **Operação Nome** | Para notificações, esta `DDoSProtectionNotifications` será.  |
| **Mensagem** | Detalhes do ataque. |
| **Tipo** | Tipo de notificação. Os valores possíveis `MitigationStarted` incluem. `MitigationStopped`. |
| **PublicIpAddress** | O seu endereço IP público. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Nome do campo | Description |
| --- | --- |
| **TimeGenerated** | A data e a hora na UTC quando o registo de fluxo foi criado. |
| **ResourceId** | Identificação de recursos do seu endereço IP público. |
| **Categoria** | Para os registos de fluxo, este será `DDoSMitigationFlowLogs` .|
| **Grupo de Recursos** | O grupo de recursos que contém o seu endereço IP público e rede virtual. |
| **SubscriçãoId** | O seu ID de subscrição do plano de proteção DDoS. |
| **Recurso** | O nome do seu endereço IP público. |
| **ResourceType** | Isto será `PUBLICIPADDRESS` sempre. |
| **Operação Nome** | Para os registos de fluxo, este será `DDoSMitigationFlowLogs` . |
| **Mensagem** | Detalhes do ataque. |
| **FontePublicIpAddress** | O endereço IP público do cliente que gera tráfego para o seu endereço IP público. |
| **FontePort** | Número de porta que varia de 0 a 65535. |
| **DestPublicIpAddress** | O seu endereço IP público. |
| **DestPort** | Número de porta que varia de 0 a 65535. |
| **Protocolo** | Tipo de protocolo. Os valores possíveis `tcp` `udp` incluem, . `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Nome do campo | Description |
| --- | --- |
| **TimeGenerated** | A data e a hora na UTC quando o relatório foi criado. |
| **ResourceId** | Identificação de recursos do seu endereço IP público. |
| **Categoria** | Para notificações, esta `DDoSProtectionNotifications` será.|
| **Grupo de Recursos** | O grupo de recursos que contém o seu endereço IP público e rede virtual. |
| **SubscriçãoId** | O seu ID de subscrição do plano de proteção DDoS. |
| **Recurso** | O nome do seu endereço IP público. |
| **ResourceType** | Isto será `PUBLICIPADDRESS` sempre. |
| **Operação Nome** | Para relatórios de mitigação, isto `DDoSMitigationReports` será. |
| **Logotipo** | Os valores possíveis `Incremental` incluem, `PostMitigation` . .|
| **MitigaçãoPeriodStart** | A data e a hora na UTC quando a mitigação começou.  |
| **MitigaçãoPeriodEnd** | A data e a hora na UTC quando a mitigação terminou. |
| **IPAddress** | O seu endereço IP público. |
| **AttackVectors** |  Desagregação de tipos de ataque. As chaves `TCP SYN flood` incluem, , , , , . `TCP flood` `UDP flood` `UDP reflection` `Other packet flood` .|
| **TrafficOverview** |  Avaria no tráfego de ataque. As chaves `Total packets` incluem, , , , , , , , `Total packets dropped` , , `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` `Total Other packets dropped` . |
| **Protocolos** | Desagregação dos protocolos envolvidos. As chaves `TCP` `UDP` incluem, . `Other` . |
| **GotaReasons** | Desagregação das razões para os pacotes abandonados. As chaves `Protocol violation invalid TCP syn` incluem, , , , , , , , `Protocol violation invalid TCP` , , , `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` `Packet was forwarded to service` . |
| **TopSourceCountries** | Desagregação dos 10 principais países de origem do tráfego. |
| **TopSourceCountriesForDroppedPackets** | Desagregação dos 10 principais países de origem do tráfego de ataques que é/foi atenuado. |
| **TopSourceASNs** | Repartição dos 10 principais números do sistema autónomo de origem (ASN) do tráfego de entrada.  |
| **FonteContinentes** | Desagregação dos continentes de origem do tráfego. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Permitir o registo de diagnóstico em todos os IPs públicos

Este [modelo](https://aka.ms/ddosdiaglogs) cria uma definição de Política Azure para permitir automaticamente a sessão de diagnóstico em todos os registos IP públicos num âmbito definido.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FPolicy%20-%20DDOS%20Enable%20Diagnostic%20Logging%2FAzure%20Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Ver dados de registo em livros

### <a name="azure-sentinel-data-connector"></a>Conector de dados Azure Sentinel

Pode ligar registos ao Azure Sentinel, visualizar e analisar os seus dados em livros de trabalho, criar alertas personalizados e incorporá-los em processos de investigação. Para ligar ao Azure Sentinel, consulte [Connect to Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Conector Azure Sentinel DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Livro de proteção Azure DDos

Pode utilizar [este modelo Azure Resource Manager (ARM)](https://aka.ms/ddosworkbook) para implementar um livro de análise de ataque. Este livro permite visualizar dados de ataque em vários painéis filtrantes para entender facilmente o que está em jogo. 

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![Livro de Proteção dDos](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Validar e testar

Para simular um ataque DDoS para validar os seus registos, consulte [validar a deteção de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

- Configure registos de diagnóstico DDoS, incluindo notificações, relatórios de mitigação e registos de fluxos de mitigação. 
- Permitir o registo de diagnóstico em todos os IPs públicos num âmbito definido.
- Ver registos de dados em livros de trabalho.

Para aprender a configurar alertas de ataque, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ver e configurar os alertas da proteção contra DDoS](alerts.md)
