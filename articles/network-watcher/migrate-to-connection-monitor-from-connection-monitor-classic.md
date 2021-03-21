---
title: Migrar para o Monitor de Ligação do Monitor de Ligação
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o Monitor de Ligação a partir do Monitor de Ligação.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d4ab5361d245ad1ee10d43184cc0a2d65fed2054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730036"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migrar para o Monitor de Ligação a partir do Monitor de Ligação (Clássico)

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos monitores de ligação no Connection Monitor (clássico), mas pode continuar a utilizar os monitores de ligação existentes criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas cargas de trabalho atuais, [migra do Connection Monitor (clássico) para o novo Monitor de Ligação](migrate-to-connection-monitor-from-connection-monitor-classic.md)  no Azure Network Watcher antes de 29 de fevereiro de 2024.

Pode migrar os monitores de ligação existentes para um novo Monitor de Ligação melhorado com apenas alguns cliques e com zero tempo de inatividade. Para saber mais sobre os benefícios, consulte [o Connection Monitor.](./connection-monitor-overview.md)

## <a name="key-points-to-note"></a>Pontos-chave a notar

A migração ajuda a produzir os seguintes resultados:

* Agentes e definições de firewall funcionam como está. Não são necessárias alterações. 
* Os monitores de ligação existentes estão mapeados para o > grupo de teste do Grupo de Teste de Ligação >. Ao selecionar **Editar,** pode visualizar e modificar as propriedades do novo Monitor de Ligação, descarregar um modelo para escoar alterações no Monitor de Ligação e submetê-lo através do Azure Resource Manager. 
* As máquinas virtuais Azure com a extensão do Observador de Rede enviam dados tanto para o espaço de trabalho como para as métricas. O Monitor de Ligação disponibiliza os dados através das novas métricas (ChecksFailedPercent e RoundTripTimeMs) em vez das métricas antigas (ProbesFailedPercent e AverageRoundtripMs). As métricas antigas serão migradas para novas métricas como ProbesFailedPercent -> ChecksFailedPercent e AverageRoundtripMs -> RoundTripTimeMs.
* Monitorização de dados:
   * **Alertas**: Migrar automaticamente para as novas métricas.
   * **Dashboards e integrações**: Requerer a edição manual do conjunto de métricas. 
    
## <a name="prerequisites"></a>Pré-requisitos

Se estiver a utilizar um espaço de trabalho personalizado, certifique-se de que o Network Watcher está ativado na sua subscrição e na região do seu espaço de trabalho Log Analytics. 

## <a name="migrate-the-connection-monitors"></a>Migrar os monitores de ligação

1. Para migrar os monitores de ligação mais antigos para o mais recente, selecione **Connection Monitor** e, em seguida, selecione **Monitores de Ligação Migrar**.

    ![Screenshot mostrando a migração de monitores de ligação para o Monitor de Ligação.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecione a sua subscrição e os monitores de ligação que pretende migrar e, em seguida, **selecione Migrar selecionado**. 

Com apenas alguns cliques, emigrou os monitores de ligação existentes para o Monitor de Ligação. Uma vez migrado de CM (Clássico) para CM, não poderá ver o monitor sob CM (Clássico)

Agora pode personalizar as propriedades do Connection Monitor, alterar o espaço de trabalho predefinido, os modelos de descarregamento e verificar o estado de migração. 

Após o início da migração, ocorrem as seguintes alterações: 
* O recurso Azure Resource Manager altera o monitor de ligação mais recente.
    * O nome, região e subscrição do monitor de ligação permanecem inalterados. A identificação do recurso não é afetada.
    * A menos que o monitor de ligação seja personalizado, um espaço de trabalho padrão do Log Analytics é criado na subscrição e na região do monitor de ligação. Este espaço de trabalho é onde os dados de monitorização são armazenados. Os dados dos resultados do teste também são armazenados nas métricas.
    * Cada teste é migrado para um grupo de teste chamado *DefaultTestGroup*.
    * Os pontos finais de origem e destino são criados e utilizados no novo grupo de teste. Os nomes predefinidos são *padrãoSourceEndpoint* e *padrãoDestinationEndpoint*.
    * A porta de destino e o intervalo de sondagem são movidos para uma configuração de teste chamada *Configuração padrão do Teste*. O protocolo é definido com base nos valores portuários. Os limiares de sucesso e outras propriedades opcionais ficam em branco.
* Os alertas de métricas são migrados para alertas de métricas do Monitor de Ligação. As métricas são diferentes, daí a mudança. Para obter mais informações, consulte [a monitorização da conectividade da rede com o Monitor de Ligação.](./connection-monitor-overview.md#metrics-in-azure-monitor)
* Os monitores de ligação migrados deixaram de ser apresentados como a solução de monitor de ligação mais antiga. Estão agora disponíveis para utilização apenas no Monitor de Ligação.
* Quaisquer integrações externas, como dashboards no Power BI e Grafana, e integrações com sistemas de Informação de Segurança e Gestão de Eventos (SIEM), devem ser migradas manualmente. Este é o único passo manual que precisa de ser acionado para migrar a sua configuração.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Connection Monitor, consulte:
* [Migrar do Monitor de Desempenho da Rede para o Monitor de Ligação](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Criar Monitor de Ligação utilizando o portal Azure](./connection-monitor-create-using-portal.md)
