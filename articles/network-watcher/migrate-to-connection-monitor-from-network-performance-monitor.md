---
title: Migrar para o Monitor de Ligação do Monitor de Desempenho da Rede
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o Monitor de Ligação a partir do Monitor de Desempenho da Rede.
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
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366356"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrar para o Monitor de Ligação do Monitor de Desempenho da Rede

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho com o Network Performance Monitor. Pode continuar a utilizar os testes criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, migrar os seus testes do Monitor de Desempenho da Rede para o novo Monitor de Ligação no Azure Network Watcher antes de 29 de fevereiro de 2024.

Pode migrar os testes do Monitor de Desempenho da Rede (NPM) para um novo Monitor de Ligação melhorado com um único clique e com zero tempo de inatividade. Para saber mais sobre os benefícios, consulte [o Connection Monitor.](./connection-monitor-overview.md)


## <a name="key-points-to-note"></a>Pontos-chave a notar

A migração ajuda a produzir os seguintes resultados:

* Os agentes no local e as definições de firewall funcionam como está. Não são necessárias alterações. Os agentes do Log Analytics instalados em máquinas virtuais Azure precisam de ser substituídos pela [extensão do Observador de Rede](../virtual-machines/extensions/network-watcher-windows.md).
* Os testes existentes são mapeados para o formato de teste do Grupo de Teste > de Controlo > de Ligação. Ao selecionar **Editar,** pode visualizar e modificar as propriedades do novo Monitor de Ligação, descarregar um modelo para escoar alterações e submeter o modelo através do Azure Resource Manager.
* Os agentes enviam dados tanto para o espaço de trabalho do Log Analytics como para as métricas.
* Monitorização de dados:
   * **Dados em Log Analytics**: Antes da migração, os dados permanecem no espaço de trabalho no qual o NPM está configurado na tabela NetworkMonitoring. Após a migração, os dados vão para a tabela NetworkMonitoring, nwConnectionMonitorTestResult e nwConnectionMonitorPathResult no mesmo espaço de trabalho. Após os testes serem desativados em NPM, os dados são armazenados apenas na tabela NWConnectionMonitorTestResult e na tabela NWConnectionMonitorPathResult.
   * **Alertas, dashboards e integrações baseados em registos**: Tem de editar manualmente as consultas com base na nova tabela NWConnectionMonitorTestResult e na tabela NWConnectionMonitorPathResult. Para recriar os alertas em métricas, consulte [a monitorização da conectividade da rede com o Monitor de Ligação](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Para monitorização ExpressRoute:
    * **Fim à perda e latência terminais**: O Monitor de Ligação irá alimentar isto, e será mais fácil do que o NPM, uma vez que os utilizadores não precisam de configurar quais circuitos e espreitadores para monitorizar. Os circuitos na trajetória serão automaticamente descobertos, os dados estarão disponíveis em métricas (mais rápido do que LA, que foi onde a NPM armazenou os resultados). A topologia funcionará como está também.
    * **Medições de largura** de banda : Com o lançamento de métricas relacionadas com a largura de banda, a abordagem baseada em análise de registo da NPM não foi eficaz na monitorização da largura de banda para os clientes ExpressRoute. Esta capacidade não está agora disponível no Connection Monitor.
    
## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o Network Watcher está ativado na sua subscrição e na região do espaço de trabalho Log Analytics. Se não for feito, verá um erro indicando "Antes de tentar migrar, por favor, ative a extensão do observador de rede na subscrição de seleção e localização do espaço de trabalho de LA selecionado."
* Caso o Azure VM pertença a uma região/subscrição diferente da do espaço de trabalho Log Analytics seja utilizado como ponto final, certifique-se de que o Network Watcher está ativado para essa subscrição e região.   
* As máquinas virtuais Azure com agentes Log Analytics instalados devem ser ativadas com a extensão do Observador de Rede.

## <a name="migrate-the-tests"></a>Migrar os testes

Para migrar os testes do Monitor de Desempenho da Rede para o Monitor de Ligação, faça o seguinte:

1. No Monitor de Rede, selecione **Connection Monitor** e, em seguida, selecione os **testes migratórios a partir do** separador NPM. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrar testes do Monitor de Desempenho da Rede para o Monitor de Ligação" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Nas listas de drop-down, selecione a sua subscrição e espaço de trabalho e, em seguida, selecione a funcionalidade NPM que pretende migrar. 
1. **Selecione Importar** para migrar os testes.
* Se o NPM não estiver ativado no espaço de trabalho, verá um erro que indica "Não se encontra nenhum config NPM válido". 
* Se não existirem testes na funcionalidade que escolheu no passo 2, verá um erro indicando que "O espaço de trabalho selecionado não tem <feature> config".
* Se não houver testes válidos, verá um erro indicando que "O espaço de trabalho selecionado não tem testes válidos"
* Os seus testes podem conter agentes que já não estão ativos, mas podem ter estado ativos no passado. Verá um erro afirmando "Poucos testes contêm agentes que já não estão ativos. Lista de agentes inativos. {0} Estes agentes podem estar a correr no passado, mas já não estão desligados ou já não estão a funcionar. Ative os agentes e migrar para o Monitor de Ligação. Clique em continuar a migrar os testes que não contêm agentes que não estão ativos."

Após o início da migração, ocorrem as seguintes alterações: 
* É criado um novo recurso de monitor de ligação.
   * É criado um monitor de ligação por região e subscrição. Para testes com agentes no local, o novo nome do monitor de ligação é formatado como `<workspaceName>_"workspace_region_name"` . Para testes com agentes Azure, o novo nome do monitor de ligação é formatado como `<workspaceName>_<Azure_region_name>` .
   * Os dados de monitorização são agora armazenados no mesmo espaço de trabalho do Log Analytics em que o NPM está ativado, em novas tabelas chamadas NWConnectionMonitorTestResult e tabela NWConnectionMonitorPathResult. 
   * O nome do teste é transportado para a frente como o nome do grupo de teste. A descrição do teste não é migrada.
   * Os pontos finais de origem e destino são criados e utilizados no novo grupo de teste. Para os agentes no local, os pontos finais são formatados como `<workspaceName>_<FQDN of on-premises machine>` . A descrição do agente não está migrada.
   * A porta de destino e o intervalo de sondagem são movidos para uma configuração de teste chamada `TC_<protocol>_<port>` e `TC_<protocol>_<port>_AppThresholds` . O protocolo é definido com base nos valores portuários. Para o ICMP, as configurações de teste são nomeadas como `TC_<protocol>` e `TC_<protocol>_AppThresholds` . Limiares de sucesso e outras propriedades opcionais se definidos forem migrados, caso contrário ficam em branco.
   * Se os testes migratórios contiverem agentes que não estão a funcionar, tens de ativar os agentes e migrar novamente.
* O NPM não está desativado, pelo que os testes migrados podem continuar a enviar dados para a tabela NetworkMonitoring, para a tabela NWConnectionMonitorTestResult e para a tabela NWConnectionMonitorPathResult. Esta abordagem garante que os alertas e integrações existentes baseados em registos não sejam afetados.
* O monitor de ligação recém-criado é visível no Monitor de Ligação.

Após a migração, certifique-se de:
* Desative manualmente os testes em NPM. Até o fazer, continuará a ser cobrado por eles. 
* Enquanto estiver a desativar o NPM, reexamine os seus alertas nas tabelas NWConnectionMonitorTestResult e NWConnectionMonitorPathResult ou use métricas. 
* Migrar quaisquer integrações externas para as tabelas NWConnectionMonitorTestResult e NWConnectionMonitorPathResult. Exemplos de integrações externas são dashboards em Power BI e Grafana, e integrações com sistemas de Informação de Segurança e Gestão de Eventos (SIEM).


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Connection Monitor, consulte:
* [Migrar do Monitor de Ligação (clássico) para o Monitor de Ligação](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Criar Monitor de Ligação utilizando o portal Azure](./connection-monitor-create-using-portal.md)
