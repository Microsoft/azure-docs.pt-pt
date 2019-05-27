---
title: Monitorizar o servidor de processos do Azure Site Recovery
description: Este artigo descreve como monitorizar o servidor de processos do Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: raynew
ms.openlocfilehash: f3ba08bc1d557d53bca2421b5052f496c740b185
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969339"
---
# <a name="monitor-the-process-server"></a>Monitorizar o servidor de processos

Este artigo descreve como monitorizar o [recuperação de Site](site-recovery-overview.md) servidor de processos.

- O servidor de processos é utilizado quando configurou a recuperação após desastre de VMs de VMware no local e servidores físicos para o Azure.
- Por predefinição, o servidor de processos é executado no servidor de configuração. Ele é instalado por padrão quando implantar o servidor de configuração.
- Opcionalmente, para dimensionamento e o identificador de grandes quantidades de máquinas replicadas e mais altos volumes de tráfego de replicação, pode implementar servidores de processos adicionais, de escalamento horizontal.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre a função e a implementação de servidores de processos.

## <a name="monitoring-overview"></a>Descrição geral da monitorização

Uma vez que o servidor de processos tem muitas funções, especialmente em dados replicados de colocação em cache, compressão e a transferência para o Azure, é importante monitorizar o estado de funcionamento de servidor de processos de forma contínua.

Existem diversas situações que normalmente afetam o desempenho do servidor de processo. Problemas que afetam o desempenho serão de ter um impacto em cascata no estado de funcionamento VM, eventualmente, enviar por push o servidor de processos e respetivas máquinas replicadas para um estado crítico. Situações incluem:

- Elevado número de VMs, utilize um servidor de processos, a aproximar-se as ou superando limitações recomendadas.
- VMs com o servidor de processos tem uma elevada taxa de alteração.
- Débito de rede entre VMs e o servidor de processos não é suficiente para carregar dados de replicação para o servidor de processos.
- Débito de rede entre o servidor de processos e o Azure não é suficiente para carregar dados de replicação do servidor de processos para o Azure.

Todos esses problemas podem afetar o objetivo de ponto de recuperação (RPO) de VMs. 

**Por quê?** Uma vez que a geração de um ponto de recuperação para uma VM requer que todos os discos na VM para ter um ponto comum. Se um disco com uma elevada taxa de alteração, a replicação está lenta ou, o servidor de processos não é ideal, elas impactam a eficiência do desempenho são criados pontos de recuperação.

## <a name="monitor-proactively"></a>Monitorizar proativamente

Para evitar problemas com o servidor de processos, é importante:

- Compreender os requisitos específicos para os servidores de processos usando [capacidade e a documentação de orientação de dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e certifique-se de servidores de processos são implementados e em execução, de acordo com as recomendações.
- Monitorizar alertas e resolução de problemas à medida que ocorrem, para manter os servidores de processos em execução com eficiência.


## <a name="process-server-alerts"></a>Alertas do servidor de processos

O servidor de processos gera um número de alertas de estado de funcionamento, resumidos na tabela seguinte.

**Tipo de alerta** | **Detalhes**
--- | ---
![Bom Estado de Funcionamento][green] | Servidor de processos está ligado e bom estado de funcionamento.
![Aviso][yellow] | Utilização da CPU > 80% para os últimos 15 minutos
![Aviso][yellow] | Utilização de memória > 80% para os últimos 15 minutos
![Aviso][yellow] | Pasta de cache de espaço livre < 30% nos últimos 15 minutos
![Aviso][yellow] | Não estão a executar serviços do servidor de processos nos últimos 15 minutos
![Crítica][red] | Utilização da CPU > 95% para os últimos 15 minutos
![Crítica][red] | Memória utilização > 95% para os últimos 15 minutos
![Crítica][red] | Pasta de cache de espaço livre < 25% para os últimos 15 minutos
![Crítica][red] | Nenhum heartbeat do servidor de processos para 15 minutos.

![chave da tabela](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O estado de funcionamento geral do servidor de processos baseia-se a pior alerta gerado.



## <a name="monitor-process-server-health"></a>Estado de funcionamento de servidor de processos de monitor

Pode monitorizar o estado de funcionamento dos seus servidores de processo da seguinte forma: 

1. Para monitorizar o estado de funcionamento de replicação e o estado de uma máquina replicada e do seu servidor de processos, no cofre > **itens replicados**, clique na máquina que pretende monitorizar.
2. Na **estado de funcionamento da replicação**, pode monitorizar o estado de funcionamento da VM. Clique no estado para desagregar para obter detalhes de erro.

    ![Estado de funcionamento de servidor de processos no dashboard VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Na **estado de funcionamento de servidor de processos**, pode monitorizar o estado do servidor de processos. Desagregar para obter detalhes.

    ![Detalhes do servidor de processo no dashboard VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Estado de funcionamento também pode ser monitorizado com a representação gráfica na página de VM.
    - Um servidor de processos de escalamento horizontal irá ser destacado na cor de laranja se existirem avisos associados a ele e vermelho, se tiver quaisquer problemas críticos. 
    - Se o servidor de processos está em execução na implementação predefinida no servidor de configuração, em seguida, o servidor de configuração será realçado em conformidade.
    - Para fazer uma busca detalhada, clique no servidor de configuração ou no servidor de processo. Tenha em atenção de todos os problemas e quaisquer recomendações de correção.

Também pode monitorizar processar servidores no cofre sob **infraestrutura do Site Recovery**. Na **gerir a infraestrutura do Site Recovery**, clique em **servidores de configuração**. Selecione o servidor de configuração associado com o servidor de processos e exploração de para baixo em detalhes do servidor de processo.


## <a name="next-steps"></a>Passos Seguintes

- Se tiver algum processar problemas de servidores, siga os nossos [orientações de resolução de problemas](vmware-physical-azure-troubleshoot-process-server.md)
- Se precisar de mais ajuda, publique sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
