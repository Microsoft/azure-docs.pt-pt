---
title: Como configurar a monitorização - Azure Digital Twins / Microsoft Docs
description: Saiba configurar opções de monitorização e registo para As Gémeas Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76511863"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Como configurar a monitorização em Azure Digital Twins

A Azure Digital Twins suporta a exploração madeireira, monitorização e análise robustas. Os desenvolvedores de soluções podem utilizar registos do Monitor Azure, registos de diagnóstico, registos de atividades e outros serviços para suportar as complexas necessidades de monitorização de uma aplicação IoT. As opções de exploração madeireira podem ser combinadas para consultar ou exibir registos em vários serviços e fornecer cobertura de registo granular para muitos serviços.

Este artigo resume opções de registo e monitorização e como combiná-las de formas específicas às Gémeas Digitais Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Rever registos de atividades

Os [registos](../azure-monitor/platform/platform-logs-overview.md) de atividade do Azure fornecem informações rápidas sobre eventos de nível de subscrição e histórias de operação para cada instância de serviço Azure.

Os eventos de nível de subscrição incluem:

* Criação de recursos
* Remoção de recursos
* Criar segredos de aplicativos
* Integração com outros serviços

O registo de atividades para As Gémeas Digitais Azure está ativado por padrão e pode ser encontrado através do portal Azure por:

1. Selecionando a sua instância De Gémeos Digitais Azure.
1. Escolha **o registo de atividade** para elevar o painel de exibição:

    [![Registo de atividades](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Para exploração madeireira de atividade avançada:

1. Selecione a opção **Registos** para visualizar a visão geral do Registo de **Atividade:**

    [![Seleção](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A visão geral do **Registo de Atividades** resume os dados essenciais de registo de atividade:

    [![Visão geral da análise do registo de atividade]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Utilize **registos** de atividade para informações rápidas sobre eventos de nível de subscrição.

## <a name="enable-customer-diagnostic-logs"></a>Ativar registos de diagnóstico do cliente

As [definições](../azure-monitor/platform/platform-logs-overview.md) de diagnóstico do Azure podem ser definidas para cada instância azure para complementar o abate de atividades. Enquanto os registos de atividade dizem respeito a eventos de nível de subscrição, o registo de diagnóstico fornece insights sobre o histórico operacional dos próprios recursos.

Exemplos de registo de diagnóstico incluem:

* O tempo de execução de uma função definida pelo utilizador
* O código de estado de resposta de um pedido de API bem sucedido
* Recuperando uma chave de aplicativo de um cofre

Para ativar registos de diagnóstico, por exemplo:

1. Traga o recurso no portal Azure.
1. Selecione **as definições de diagnóstico:**

    [![Configurações de diagnóstico um](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selecione **ligar os diagnósticos** para recolher dados (se não estiver ativado anteriormente).
1. Preencha os campos solicitados e selecione como e onde os dados serão guardados:

    [![Configurações de diagnóstico dois](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Os registos de diagnóstico são muitas vezes guardados utilizando o Armazenamento de [Ficheiros Azure](../storage/files/storage-files-deployment-guide.md) e partilhados com [registos do Monitor Azure](../azure-monitor/log-query/get-started-portal.md). Ambas as opções podem ser selecionadas.

>[!TIP]
>Utilize **registos de diagnóstico** para informações sobre operações de recursos.

## <a name="azure-monitor-and-log-analytics"></a>Monitor azure e análise de registo

As aplicações IoT unem recursos, dispositivos, locais e dados díspares numa só. A exploração madeireira de grãos finos fornece informações detalhadas sobre cada peça, serviço ou componente específico da arquitetura de aplicação global, mas é frequentemente necessária uma visão geral unificada para manutenção e depuração.

O Azure Monitor inclui o poderoso serviço de análise de registos, que permite visualizar e analisar fontes de registo num só local. O Azure Monitor é, portanto, altamente útil para analisar registos dentro de aplicações ioT sofisticadas.

Exemplos de utilização incluem:

* Consulta de múltiplas histórias de registo de diagnóstico
* Ver registos para várias funções definidas pelo utilizador
* Exibição de registos para dois ou mais serviços dentro de um prazo específico

A consulta completa de registoé fornecida através de [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md). Para configurar estas características poderosas:

1. Procure **o Log Analytics** no portal Azure.
1. Os casos disponíveis do espaço de **trabalho log Analytics** serão apresentados. Escolha um e selecione **Registos** para consulta:

    [![Análise de registo](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Se ainda não tiver uma instância de espaço de **trabalho Log Analytics,** pode criar um espaço de trabalho selecionando o botão **Adicionar:**

    [![Criar OMs](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Uma vez que a sua instância de **espaço de trabalho Log Analytics** é aprovisionada, pode utilizar consultas poderosas para encontrar entradas em registos de múltiplos ou pesquisar usando critérios específicos usando a Gestão de **Registos:**

   [![Gestão de registos](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Para mais informações sobre operações de consulta poderosas, leia [começar com consultas](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Pode sentir um atraso de 5 minutos ao enviar eventos para log **analytics espaço** de trabalho pela primeira vez.

Os registos do Monitor Azure também fornecem poderosos serviços de notificação de erros e alertas, que podem ser vistos selecionando **diagnosticar e resolver problemas:**

   [![Notificações de alerta e erro](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Utilize o espaço de **trabalho do Log Analytics** para consultar histórias de registo para várias funcionalidades, subscrições ou serviços de aplicações.

## <a name="other-options"></a>Outras opções

A Azure Digital Twins também suporta a sinalização de registo e auditoria de segurança específicas da aplicação. Para uma visão geral completa de todas as opções de exploração madeireira Azure disponíveis para a sua instância Azure Digital Twins, leia o artigo de auditoria de [log Azure.](../security/fundamentals/log-audit.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os registos](../azure-monitor/platform/platform-logs-overview.md)de atividade do Azure.

- Mergulhe mais nas definições de diagnóstico do Azure, lendo uma [visão geral dos registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md).

- Leia mais sobre [os registos do Monitor Azure](../azure-monitor/log-query/get-started-portal.md).
