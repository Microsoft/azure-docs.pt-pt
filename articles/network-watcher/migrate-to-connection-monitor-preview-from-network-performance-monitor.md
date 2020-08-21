---
title: Migrar para o Monitor de Ligação (Pré-visualização) do Monitor de Desempenho da Rede
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o Monitor de Ligação (Pré-visualização) a partir do Monitor de Desempenho da Rede.
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701835"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrar para o Monitor de Ligação (Pré-visualização) do Monitor de Desempenho da Rede

Pode migrar os testes do Monitor de Desempenho da Rede para o novo e melhorado Monitor de Ligação (Preview) num clique e com zero tempo de inatividade. Para saber mais sobre os benefícios, pode ler [o Monitor de Ligação (Pré-visualização)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> Apenas os testes do Monitor de Conectividade de Serviço podem ser migrados para o Monitor de Ligação (pré-visualização).
>

## <a name="key-points-to-note"></a>Pontos-chave a notar

* Os agentes no local e as definições de firewall funcionarão como está. Não são necessárias alterações. Os agentes do Log Analytics instalados em Azure Virtual Machines precisam de ser substituídos pela extensão do Observador de Rede
* Os testes existentes serão mapeados para o monitor de ligação (pré-visualização)-> formato de teste do Grupo de Teste ->. Os utilizadores podem clicar em *Editar* para visualizar e modificar propriedades do novo Monitor de Ligação e modelo de descarregamento para escoar alterações no Monitor de Ligação e submetê-lo através do Azure Resource Manager.
* Os agentes enviam dados para ambos – Log Analytics espaço de trabalho e métricas.
* Monitorizar dados
    * Dados em Log Analytics – Todos os dados pré-migração continuam a estar no espaço de trabalho em que o NPM está configurado na tabela NetworkMonitoring. Após a migração, os dados vão para a tabela NetworkMonitoring e ConnectionMonitor_CL tabela no mesmo espaço de trabalho. Uma vez que os testes são desativado a partir de NPM, os dados só serão armazenados em ConnectionMonitor_CL tabela
    * Registar alertas, dashboards e Integrações – Terá de editar manualmente as consultas com base na nova tabela ConnectionMonitor_CL. Também pode recriar os alertas em métricas usando este link. A capacidade de migrar alertas baseados em registos na tabela NetworkMonitoring para alertas baseados em métricas automaticamente como parte da migração estará disponível em breve
    
## <a name="prerequisites"></a>Pré-requisitos

*   Certifique-se de que o Observador de Rede está ativado na subscrição e região do espaço de trabalho Log Analytics
*   As máquinas virtuais Azure com agentes de análise de Log instalados terão de ser ativadas com a extensão do Observador de Rede

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Passos para migrar os testes do Monitor de Desempenho da Rede para o Monitor de Ligação (Pré-visualização)

1.  Clique em "Monitor de Ligação", navegue em "Migrar testes a partir de NPM" para migrar testes para Connection Monitor (Preview)

    ![Screenshot mostrando testes migratórios de NPM para visualização do monitor de conexão](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Selecione subscrição, espaço de trabalho e funcionalidade NPM que deseja migrar. Atualmente, só pode migrar testes a partir do Monitor de Conectividade de Serviço.  
1.  Clique em "Importar" para migrar testes
1.  Uma vez que a migração começa, as mudanças acontecem: 
    1. É criado um novo recurso de monitor de ligação
        1. É criado um monitor de ligação por região e subscrição. Para os testes com agentes no local, o novo nome do monitor de ligação é do formato <workspaceName> _"no local". Para testes com agentes Azure, o novo <workspaceName> nome do monitor de ligação é do formato_<Azure_region_name>
        1. Os dados de monitorização são agora armazenados no mesmo espaço de trabalho do Log Analytics em que o NPM está ativado, numa nova tabela chamada Connectionmonitor_CL tabela. 
        1. O nome do teste é levado para a frente para testar o nome do grupo. A descrição do teste não será migrada.
        1. Os pontos finais de origem e destino são criados e utilizados no grupo de teste criado. Para os agentes no local, os pontos finais são nomeados no formato <workspaceName> _"ponto final"._ <FQDN of on-premises machine> Para Azure, se os testes migratórios contiverem agentes não estão a funcionar, terá de ativar os agentes e migrar novamente.
        1. A porta de destino e o intervalo de sondagem são movidos para a configuração de teste, nomeadamente "TC"_ <testname> e "TC"_ <testname> _"AppThresholds". Com base nos valores da porta, o protocolo está definido. Os limiares de sucesso e outras propriedades opcionais ficam em branco.
    1. NPM não é desativado. Assim, os testes migrados continuam a enviar dados para a tabela NetworkMonitoring, bem como ConnectionMonitor_CL tabela. Este passo garante que os alertas e integrações existentes baseados em registos não sejam impactados. Alertas baseados em registos migratórios na tabela NetworkMonitoring para alertas baseados em métricas automaticamente como parte da migração estarão disponíveis em breve.
    1. O monitor de ligação recém-criado será visível no Monitor de Ligação (Pré-visualização)
1.  Após a migração, você precisaria de desativar manualmente os testes em NPM. Até lá, continuaria a ser cobrado pelo mesmo. Ao desativar o NPM, certifique-se de que recria os seus alertas na mesa ConnectionMonitor_CL ou utilize métricas. Também certifique-se de que quaisquer integrações externas como dashboards no Power BI, Grafana, integrações com sistemas SIEM, terão de ser migradas para ConnectionMonitor_CL tabela


## <a name="next-steps"></a>Passos seguintes

* Saiba [como migrar do Monitor de Ligação para o Monitor de Ligação (Pré-visualização)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Saiba [como criar o Monitor de Ligação (Pré-visualização) utilizando o portal Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
