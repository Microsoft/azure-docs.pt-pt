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
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: cd0d4c2dc550beef1b5fceb373a4cb96ed75a172
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544501"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrar para o Monitor de Ligação do Monitor de Desempenho da Rede

Pode migrar os testes do Monitor de Desempenho da Rede (NPM) para um novo Monitor de Ligação melhorado com um único clique e com zero tempo de inatividade. Para saber mais sobre os benefícios, consulte [o Connection Monitor.](./connection-monitor-overview.md)


>[!NOTE]
> Apenas os testes do Monitor de Conectividade de Serviço podem ser migrados para o Monitor de Ligação.
>

## <a name="key-points-to-note"></a>Pontos-chave a notar

A migração ajuda a produzir os seguintes resultados:

* Os agentes no local e as definições de firewall funcionam como está. Não são necessárias alterações. Os agentes do Log Analytics instalados em máquinas virtuais Azure precisam de ser substituídos pela extensão do Observador de Rede.
* Os testes existentes são mapeados para o formato de teste do Grupo de Teste > de Controlo > de Ligação. Ao selecionar **Editar,** pode visualizar e modificar as propriedades do novo Monitor de Ligação, descarregar um modelo para escoar alterações e submeter o modelo através do Azure Resource Manager.
* Os agentes enviam dados tanto para o espaço de trabalho do Log Analytics como para as métricas.
* Monitorização de dados:
   * **Dados em Log Analytics**: Antes da migração, os dados permanecem no espaço de trabalho no qual o NPM está configurado na tabela NetworkMonitoring. Após a migração, os dados vão para a tabela NetworkMonitoring e ConnectionMonitor_CL tabela no mesmo espaço de trabalho. Após os testes serem desativadas em NPM, os dados são armazenados apenas na tabela ConnectionMonitor_CL.
   * **Alertas, dashboards e integrações baseados em registos**: Deve editar manualmente as consultas com base na nova tabela ConnectionMonitor_CL. Para recriar os alertas em métricas, consulte [a monitorização da conectividade da rede com o Monitor de Ligação](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o Network Watcher está ativado na sua subscrição e na região do espaço de trabalho Log Analytics.
* As máquinas virtuais Azure com agentes Log Analytics instalados devem ser ativadas com a extensão do Observador de Rede.

## <a name="migrate-the-tests"></a>Migrar os testes

Para migrar os testes do Monitor de Desempenho da Rede para o Monitor de Ligação, faça o seguinte:

1. No Monitor de Rede, selecione **Connection Monitor** e, em seguida, selecione os **testes migratórios a partir do** separador NPM. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrar testes do Monitor de Desempenho da Rede para o Monitor de Ligação" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Nas listas de drop-down, selecione a sua subscrição e espaço de trabalho e, em seguida, selecione a funcionalidade NPM que pretende migrar. Atualmente, só pode migrar testes a partir do Monitor de Conectividade de Serviço.  
1. **Selecione Importar** para migrar os testes.

Após o início da migração, ocorrem as seguintes alterações: 
* É criado um novo recurso de monitor de ligação.
   * É criado um monitor de ligação por região e subscrição. Para testes com agentes no local, o novo nome do monitor de ligação é formatado como `<workspaceName>_"on-premises"` . Para testes com agentes Azure, o novo nome do monitor de ligação é formatado como `<workspaceName>_<Azure_region_name>` .
   * Os dados de monitorização são agora armazenados no mesmo espaço de trabalho do Log Analytics em que o NPM está ativado, numa nova tabela chamada Connectionmonitor_CL. 
   * O nome do teste é transportado para a frente como o nome do grupo de teste. A descrição do teste não é migrada.
   * Os pontos finais de origem e destino são criados e utilizados no novo grupo de teste. Para os agentes no local, os pontos finais são formatados como `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Para O Azure, se os testes migratórios contiverem agentes que não estão a funcionar, tens de ativar os agentes e migrar novamente.
   * A porta de destino e o intervalo de sondagem são transferidos para uma configuração de teste chamada *TC_ \<testname>* e *TC_ \<testname> _AppThresholds*. O protocolo é definido com base nos valores portuários. Os limiares de sucesso e outras propriedades opcionais ficam em branco.
* O NPM não é desativado, pelo que os testes migrados podem continuar a enviar dados para as tabelas NetworkMonitoring e ConnectionMonitor_CL. Esta abordagem garante que os alertas e integrações existentes baseados em registos não sejam afetados.
* O monitor de ligação recém-criado é visível no Monitor de Ligação.

Após a migração, certifique-se de:
* Desative manualmente os testes em NPM. Até o fazer, continuará a ser cobrado por eles. 
* Enquanto estiver a desativar o NPM, reorgan crie os seus alertas na mesa ConnectionMonitor_CL ou use métricas. 
* Migrar quaisquer integrações externas para a mesa ConnectionMonitor_CL. Exemplos de integrações externas são dashboards em Power BI e Grafana, e integrações com sistemas de Informação de Segurança e Gestão de Eventos (SIEM).


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Connection Monitor, consulte:
* [Migrar do Monitor de Ligação para o Monitor de Ligação](/azure/network-watcher/migrate-to-connection-monitor-from-connection-monitor-classic)
* [Criar Monitor de Ligação utilizando o portal Azure](./connection-monitor-create-using-portal.md)