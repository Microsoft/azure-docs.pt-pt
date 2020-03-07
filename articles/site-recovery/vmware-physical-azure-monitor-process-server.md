---
title: Monitorize o servidor de processo de recuperação do site Azure
description: Este artigo descreve como monitorizar o servidor do processo de recuperação do site Azure usado para a recuperação de desastres vMware VM/servidor físico
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362783"
---
# <a name="monitor-the-process-server"></a>Monitorize o servidor de processos

Este artigo descreve como monitorizar o servidor do processo de recuperação do [site.](site-recovery-overview.md)

- O servidor de processos é utilizado quando configura a recuperação de desastres de VMware VMware no local e servidores físicos para o Azure.
- Por padrão, o servidor de processo saem no servidor de configuração. É instalado por padrão quando implementa o servidor de configuração.
- Opcionalmente, para escalar e lidar com um maior número de máquinas replicadas e volumes mais elevados de tráfego de replicação, pode implementar servidores de processos adicionais e scale-out.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o papel e implementação de servidores de processos.

## <a name="monitoring-overview"></a>Descrição geral da monitorização

Uma vez que o servidor de processos tem tantas funções, particularmente em dados replicados, compressão e transferência para o Azure, é importante monitorizar a saúde do servidor de processos de forma contínua.

Há uma série de situações que geralmente afetam o desempenho do servidor de processos. Problemas que afetam o desempenho terão um efeito em cascata na saúde vm, eventualmente empurrando tanto o servidor de processo como as suas máquinas replicadas para um estado crítico. As situações incluem:

- Um número elevado de VMs utiliza um servidor de processo, aproximando-se ou excedendo as limitações recomendadas.
- Os VMs que usam o servidor de processo têm uma alta taxa de agitação.
- A entrada de rede entre VMs e o servidor de processo não é suficiente para enviar dados de replicação para o servidor de processo.
- A entrada de rede entre o servidor de processo si não é suficiente para enviar dados de replicação do servidor de processo para o Azure.

Todas estas questões podem afetar o objetivo do ponto de recuperação (RPO) das VMs. 

**Porquê?** Porque gerar um ponto de recuperação para um VM requer que todos os discos do VM tenham um ponto comum. Se um disco tem uma alta taxa de churn, a replicação é lenta, ou o servidor de processo não é o ideal, tem impacto na eficiência dos pontos de recuperação.

## <a name="monitor-proactively"></a>Monitorizar proativamente

Para evitar problemas com o servidor de processos, é importante:

- Compreenda os requisitos específicos para servidores de processos que utilizem capacidade e orientação de [dimensionamento,](site-recovery-plan-capacity-vmware.md#capacity-considerations)e certifique-se de que os servidores de processos são implementados e funcionando de acordo com as recomendações.
- Monitorize alertas e problemas de resolução de problemas à medida que ocorrem, para manter os servidores de processo sinuosos eficientemente.


## <a name="process-server-alerts"></a>Alertas de servidor de processo

O servidor de processo gera uma série de alertas de saúde, resumidos na tabela seguinte.

**Tipo de alerta** | **Detalhes**
--- | ---
![Bom estado de funcionamento][green] | O servidor de processos está conectado e saudável.
![Aviso][yellow] | Utilização de CPU > 80% nos últimos 15 minutos
![Aviso][yellow] | Utilização da memória > 80% nos últimos 15 minutos
![Aviso][yellow] | Espaço livre de pasta cache < 30% nos últimos 15 minutos
![Aviso][yellow] | A Recuperação do Site monitoriza os dados pendentes/de saída a cada cinco minutos, e estima que os dados na cache do servidor de processo não podem ser enviados para o Azure dentro de 30 minutos.
![Aviso][yellow] | Os serviços de servidores de processo sem funcionar nos últimos 15 minutos
![Crítica][red] | Utilização de CPU > 95% nos últimos 15 minutos
![Crítica][red] | Utilização da memória > 95% nos últimos 15 minutos
![Crítica][red] | Espaço livre de pasta cache < 25% nos últimos 15 minutos
![Crítica][red] | A Recuperação do Site monitoriza os dados pendentes/de saída a cada cinco minutos, e estima que os dados na cache do servidor de processo não podem ser enviados para o Azure dentro de 45 minutos.
![Crítica][red] | Não há batimentos cardíacos do servidor de processos durante 15 minutos.

![Chave de mesa](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O estado de saúde geral do servidor de processo baseia-se no pior alerta gerado.



## <a name="monitor-process-server-health"></a>Monitorizar a saúde do servidor de processos

Pode monitorizar o estado de saúde dos seus servidores de processo seguem-se: 

1. Para monitorizar a saúde e o estado de replicação de uma máquina replicada, e do seu servidor de processos, em vault > **Itens replicados,** clique na máquina que pretende monitorizar.
2. Na Saúde de **Replicação,** pode monitorizar o estado de saúde vm. Clique no estado para perfurar para obter detalhes de erro.

    ![Processar a saúde do servidor no dashboard VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. No **Process Server Health,** pode monitorizar o estado do servidor de processos. Faça um exercício para mais detalhes.

    ![Processar detalhes do servidor no dashboard VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. A saúde também pode ser monitorizada utilizando a representação gráfica na página VM.
    - Um servidor de processo scale-out será realçado em laranja se houver avisos associados a ele, e vermelho se tiver algum problema crítico. 
    - Se o servidor de processo estiver a ser reiniciado na implementação predefinida no servidor de configuração, o servidor de configuração será realçado em conformidade.
    - Para perfurar, clique no servidor de configuração ou no servidor de processos. Note quaisquer problemas e quaisquer recomendações de reparação.

Também pode monitorizar servidores de processos no cofre em **infraestrutura**de recuperação do local. Em Gerir a sua infraestrutura de **recuperação de sites,** clique em Servidores de **Configuração**. Selecione o servidor de configuração associado ao servidor de processos e faça uma análise aos detalhes do servidor de processos.


## <a name="next-steps"></a>Passos seguintes

- Se tiver algum problema com servidores de processos, siga a nossa orientação de [resolução de problemas](vmware-physical-azure-troubleshoot-process-server.md)
- Se precisar de mais ajuda, publique a sua pergunta no fórum de recuperação do [site Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
