---
title: Migrar para o Monitor de Ligação (Pré-visualização) do Monitor de Ligação
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o Monitor de Ligação (Pré-visualização) a partir do Monitor de Ligação.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701840"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrar para o Monitor de Ligação (Pré-visualização) do Monitor de Ligação

Pode migrar os monitores de ligação existentes para o novo e melhorado Monitor de Ligação (Preview) num clique e com zero tempo de inatividade. Para saber mais sobre os benefícios, pode ler [o Monitor de Ligação (Pré-visualização)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Pontos-chave a notar

* Agentes e definições de firewall funcionarão como está (sem necessidade de toque) 
* Os monitores de ligação existentes serão mapeados para o grupo de teste de > ->->. Os utilizadores podem clicar em *Editar* para visualizar e modificar propriedades do novo Monitor de Ligação e modelo de descarregamento para escoar alterações no Monitor de Ligação e submetê-lo através do Azure Resource Manager. 
* As máquinas virtuais Azure com extensão do Network Watcher enviam dados tanto para o espaço de trabalho como para as métricas. Os Monitores de Ligação disponibilizarão os dados através das novas métricas (ChecksFailedPercent (Preview) e RoundTripTimeMs (Preview)) em vez das métricas antigas de stop (ProbesFailedPercent e AverageRoundtripMs) 
* Monitorizar dados
    * Alertas – Será migrado para novas métricas como parte da migração
    * Dashboards e Integrações – Terá de editar manualmente as métricas definidas. 
    
## <a name="prerequisites"></a>Pré-requisitos

Se utilizar espaço de trabalho personalizado, certifique-se de que o Observador de Rede está ativado na subscrição e na região do espaço de trabalho Log Analytics 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Passos para migrar do Monitor de Ligação para o Monitor de Ligação (Pré-visualização)

1. Clique em "Monitor de Ligação", navegue em "Migrar monitores de ligação" para migrar monitores de ligação de solução mais antiga para mais recente.

    ![Screenshot mostrando monitores de ligação migração para visualização do monitor de ligação](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecione monitores de subscrição e de ligação e clique em "Migrar selecionado". Num clique migrar os monitores de ligação existentes para o Monitor de Ligação (Pré-visualização) 
1. Pode personalizar propriedades do monitor de ligação, alterar o espaço de trabalho predefinido, o modelo de descarregamento e verificar o estado da migração. 
1. Uma vez que a migração começa, as mudanças acontecem: 
    1. Alterações de recursos do Azure Resource Manager no monitor de ligação mais recente
        1. O nome, a região e a subscrição do monitor de ligação permanecem inalterados. Portanto, não há impacto na identificação de recursos.
        1. A menos que seja personalizado, é criado um espaço de trabalho log analytics predefinido na região e subscrição do monitor de ligação. Este espaço de trabalho é onde os dados de monitorização serão armazenados. Os dados dos resultados do teste também serão armazenados em métricas.
        1. Cada teste é migrado para um grupo de teste chamado * DefaultTestGroup*
        1.  Os pontos finais de origem e destino são criados e utilizados no grupo de teste criado. Os nomes predefinidos são *padrãoSourceEndpoint* e *padrãoDestinationEndpoint*
        1. A porta de destino e o intervalo de sondagem são movidos para a configuração de teste chamada *Configuração padrão do Teste*. Com base nos valores da porta, o protocolo está definido. Os limiares de sucesso e outras propriedades opcionais ficam em branco.
    1. Os alertas métricos são migrados para alertas métricos do Monitor de Ligação (Pré-visualização). As métricas são <link to metric section in the doc> diferentes, daí a mudança
    1. Os monitores de ligação migrados não aparecerão na solução de monitor de ligação mais antiga, estando agora apenas disponíveis para utilização no Monitor de Ligação (Pré-visualização)
    1. Quaisquer integrações externas como dashboards no Power BI, Grafana, integrações com sistemas SIEM, terão de ser migradas diretamente pelo utilizador. Este é o único passo manual que o utilizador precisa de realizar para migrar a sua configuração.

## <a name="next-steps"></a>Passos seguintes

* Saiba [como migrar do Monitor de Desempenho da Rede para o Monitor de Ligação (Pré-visualização)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Saiba [como criar o Monitor de Ligação (Pré-visualização) utilizando o portal Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
