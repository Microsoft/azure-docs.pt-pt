---
title: Questões comuns sobre monitorização da recuperação do local de Azure
description: Obtenha respostas a perguntas comuns sobre a monitorização da recuperação do local do Azure, utilizando monitorização incorporada e Monitor Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "82131149"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Questões comuns sobre monitorização da recuperação do local

Este artigo responde a perguntas comuns sobre a monitorização [da Recuperação do Local](site-recovery-overview.md)de Azure, utilizando a monitorização incorporada da Recuperação do Local, e o Monitor Azure (Log Analytics).

## <a name="general"></a>Geral

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Como é que o valor RPO é registado diferente do último ponto de recuperação disponível?

A Recuperação do Site utiliza um processo assíncrona em várias etapas para replicar máquinas para Azure.

- No penúltimo passo de replicação, as recentes alterações na máquina, juntamente com os metadados, são copiadas para uma conta de armazenamento log/cache.
- Estas alterações, juntamente com a etiqueta que identifica um ponto recuperável, são escritas na conta de armazenamento/disco gerido na região alvo.
- A Recuperação do Site pode agora gerar um ponto recuperável para a máquina.
- Neste momento, a RPO foi recebida para as alterações enviadas para a conta de armazenamento até agora. Por outras palavras, a máquina RPO neste ponto é igual à quantidade de tempo decorrido a partir do relógio correspondente ao ponto recuperável.
- Agora, a Recuperação do Site recolhe os dados carregados da conta de armazenamento e aplica-os aos discos de réplica criados para a máquina.
- A Recuperação do Site gera então um ponto de recuperação, e torna este ponto disponível para recuperação em failover.
- Assim, o último ponto de recuperação disponível indica o tempotando correspondente ao último ponto de recuperação que já foi processado, e aplicado aos discos de réplica.


Um tempo de sistema incorreto na máquina de origem de replicação, ou em servidores de infraestrutura no local, irá distorcer o valor de RPO calculado. Para uma informação exata do RPO, certifique-se de que o relógio do sistema está correto em todos os servidores e máquinas.



## <a name="inbuilt-site-recovery-logging"></a>Registo de recuperação de local incorporado


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Porque é que a contagem de VM na infraestrutura do cofre é diferente da contagem total mostrada em Itens Replicados?

A vista da infraestrutura do cofre é traçada por cenários de replicação. Apenas as máquinas no cenário de replicação atualmente selecionado estão incluídas na contagem para a vista. Além disso, contamos apenas VMs que estão configurados para replicar para Azure. Falhas por cima de máquinas, ou máquinas que se reproduzem para um local no local, não são contadas na vista.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Porque é que a contagem de itens replicados no Essencial é diferente da contagem total de itens replicados no painel de instrumentos?

Apenas as máquinas para as quais a replicação inicial foi concluída estão incluídas na contagem mostrada no Essencial. O total de itens replicados inclui todas as máquinas no cofre, incluindo aquelas para as quais a replicação inicial está atualmente em curso.

## <a name="azure-monitor-logging"></a>Registo do Azure Monitor


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Com que frequência a Recuperação do Site envia registos de recursos para o Registo do Monitor Azure? 

- AzureSiteRecoveryReplicationStats e AzureSiteRecoveryRecoveryPoints são enviados a cada 15 minutos.  
- AzureSiteRecoveryReplicationDataUploadRate e AzureSiteRecoveryProtectedDiskDataChurn são enviados a cada cinco minutos. 
- A azureSiteRecoveryJobs é enviada no gatilho e na conclusão de um trabalho.
- A azureSiteRecoveryEvents é enviada sempre que um evento é gerado. 
- AzureSiteRecoveryReplicatedItems é enviado sempre que há qualquer alteração ambiental. Normalmente, o tempo de atualização de dados é de 15 minutos após uma alteração. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Quanto tempo os dados são mantidos nos registos do Azure Monitor? 

Por predefinição, a retenção é de 31 dias. Pode aumentar o período na secção **De Utilização e Custo Estimado** no espaço de trabalho Log Analytics. Clique em **Retenção de Dados** e escolha o intervalo.

### <a name="whats-the-size-of-the-resource-logs"></a>Qual é o tamanho dos registos de recursos? 

Normalmente, o tamanho de um tronco é de 15-20 KB. 


## <a name="next-steps"></a>Passos seguintes

Saiba como monitorizar com [a monitorização incorporada da Recuperação do Local,](site-recovery-monitor-and-troubleshoot.md)ou [Monitor Azure](monitor-log-analytics.md).


