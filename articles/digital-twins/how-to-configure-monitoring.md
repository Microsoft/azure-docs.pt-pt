---
title: Como configurar a monitorização no duplos Digital do Azure | Documentos da Microsoft
description: Como configurar a monitorização no duplos Digital do Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 5dc2587a0c127106d5afb41e20eca43919065f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118790"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Como configurar a monitorização no duplos Digital do Azure

Duplos Digital do Azure suporta registo robusto, monitorização e análise. Os desenvolvedores de soluções podem utilizar os registos do Azure Monitor, registos de diagnóstico, registo de atividade e outros serviços para suportar as necessidades complexas de monitorização de uma aplicação IoT. Opções de registo podem ser combinadas para consultar ou apresentar registos em vários serviços e fornecer cobertura de Registro em log granular para muitos serviços.

Este artigo resume o registo e monitorização de opções e como combiná-los de formas específicas para Twins Digital do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Registos de atividades de revisão

Azure [registos de atividades](../azure-monitor/platform/activity-logs-overview.md) fornecem informações rápidas sobre históricos de evento e a operação de nível de assinatura para cada instância de serviço do Azure.

Eventos de nível de assinatura incluem:

* Criação de recursos
* Remoção de recursos
* Criação de segredos de aplicação
* Integrar com outros serviços

Registo de atividade duplos Digital do Azure está ativado por predefinição e pode ser encontrado através do portal do Azure ao:

1. Selecionar a instância de duplos Digital do Azure.
1. Escolher **registo de atividades** para abrir o painel de exibição:

    [![Registo de atividades](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Para o registo de atividade avançadas:

1. Selecione o **Logs** opção para apresentar o **descrição geral da análise de registo de atividade**:

    [![Seleção](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. O **descrição geral da análise de registo de atividade** resume os dados de registo de atividade essencial:

    [![Descrição geral da análise de registo de atividade]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Uso **registos de atividades** para as informações rápidas sobre os eventos de nível de assinatura.

## <a name="enable-customer-diagnostic-logs"></a>Ativar registos de diagnóstico do cliente

Azure [das definições de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) podem ser definidos para cada instância do Azure complementar o registo de atividade. Enquanto os registos de atividades que dizem respeito a eventos de nível de assinatura, o registo de diagnósticos fornece informações sobre o histórico de operacional dos próprios recursos.

Exemplos de registo de diagnósticos:

* O tempo de execução para uma função definida pelo utilizador
* O código de estado de resposta de um pedido de API com êxito
* Obter uma chave de aplicação a partir de um cofre

Para ativar os registos de diagnóstico para uma instância:

1. Abra o recurso no portal do Azure.
1. Selecione **das definições de diagnóstico**:

    [![Definições de diagnóstico um](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selecione **ativar os diagnósticos** para recolher dados (se ainda não ativada).
1. Preencha os campos de pedido e selecione como e onde os dados serão guardados:

    [![Duas definições de diagnóstico](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Os registos de diagnóstico são, muitas vezes, salvo usando [File Storage do Azure](../storage/files/storage-files-deployment-guide.md) e partilhado com [registos do Azure Monitor](../azure-monitor/log-query/get-started-portal.md). Ambas as opções podem ser selecionadas.

>[!TIP]
>Uso **registos de diagnóstico** para informações sobre operações de recursos.

## <a name="azure-monitor-and-log-analytics"></a>Análise de registo e monitor do Azure

Aplicações de IoT unam-se diferentes recursos, dispositivos, locais e dados em um. Registro em log detalhado fornece informações detalhadas sobre cada parte específica, o serviço ou o componente da arquitetura geral do aplicativo, mas uma visão unificada, muitas vezes, é necessária para manutenção e a depuração.

Monitor do Azure inclui o serviço de análise do registo poderosa, que permite o registo de origens para ser visualizado e analisados num único local. O Azure Monitor, por conseguinte, é extremamente útil para analisar os registos dentro sofisticados aplicações de IoT.

Exemplos de utilização incluem:

* Consultar vários históricos de registo de diagnóstico
* Ver registos para várias funções definidas pelo utilizador
* Exibindo logs para dois ou mais serviços dentro de um período de tempo específico

Consultar o registo completo é fornecido através de [registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md). Para configurar estas funcionalidades poderosas:

1. Procure **do Log Analytics** no portal do Azure.
1. Verá sua disponíveis **área de trabalho do Log Analytics** instâncias. Escolha uma e selecione **registos** a consulta:

    [![O log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Se ainda não tiver uma **área de trabalho do Log Analytics** instância, pode criar uma área de trabalho ao selecionar o **Add** botão:

    [![Criar o OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Uma vez sua **área de trabalho do Log Analytics** instância é aprovisionada, pode utilizar consultas eficientes para encontrar entradas nos registos de múltiplos ou a procura utilizando critérios específicos usando **gestão de registos**:

   [![Gestão de registos](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Para obter mais informações sobre as operações de consulta poderosa, consulte [introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Poderá haver um atraso de 5 minutos, ao enviar eventos para **área de trabalho do Log Analytics** pela primeira vez.

Registos de Monitor do Azure também fornecem erro poderosas e serviços de notificação de alerta, o que podem ser visualizados selecionando **diagnosticar e resolver problemas**:

   [![Notificações de alerta e de erro](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Uso **área de trabalho do Log Analytics** para históricos de log de consulta para as várias funcionalidades de aplicação, subscrições ou serviços.

## <a name="other-options"></a>Outras opções

Duplos Digital do Azure também suporta o registo específico de aplicação e auditoria de segurança. Para obter uma descrição geral completa de todas as opções de registo do Azure disponíveis para a sua instância de duplos Digital do Azure, consulte a [auditoria de registos do Azure](../security/azure-log-audit.md) artigo.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o Azure [registos de atividades](../azure-monitor/platform/activity-logs-overview.md).

- Aprofunde-se em definições de diagnóstico do Azure, lendo um [descrição geral dos registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md).

- Leia mais sobre [registos do Azure Monitor](../azure-monitor/log-query/get-started-portal.md).
