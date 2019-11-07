---
title: Coletar log de atividades do Azure com configurações de diagnóstico (versão prévia) – Azure Monitor | Microsoft Docs
description: Use as configurações de diagnóstico para encaminhar logs de atividades do Azure para Azure Monitor logs, armazenamento do Azure ou hubs de eventos do Azure.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587982"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Coletar log de atividades do Azure com configurações de diagnóstico (versão prévia)
O [log de atividades do Azure](activity-logs-overview.md) é um [log de plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. Até agora, você criou um perfil de log para enviar entradas do log de atividades a [um hub de eventos ou uma conta de armazenamento](activity-log-export.md) e usou um conector para coletá-las em um [espaço de trabalho log Analytics](activity-log-collect.md).

Agora você pode configurar a coleta do log de atividades do Azure usando as mesmas [configurações de diagnóstico](diagnostic-settings.md) usadas para coletar [logs de recursos](resource-logs-overview.md). O uso de configurações de diagnóstico tem as seguintes vantagens em relação aos métodos atuais:

- Método consistente para coletar todos os logs de plataforma.
- Coletar log de atividades entre várias assinaturas e locatários.
- Filtrar coleção para coletar somente logs para categorias específicas.

## <a name="considerations"></a>Considerações
Considere os seguintes detalhes da coleta de log de atividades usando configurações de diagnóstico antes de habilitar esse recurso.

- Você deve desabilitar a coleta existente da atividade antes de habilitá-la usando as configurações de diagnóstico. Ter ambos habilitados pode resultar em dados duplicados.
- A configuração de retenção para coletar o log de atividades para o armazenamento do Azure foi removida, o que significa que os dados serão armazenados indefinidamente até que você o remova.
- Você pode enviar o log de atividades de várias assinaturas para um único espaço de trabalho Log Analytics. Você pode enviar o log de atividades de qualquer assinatura única para até cinco espaços de trabalho do Log Analytics.

## <a name="configure-diagnostic-settings"></a>Definir configurações de diagnóstico
Use o procedimento a seguir para criar uma configuração de diagnóstico no portal do Azure para coletar o log de atividades do Azure. No momento, não é possível criar uma configuração de diagnóstico de nível de assinatura usando outros métodos.

1. Desabilitar a coleção de espaço de trabalho Log Analytics existente para o log de atividades:
   1. Abra o menu **log Analytics espaços de trabalho** na portal do Azure e selecione o espaço de trabalho para coletar o log de atividades.
   2. Na seção **fontes de dados de espaço de trabalho** do menu do espaço de trabalho, selecione **log de atividades do Azure**.
   3. Clique na assinatura que você deseja desconectar.
   4. Clique em **Desconectar** e em **Sim** quando solicitado a confirmar sua escolha.
2. No menu **Azure monitor** na portal do Azure, selecione log de **atividades**.
3. Clique em **Definições de diagnóstico**.
   
   ![Definições de diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Clique na faixa roxa para a experiência herdada e desabilite qualquer coleção atual para o armazenamento ou hubs de eventos. 

    ![Experiência herdada](media/diagnostic-settings-subscription/legacy-experience.png)

5. Siga os procedimentos em [criar configurações de diagnóstico no portal do Azure](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) para criar uma configuração de diagnóstico. Consulte [categorias no log de atividades](activity-logs-overview.md#categories-in-the-activity-log) para obter uma explicação das categorias que você pode usar para filtrar eventos do log de atividades. 


## <a name="differences-in-data"></a>Diferenças nos dados
As configurações de diagnóstico coletam os mesmos dados que os métodos anteriores usados para coletar o log de atividades com as seguintes diferenças atuais:

As seguintes propriedades foram removidas:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

As seguintes propriedades foram adicionadas:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>Solução de monitoramento do log de atividades
A solução de monitoramento de Log Analytics do Azure inclui várias consultas de log e exibições para analisar os registros de log de atividades em seu espaço de trabalho do Log Analytics. Essa solução usa os dados de log coletados em um espaço de trabalho Log Analytics e continuará a funcionar sem nenhuma alteração se você coletar o log de atividades usando as configurações de diagnóstico. Consulte [solução de monitoramento de análise de logs de atividade](activity-log-collect.md#activity-logs-analytics-monitoring-solution) para obter detalhes sobre esta solução.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o log de atividades](../../azure-resource-manager/resource-group-audit.md)
* [Saiba mais sobre as configurações de diagnóstico](diagnostic-settings.md)
