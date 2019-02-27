---
title: Azure Stream dados para os Hubs de eventos de monitorização
description: Aprenda a transmitir seus dados de monitorização do Azure para um hub de eventos para obter os dados num parceiro SIEM ou a ferramenta de análise.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 549ec74514ff03e06ff25893d3fa865f179470e9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870691"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Azure Stream a monitorização dos dados para um hub de eventos para consumo por uma ferramenta externa

Este artigo explica como configurar diferentes camadas de dados do seu ambiente do Azure, para serem enviados dos Hubs de eventos espaço de nomes ou event hub individual, onde podem ser coletado por uma ferramenta externa.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Os dados que pode enviar para um hub de eventos?

No seu ambiente do Azure, existem vários "escalões" de dados de monitorização e o método de acesso aos dados de cada camada varia um pouco. Normalmente, estas camadas podem ser descritas como:

- **Dados de monitorização da aplicação:** Dados sobre o desempenho e a funcionalidade do código que escrevi e forem executadas no Azure. Exemplos de dados de monitorização de aplicações incluem rastreios de desempenho, registos de aplicações e telemetria de utilizador. Dados de monitorização da aplicação, normalmente, é recolhida em uma das seguintes formas:
  - Por instrumentar o seu código com um SDK, tais como o [SDK do Application Insights](../../azure-monitor/app/app-insights-overview.md).
  - Executando um agente de monitorização que escuta para registos de nova aplicação na máquina de execução da sua aplicação, tais como o [o agente de diagnóstico do Windows Azure](./../../azure-monitor/platform/diagnostics-extension-overview.md) ou [o agente de diagnóstico do Linux Azure](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dados de monitorização de SO convidado:** Dados sobre o sistema operativo no qual a sua aplicação está em execução. Exemplos de dados de monitorização de SO convidado seria Linux syslog ou eventos de sistema do Windows. Para recolher este tipo de dados, tem de instalar um agente, como o [o agente de diagnóstico do Windows Azure](./../../azure-monitor/platform/diagnostics-extension-overview.md) ou [o agente de diagnóstico do Linux Azure](../../virtual-machines/extensions/diagnostics-linux.md).
- **Recursos do Azure, os dados de monitorização:** Dados sobre o funcionamento de um recurso do Azure. Para alguns tipos de recursos do Azure, como máquinas virtuais, existe um SO convidado e aplicações para monitorizar dentro desse serviço do Azure. Outros recursos do Azure, tais como grupos de segurança de rede, o recurso de dados de monitorização é o escalão mais elevado de dados disponíveis (uma vez que não existe nenhum SO convidado ou a aplicação em execução nesses recursos). Estes dados podem ser coletados com [definições de diagnóstico de recursos](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
- **Subscrição do Azure, os dados de monitorização:** Dados sobre a operação e a gestão de uma subscrição do Azure, bem como dados sobre o estado de funcionamento e a operação do Azure em si. O [registo de atividades](./../../azure-monitor/platform/activity-logs-overview.md) contém a subscrição a maioria dos dados, tais como incidentes de estado de funcionamento do serviço e do Azure Resource Manager auditorias de monitorização. Pode recolher estes dados a utilizar um perfil de registo.
- **Dados de monitorização de um inquilino do Azure:** Dados sobre o funcionamento dos serviços do Azure ao nível do inquilino, como o Azure Active Directory. Auditorias do Azure Active Directory e inícios de sessão são exemplos de dados de monitorização de inquilino. Estes dados podem ser recolhidos com uma definição de diagnóstico do inquilino.

Dados a partir de qualquer camada podem ser enviados para um hub de eventos, onde pode ser extraída para uma ferramenta de parceiro. Algumas origens podem ser configuradas para enviar dados diretamente para um hub de eventos, enquanto outro processo, como uma aplicação lógica pode ser necessária para recuperar os dados necessários. As secções seguintes descrevem como configurar dados de cada camada para ser transmitido para um hub de eventos. Os passos partem do princípio de que já tem ativos nessa camada para serem monitorizados.

## <a name="set-up-an-event-hubs-namespace"></a>Configurar um espaço de nomes de Hubs de eventos

Antes de começar, precisa [criar um hub de eventos e de espaço de nomes de Hubs de eventos](../../event-hubs/event-hubs-create.md). Este hub de eventos e de espaço de nomes é o destino para todos os seus dados de monitorização. Um espaço de nomes de Hubs de eventos é um agrupamento lógico de hubs de eventos que partilham a mesma política de acesso, bem como um armazenamento de conta tem blobs individuais dentro dessa conta de armazenamento. Tenha em atenção de alguns detalhes sobre o espaço de nomes de hubs de eventos e os hubs de eventos que criou:
* Recomendamos que utilize um espaço de nomes de Hubs de eventos Standard.
* Normalmente, apenas uma unidade de débito é necessária. Se precisar de aumentar verticalmente à medida que aumenta de utilização do log, sempre manualmente pode aumentar o número de unidades de débito para o espaço de nomes mais tarde ou ativar inflação automática.
* O número de unidades de débito permite-lhe aumentar a escala de débito para os hubs de eventos. O número de partições permite-lhe a paralelização de consumo entre os muitos consumidores. Uma única partição pode ser feito até 20MBps, ou aproximadamente 20 000 mensagens por segundo. Consoante a ferramenta de consumir os dados, ela poderá ou poderá não suporta o consumo de várias partições. Se não tiver a certeza sobre o número de partições para definir, recomendamos que comece com quatro partições.
* Recomendamos que defina a retenção de mensagens no seu hub de eventos a 7 dias. Se a sua ferramenta de consumo ficar inoperacional durante mais de um dia, isto garante que a ferramenta pode começar onde terminou (para eventos até 7 dias).
* Recomendamos que utilize o grupo de consumidores predefinido para o seu hub de eventos. Não é necessário para criar outros grupos de consumidores ou utilizar um grupo de consumidores separado, a menos que planeia ter duas ferramentas diferentes consumir os mesmos dados do hub de eventos mesmo.
* Para o registo de atividades do Azure, escolhe um espaço de nomes de Hubs de eventos e Monitor do Azure cria um hub de eventos dentro desse namespace chamado "insights-logs-operationallogs." Para outros tipos de registo, pode optar por um hub de eventos existente (permitindo-lhe reutilizar o mesmo hub de eventos de insights-logs-operationallogs) ou tem de criar um hub de eventos por categoria de registo do Azure Monitor.
* Normalmente, a porta 5671 e 5672 têm de ser aberta na máquina de consumo de dados do hub de eventos.

Consulte também os [FAQ de Hubs de eventos do Azure](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>A monitorização dos dados de inquilino do Azure

A monitorização dos dados de inquilino do Azure está atualmente disponível apenas para o Azure Active Directory. Pode utilizar os dados a partir [do Azure Active Directory reporting](../../active-directory/reports-monitoring/overview-reports.md), que contém o histórico de início de sessão atividade e auditoria do registo de alterações feitas a um inquilino específico.

### <a name="azure-active-directory-data"></a>Dados do Azure Active Directory

Para enviar dados de registo do Azure Active Directory num espaço de nomes de Hubs de eventos, configurou uma definição de diagnóstico do inquilino no seu inquilino do AAD. [Siga este guia](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) para configurar uma definição de diagnóstico do inquilino.

## <a name="azure-subscription-monitoring-data"></a>A monitorização dos dados de subscrição do Azure

A monitorização dos dados de subscrição do Azure está disponível na [registo de atividades do Azure](./../../azure-monitor/platform/activity-logs-overview.md). Contém o criar, atualizar e eliminar operações do Resource Manager, as alterações nos [estado de funcionamento do serviço do Azure](../../service-health/service-health-overview.md) que pode afetar recursos na sua subscrição, o [estado de funcionamento do recurso](../../service-health/resource-health-overview.md) Estado transições e vários outros tipos de eventos de nível de assinatura. [Este artigo fornece detalhes sobre todas as categorias de eventos que aparecem no registo de atividades do Azure](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Dados de registo de atividade

Para enviar dados de registo de atividades do Azure para um espaço de nomes de Hubs de eventos, configurar um perfil de registo na sua subscrição. [Siga este guia](./activity-logs-stream-event-hubs.md) para configurar um perfil de registo na sua subscrição. Opte por fazê-lo uma vez por subscrição que pretende monitorizar.

> [!TIP]
> Um perfil de registo atualmente só permite-lhe selecionar um espaço de nomes de Hubs de eventos, no qual um hub de eventos é criado com os nome "insights-operational-logs." Ele não é possível especificar o seu próprio nome de hub de eventos num perfil de registo.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Registos de diagnóstico e métricas de recursos do Azure

Recursos do Azure emitem dois tipos de dados de monitorização:
1. [Registos de diagnóstico de recursos](./../../azure-monitor/platform/diagnostic-logs-overview.md)
2. [Métricas](../../azure-monitor/platform/data-collection.md)

Ambos os tipos de dados são enviados para um hub de eventos com uma definição de diagnóstico de recursos. [Siga este guia](./../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) para configurar uma definição de diagnóstico de recursos num determinado recurso. Configurar uma definição de diagnóstico de recursos em cada recurso do qual pretende recolher registos.

> [!TIP]
> Pode utilizar a política do Azure para se certificar de que todos os recursos dentro de um determinado escopo sempre está configurado com uma definição de diagnóstico [utilizando o efeito de DeployIfNotExists na regra de política](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="guest-os-data"></a>Dados de SO convidado

Tem de instalar um agente para enviar dados de monitorização do SO do convidado para um hub de eventos. Para Windows ou Linux, especifique os dados que pretende que sejam enviados para o hub de eventos, bem como o hub de eventos para o qual os dados devem ser enviados num ficheiro de configuração e passam esse ficheiro de configuração para o agente em execução na VM.

### <a name="linux-data"></a>Dados do Linux

O [agente Linux do Azure diagnóstico](../../virtual-machines/extensions/diagnostics-linux.md) pode ser utilizado para enviar dados a partir de uma máquina Linux para um hub de eventos de monitorização. Faça isso adicionando o hub de eventos como um sink numa sua LAD definições de ficheiro protegido de configuração JSON. [Veja este artigo para saber mais sobre como adicionar o coletor de hub de eventos para o agente de diagnóstico do Linux do Azure](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Não é possível configurar a transmissão em fluxo de dados de monitorização de SO de convidado para um hub de eventos no portal. Em vez disso, tem de editar manualmente o ficheiro de configuração.

### <a name="windows-data"></a>Dados do Windows

O [agente do diagnóstico do Windows Azure](./../../azure-monitor/platform/diagnostics-extension-overview.md) pode ser utilizado para enviar dados a partir de uma máquina do Windows para um hub de eventos de monitorização. Fazer isso adicionando o hub de eventos como um sink numa secção sua privateConfig do ficheiro de configuração WAD. [Veja este artigo para saber mais sobre como adicionar o coletor de hub de eventos para o agente de diagnóstico do Windows Azure](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Não é possível configurar a transmissão em fluxo de dados de monitorização de SO de convidado para um hub de eventos no portal. Em vez disso, tem de editar manualmente o ficheiro de configuração.

## <a name="application-monitoring-data"></a>Dados de monitorização de aplicações

Dados de monitorização de aplicações requer que seu código está equipado com um SDK, então não é uma solução para fins gerais para dados para um hub de eventos no Azure de monitorização de aplicações de encaminhamento. No entanto, [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço que pode ser utilizado para recolher dados de nível de aplicativo do Azure. Se estiver a utilizar o Application Insights, pode transmitir em fluxo com dados de monitorização para um hub de eventos, fazendo o seguinte:

1. [Configurar a exportação contínua](../../azure-monitor/app/export-telemetry.md) os dados do Application Insights para uma conta de armazenamento.

2. Configurar uma aplicação de lógica acionada por temporizador que [extrai dados de armazenamento de BLOBs](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [envia-o como uma mensagem para o hub de eventos](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>O que posso fazer com os dados de monitorização a ser enviados para o meu hub de eventos?

Encaminhamento seus dados de monitorização para um hub de eventos com o Azure Monitor permite-lhe integrar facilmente com parceiros SIEM e ferramentas de monitorização. A maioria das ferramentas requerem a cadeia de ligação do hub de eventos e determinadas permissões à sua subscrição do Azure para ler dados do hub de eventos. Aqui está uma lista parcial das ferramentas com a integração do Azure Monitor:

* **IBM QRadar** -o Microsoft Azure DSM e protocolo de Hub de eventos do Microsoft Azure estão disponíveis para transferência a partir [o site de suporte da IBM](https://www.ibm.com/support). Pode [Saber mais sobre a integração com o Azure aqui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** -consoante a configuração do Splunk, há duas abordagens:
    1. [O suplemento de Monitor do Azure para o Splunk](https://splunkbase.splunk.com/app/3534/) está disponível em Splunkbase e um projeto de código-fonte aberto. [A documentação está aqui](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Se não é possível instalar um suplemento na sua instância do Splunk (ex. Se utilizar um proxy ou em execução na Splunk Cloud), pode reencaminhar estes eventos para o coletor de eventos do Splunk HTTP usando [essa função que é acionada por mensagens novas no hub de eventos](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** -instruções sobre como configurar SumoLogic consumir dados de um hub de eventos é [disponível aqui](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)
* **ArcSight** -conector de inteligente o Hub de eventos do Azure ArcSight está disponível como parte da [aqui a coleção de conector inteligente ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852).
* **Servidor syslog** - se de que pretende transmitir dados do Azure Monitor diretamente para um servidor syslog, pode consultar [deste repositório do GitHub](https://github.com/miguelangelopereira/azuremonitor2syslog/).

## <a name="next-steps"></a>Próximos Passos
* [Arquive o registo de atividades para uma conta de armazenamento](../../azure-monitor/platform/archive-activity-log.md)
* [Leia a visão geral do registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Configurar um alerta com base num evento do registo de atividade](../../azure-monitor/platform/alerts-log-webhook.md)


