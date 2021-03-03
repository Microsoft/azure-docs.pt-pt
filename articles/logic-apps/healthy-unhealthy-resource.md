---
title: Configurar o registo para monitorizar aplicações lógicas no Azure Security Center
description: Monitorize a saúde dos seus recursos de Aplicações Lógicas no Azure Security Center, criando um registo de diagnóstico.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712390"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Configurar o registo para monitorizar aplicações lógicas no Azure Security Center

Quando monitoriza os recursos das suas Aplicações Lógicas no [Microsoft Azure Security Center,](../security-center/security-center-introduction.md)pode [rever se as suas aplicações lógicas estão a seguir as políticas predefinidas.](#view-logic-apps-health-status) O Azure mostra o estado de saúde de um recurso de Aplicações Lógicas depois de ativar o registo e configurar corretamente o destino dos registos. Este artigo explica como configurar a gravação de diagnóstico e certificar-se de que todas as suas aplicações lógicas são recursos saudáveis.

> [!TIP]
> Para encontrar o estado atual do serviço De aplicações lógicas, reveja a [página de status do Azure,](https://status.azure.com/)que lista o estado dos diferentes produtos e serviços em cada região disponível.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Aplicativos lógicos existentes com [registo de diagnóstico ativado](#enable-diagnostic-logging).
* Um espaço de trabalho Log Analytics, que é necessário para permitir o registo para a sua aplicação lógica. Se não tiver um espaço de trabalho, primeiro [crie o seu espaço de trabalho.](../azure-monitor/logs/quick-create-workspace.md)

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnósticos

Antes de poder ver o estado de saúde dos recursos para as suas aplicações lógicas, tem primeiro de [configurar a registo de diagnóstico](monitor-logic-apps-log-analytics.md). Se já tiver um espaço de trabalho log Analytics, pode ativar o registo quando cria a sua aplicação lógica ou em aplicações lógicas existentes.

> [!TIP]
> A recomendação padrão é ativar registos de diagnóstico para Aplicações Lógicas. No entanto, controla esta definição para as suas aplicações lógicas. Quando ativar registos de diagnóstico para as suas aplicações lógicas, pode utilizar as informações para ajudar a analisar incidentes de segurança.

### <a name="check-diagnostic-logging-setting"></a>Verifique a definição de registo de diagnóstico

Se não tiver a certeza se as suas aplicações lógicas têm registo de diagnóstico ativado, pode consultar o Centro de Segurança:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, insira e selecione **o Centro de Segurança.**
1. No menu do painel do Centro de Segurança, em **Geral,** selecione **Recomendações**.
1. Na tabela de sugestões de segurança, encontrar e selecionar Ative os registos **de auditoria e de registo** de diagnóstico em &gt; **Aplicações Lógicas deve ser ativado** na tabela de controlos de segurança.
1. Na página de recomendação, expanda a secção **de passos de Remediação** e reveja as opções. Pode ativar os diagnósticos de Aplicações Lógicas selecionando o **Quick Fix!** botão, ou seguindo as instruções de reparação manual.

## <a name="view-logic-apps-health-status"></a>Ver estado de saúde das aplicações lógicas

Depois de ter [ativado a sessão de diagnóstico,](#enable-diagnostic-logging)pode ver o estado de saúde das suas aplicações lógicas no Security Center.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, insira e selecione **o Centro de Segurança.**
1. No menu do painel do Centro de Segurança, em **Geral,** selecione **Inventário**.
1. Na página de inventário, filtre a sua lista de ativos para mostrar apenas recursos de Aplicações Lógicas. No menu da página, selecione **aplicações lógicas de tipos de** &gt; recursos.

   O balcão **dos Recursos Insalubres** mostra o número de aplicações lógicas que o Security Center considera insalubres.
1.  Na lista de recursos de aplicações lógicas, reveja a coluna **Recomendações.** Para rever os detalhes de saúde de uma aplicação lógica específica, selecione um nome de recurso ou selecione o botão elipses **(...**) &gt; **Ver recurso**.
1.  Para remediar eventuais problemas de saúde em recursos, siga os passos listados para as suas aplicações lógicas.

Se o registo de diagnóstico já estiver ativado, pode haver um problema com o destino dos seus registos. [Reveja como corrigir problemas com diferentes destinos de registo de diagnóstico](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Corrigir registos de diagnóstico para aplicações lógicas

Se as suas [aplicações lógicas estiverem listadas como insalubres no Security Center,](#view-logic-apps-health-status)abra a sua aplicação lógica no Code View no portal Azure ou através do Azure CLI. Em seguida, verifique a configuração de destino para os seus registos de diagnóstico: [Azure Log Analytics,](#log-analytics-and-event-hubs-destinations) [Azure Event Hubs,](#log-analytics-and-event-hubs-destinations)ou [uma conta de Armazenamento Azure](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics e destinos de Centros de Eventos

Se utilizar o Log Analytics ou Os Centros de Eventos como destino para os seus registos de diagnóstico de Aplicações Lógicas, verifique as seguintes definições. 

1. Para confirmar que ativou registos de diagnóstico, verifique se o campo de definições de diagnóstico `logs.enabled` está definido para `true` . 
1. Para confirmar que não definiu uma conta de armazenamento como destino, verifique se o `storageAccountId` campo está definido para `false` .

Por exemplo:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Destino da conta de armazenamento

Se utilizar uma conta de armazenamento como destino para os seus registos de diagnóstico de Aplicações Lógicas, verifique as seguintes definições.

1. Para confirmar que ativou registos de diagnóstico, verifique se o campo de definições de diagnóstico `logs.enabled` está definido para `true` .
1. Para confirmar que ativou uma política de retenção para os seus registos de diagnóstico, verifique se o `retentionPolicy.enabled` campo está definido para `true` .
1. Para confirmar que definiu um tempo de retenção de 0-365 dias, verifique se o `retentionPolicy.days` campo está definido para um número inclusivamente entre 0 e 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```