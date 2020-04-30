---
title: Questões comuns sobre monitorização da recuperação do site azure
description: Obtenha respostas a perguntas comuns sobre monitorização de recuperação do site azure, utilizando monitorização incorporada e Monitor Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131149"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Questões comuns sobre monitorização da recuperação do site

Este artigo responde a perguntas comuns sobre a monitorização da [recuperação](site-recovery-overview.md)do site Azure, utilizando a monitorização incorporada da recuperação do site, e o Monitor Azure (Log Analytics).

## <a name="general"></a>Geral

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Como é que o valor do RPO é registado diferente do último ponto de recuperação disponível?

A Recuperação do Site utiliza um processo assíncrono e em várias etapas para replicar máquinas para o Azure.

- No penúltimo passo de replicação, as recentes alterações na máquina, juntamente com os metadados, são copiadas para uma conta de armazenamento de log/cache.
- Estas alterações, juntamente com a etiqueta que identifica um ponto recuperável, são escritas na conta de armazenamento/disco gerido na região alvo.
- A Recuperação do Local pode agora gerar um ponto recuperável para a máquina.
- Neste momento, o RPO foi satisfeito para as alterações enviadas para a conta de armazenamento até agora. Por outras palavras, o RPO da máquina neste momento é igual a quantidade de tempo que é decorrido da marca temporal correspondente ao ponto recuperável.
- Agora, a Recuperação do Site recolhe os dados carregados da conta de armazenamento e aplica-os aos discos de réplica criados para a máquina.
- A Recuperação do Site gera então um ponto de recuperação, e disponibiliza este ponto para recuperação no failover.
- Assim, o último ponto de recuperação disponível indica o carimbo de tempo correspondente ao último ponto de recuperação que já foi processado, e aplicado aos discos de réplica.


Um tempo de sistema incorreto na máquina de origem de replicação, ou nos servidores de infraestrutura no local, irá distorcer o valor de RPO computado. Para um relatório RPO preciso, certifique-se de que o relógio do sistema está correto em todos os servidores e máquinas.



## <a name="inbuilt-site-recovery-logging"></a>Exploração madeireira de recuperação do local incorporado


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Porque é que a contagem de VM na infraestrutura do cofre é diferente da contagem total mostrada em itens replicados?

A vista da infraestrutura do cofre é amplamente ampla por cenários de replicação. Apenas as máquinas no cenário de replicação atualmente selecionado estão incluídas na contagem para a vista. Além disso, só contamos VMs que estão configurados para replicar para Azure. As máquinas falhadas, ou as máquinas que se replicam de volta a um local no local, não são contadas na vista.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Porque é que a contagem de itens replicados em Essentials é diferente da contagem total de itens replicados no painel de instrumentos?

Apenas as máquinas para as quais a replicação inicial foi concluída estão incluídas na contagem mostrada no Essencial. O total de itens replicados inclui todas as máquinas do cofre, incluindo aquelas para as quais a replicação inicial está atualmente em andamento.

## <a name="azure-monitor-logging"></a>Registo do Azure Monitor


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Com que frequência a Recuperação do Site envia registos de recursos para o Registo do Monitor Azure? 

- As Estatísticas de Recuperação do AzureSite e o AzureSiteRecoveryRecoveryPoints são enviados a cada 15 minutos.  
- AzureSiteRecoveryReplicationDataUploadRate e AzureSiteRecoveryProtectedDiskDataChurnÉChurn são enviados a cada cinco minutos. 
- O AzureSiteRecoveryJobs é enviado no gatilho e na conclusão de um trabalho.
- Os Eventos AzureSiteRecoverySão enviados sempre que um evento é gerado. 
- Os Itens de Replicado Do AzureSiteRecoveryReplicadItems são enviados sempre que houver qualquer alteração ambiental. Normalmente, o tempo de atualização de dados é de 15 minutos após uma mudança. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Quanto tempo os dados são mantidos nos registos do Monitor Azure? 

Por defeito, a retenção é de 31 dias. Pode aumentar o período na secção **de Utilização e Custoestimado** no espaço de trabalho log Analytics. Clique na **Retenção**de Dados e escolha o intervalo.

### <a name="whats-the-size-of-the-resource-logs"></a>Qual é o tamanho dos registos de recursos? 

Tipicamente o tamanho de um tronco é de 15-20 KB. 


## <a name="next-steps"></a>Passos seguintes

Saiba como monitorizar com [monitorização incorporada](site-recovery-monitor-and-troubleshoot.md)da Recuperação do Local , ou [Monitor Azure](monitor-log-analytics.md).


