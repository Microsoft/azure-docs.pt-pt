---
title: Monitorar o servidor de processo de Azure Site Recovery
description: Este artigo descreve como monitorar Azure Site Recovery servidor de processo usado para a recuperação de desastre do servidor físico/VM do VMware
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082232"
---
# <a name="monitor-the-process-server"></a>Monitorar o servidor de processo

Este artigo descreve como monitorar o [site Recovery](site-recovery-overview.md) servidor de processo.

- O servidor de processo é usado quando você configura a recuperação de desastre de VMs VMware locais e servidores físicos para o Azure.
- Por padrão, o servidor de processo é executado no servidor de configuração. Ele é instalado por padrão quando você implanta o servidor de configuração.
- Opcionalmente, para dimensionar e lidar com números maiores de máquinas replicadas e volumes maiores de tráfego de replicação, você pode implantar servidores de processo adicionais e de expansão.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre a função e a implantação de servidores de processo.

## <a name="monitoring-overview"></a>Descrição geral da monitorização

Como o servidor de processo tem tantas funções, especialmente em cache de dados replicados, compactação e transferência para o Azure, é importante monitorar a integridade do servidor de processo em uma base contínua.

Há várias situações que geralmente afetam o desempenho do servidor de processo. Problemas que afetam o desempenho terão um efeito em cascata na integridade da VM, por fim enviando o servidor de processo e seus computadores replicados para um estado crítico. As situações incluem:

- Números altos de VMs usam um servidor de processo, aproximando ou excedendo as limitações recomendadas.
- As VMs que usam o servidor de processo têm uma alta taxa de rotatividade.
- A taxa de transferência de rede entre VMs e o servidor de processo não é suficiente para carregar dados de replicação no servidor de processo.
- A taxa de transferência de rede entre o servidor de processo e o Azure não é suficiente para carregar dados de replicação do servidor de processo para o Azure.

Todos esses problemas podem afetar o RPO (objetivo de ponto de recuperação) das VMs. 

**Por?** Como a geração de um ponto de recuperação para uma VM exige que todos os discos na VM tenham um ponto comum. Se um disco tem uma alta taxa de rotatividade, a replicação é lenta ou o servidor de processo não é ideal, ele afeta a forma com que os pontos de recuperação são criados.

## <a name="monitor-proactively"></a>Monitorizar proativamente

Para evitar problemas com o servidor de processo, é importante:

- Entenda os requisitos específicos para servidores de processo usando [diretrizes de capacidade e dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e verifique se os servidores de processo estão implantados e em execução de acordo com as recomendações.
- Monitore alertas e solucione problemas à medida que eles ocorrem, para manter os servidores de processo em execução com eficiência.


## <a name="process-server-alerts"></a>Alertas do servidor de processo

O servidor de processo gera vários alertas de integridade, resumidos na tabela a seguir.

**Tipo de alerta** | **Detalhes**
--- | ---
![Bom estado de funcionamento][green] | O servidor de processo está conectado e íntegro.
![Aviso][yellow] | Utilização da CPU > 80% nos últimos 15 minutos
![Aviso][yellow] | Uso de memória > 80% nos últimos 15 minutos
![Aviso][yellow] | Espaço livre na pasta de cache < 30% nos últimos 15 minutos
![Aviso][yellow] | O Site Recovery monitora os dados pendentes/enviados a cada cinco minutos e estima que os dados no cache do servidor de processo não podem ser carregados no Azure dentro de 30 minutos.
![Aviso][yellow] | Os serviços do servidor de processo não estão em execução nos últimos 15 minutos
![Crítica][red] | Utilização da CPU > 95% nos últimos 15 minutos
![Crítica][red] | Uso de memória > 95% nos últimos 15 minutos
![Crítica][red] | Espaço livre na pasta de cache < 25% nos últimos 15 minutos
![Crítica][red] | Site Recovery monitora os dados pendentes/enviados a cada cinco minutos e estima que os dados no cache do servidor de processo não podem ser carregados no Azure dentro de 45 minutos.
![Crítica][red] | Nenhuma pulsação do servidor de processo por 15 minutos.

![Chave de tabela](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O status de integridade geral do servidor de processo é baseado no pior alerta gerado.



## <a name="monitor-process-server-health"></a>Monitorar a integridade do servidor de processo

Você pode monitorar o estado de integridade de seus servidores de processo da seguinte maneira: 

1. Para monitorar a integridade e o status da replicação de um computador replicado e de seu servidor de processo, no cofre > **itens replicados**, clique no computador que você deseja monitorar.
2. Em **integridade da replicação**, você pode monitorar o status de integridade da VM. Clique no status para detalhar os detalhes do erro.

    ![Integridade do servidor de processo no painel da VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Em **processo integridade do servidor**, você pode monitorar o status do servidor de processo. Faça uma busca detalhada para obter detalhes.

    ![Detalhes do servidor de processo no painel da VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. A integridade também pode ser monitorada usando a representação gráfica na página da VM.
    - Um servidor de processo de escalabilidade horizontal será realçado em laranja se houver avisos associados a ele e vermelho se ele tiver problemas críticos. 
    - Se o servidor de processo estiver sendo executado na implantação padrão no servidor de configuração, o servidor de configuração será realçado adequadamente.
    - Para fazer uma busca detalhada, clique no servidor de configuração ou no servidor de processo. Observe quaisquer problemas e quaisquer recomendações de correção.

Você também pode monitorar servidores de processo no cofre em **infraestrutura de site Recovery**. Em **gerenciar sua infraestrutura de site Recovery**, clique em **servidores de configuração**. Selecione o servidor de configuração associado ao servidor de processo e faça uma busca detalhada nos detalhes do servidor de processo.


## <a name="next-steps"></a>Passos seguintes

- Se você tiver problemas de servidores de processo, siga nossas [diretrizes de solução de problemas](vmware-physical-azure-troubleshoot-process-server.md)
- Se precisar de mais ajuda, poste sua pergunta no [Fórum de Azure site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
