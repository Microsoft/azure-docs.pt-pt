---
title: Como configurar o monitoramento no Azure digital gêmeos | Microsoft Docs
description: Como configurar o monitoramento no Azure digital gêmeos.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: d0f5f9a1d488b6be8ca91fdd057880e351ae0b3f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261031"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Como configurar o monitoramento no Azure digital gêmeos

O Azure digital gêmeos dá suporte ao registro em log, monitoramento e análise robustos. As soluções que os desenvolvedores podem usar Azure Monitor logs, logs de diagnóstico, log de atividades e outros serviços para dar suporte às necessidades complexas de monitoramento de um aplicativo de IoT. As opções de log podem ser combinadas para consultar ou exibir registros em vários serviços e para fornecer cobertura de log granular para muitos serviços.

Este artigo resume as opções de registro em log e monitoramento e como combiná-las de formas específicas ao Azure digital gêmeos.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Examinar logs de atividade

[Os logs de atividades](../azure-monitor/platform/activity-logs-overview.md) do Azure fornecem informações rápidas sobre os históricos de eventos e operações de nível de assinatura para cada instância de serviço do Azure.

Os eventos de nível de assinatura incluem:

* Criação de recursos
* Remoção de recursos
* Criando segredos do aplicativo
* Integração com outros serviços

O log de atividades do Azure digital gêmeos é habilitado por padrão e pode ser encontrado por meio do portal do Azure por:

1. Selecionando sua instância de gêmeos digital do Azure.
1. Escolhendo o **log de atividades** para abrir o painel de exibição:

    [![Log de atividades](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Para o registro em log de atividades avançadas:

1. Selecione a opção **logs** para exibir a **análise do log de atividades visão geral**:

    [![Selecionado](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. A **análise do log de atividades visão geral** resume os dados essenciais do log de atividades:

    [![Visão geral do log Analytics de atividades]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Use **os logs de atividade** para obter informações rápidas sobre eventos no nível da assinatura.

## <a name="enable-customer-diagnostic-logs"></a>Habilitar logs de diagnóstico do cliente

[As configurações de diagnóstico](../azure-monitor/platform/resource-logs-overview.md) do Azure podem ser definidas para cada instância do Azure para complementar o log de atividades. Embora os logs de atividade pertençam a eventos de nível de assinatura, o log de diagnóstico fornece informações sobre o histórico operacional dos próprios recursos.

Exemplos de log de diagnóstico incluem:

* O tempo de execução para uma função definida pelo usuário
* O código de status de resposta de uma solicitação de API bem-sucedida
* Recuperando uma chave de aplicativo de um cofre

Para habilitar os logs de diagnóstico para uma instância do:

1. Ative o recurso em portal do Azure.
1. Selecione **configurações de diagnóstico**:

    [![Configurações de diagnóstico um](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selecione **Ativar diagnóstico** para coletar dados (se não tiver sido habilitado anteriormente).
1. Preencha os campos solicitados e selecione como e onde os dados serão salvos:

    [![Configurações de diagnóstico duas](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Os logs de diagnóstico são frequentemente salvos usando o [armazenamento de arquivos do Azure](../storage/files/storage-files-deployment-guide.md) e compartilhados com [logs de Azure monitor](../azure-monitor/log-query/get-started-portal.md). Ambas as opções podem ser selecionadas.

>[!TIP]
>Use **os logs de diagnóstico** para obter informações sobre operações de recursos.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor e log Analytics

Os aplicativos IoT unitáriam recursos, dispositivos, locais e dados diferentes em um. O registro em log refinado fornece informações detalhadas sobre cada parte, serviço ou componente específico da arquitetura geral do aplicativo, mas uma visão geral unificada geralmente é necessária para manutenção e depuração.

Azure Monitor inclui o poderoso serviço log Analytics, que permite que as fontes de log sejam exibidas e analisadas em um único local. O Azure Monitor, portanto, é altamente útil para analisar logs em aplicativos IoT sofisticados.

Exemplos de uso incluem:

* Consultando vários históricos de log de diagnóstico
* Vendo logs para várias funções definidas pelo usuário
* Exibindo logs para dois ou mais serviços dentro de um período de tempo específico

A consulta de log completa é fornecida por meio de [logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md). Para configurar esses recursos avançados:

1. Procure **log Analytics** no portal do Azure.
1. Você verá suas instâncias de **espaço de trabalho log Analytics** disponíveis. Escolha um e selecione **os logs** a serem consultados:

    [![Análise de log](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Se você ainda não tiver uma instância de **espaço de trabalho log Analytics** , poderá criar um espaço de trabalho selecionando o botão **Adicionar** :

    [![Criar OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Depois que a instância do **espaço de trabalho do log Analytics** for provisionada, você poderá usar consultas poderosas para localizar entradas em vários logs ou Pesquisar usando critérios específicos usando o gerenciamento de **log**:

   [![Gerenciamento de log](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Para obter mais informações sobre operações de consulta avançadas, consulte [introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Você pode experimentar um atraso de 5 minutos ao enviar eventos para **log Analytics espaço de trabalho** pela primeira vez.

Os logs de Azure Monitor também fornecem serviços de notificação de erro e alerta avançados, que podem ser exibidos selecionando **diagnosticar e resolver problemas**:

   [![Alertas e notificações de erro](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Use **log Analytics espaço de trabalho** para consultar históricos de log para várias funcionalidades, assinaturas ou serviços do aplicativo.

## <a name="other-options"></a>Outras opções

O Azure digital gêmeos também dá suporte ao log específico do aplicativo e à auditoria de segurança. Para obter uma visão geral completa de todas as opções de log do Azure disponíveis para sua instância do gêmeos digital do Azure, consulte o artigo [auditoria de log do Azure](../security/fundamentals/log-audit.md) .

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os logs de atividades](../azure-monitor/platform/activity-logs-overview.md)do Azure.

- Aprofunde-se nas configurações de diagnóstico do Azure lendo uma [visão geral dos logs de diagnóstico](../azure-monitor/platform/resource-logs-overview.md).

- Leia mais sobre [logs de Azure monitor](../azure-monitor/log-query/get-started-portal.md).
