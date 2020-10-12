---
title: Monitorize o servidor de processo de recuperação do site Azure
description: Este artigo descreve como monitorizar o servidor de processo de recuperação do site Azure utilizado para a recuperação de desastres vMware VM/servidor físico
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86134742"
---
# <a name="monitor-the-process-server"></a>Monitorize o servidor de processo

Este artigo descreve como monitorizar o servidor de processo [de recuperação do site.](site-recovery-overview.md)

- O servidor de processo é utilizado quando configura a recuperação de desastres de VMware VMware e servidores físicos para Azure.
- Por predefinição, o servidor de processo funciona no servidor de configuração. É instalado por predefinição quando implementa o servidor de configuração.
- Opcionalmente, para escalar e lidar com um maior número de máquinas replicadas e volumes mais elevados de tráfego de replicação, pode implementar servidores de processos adicionais e de escala.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o papel e implementação de servidores de processo.

## <a name="monitoring-overview"></a>Descrição geral da monitorização

Uma vez que o servidor de processo tem tantas funções, particularmente na replicação de caching de dados, compressão e transferência para a Azure, é importante monitorizar a saúde do servidor de processos de forma contínua.

Há uma série de situações que geralmente afetam o desempenho do servidor de processo. Os problemas que afetam o desempenho terão um efeito em cascata na saúde de VM, eventualmente empurrando tanto o servidor de processo como as suas máquinas replicadas para um estado crítico. As situações incluem:

- Um número elevado de VMs utiliza um servidor de processo, aproximando-se ou excedendo as limitações recomendadas.
- Os VMs que usam o servidor de processo têm uma alta taxa de churn.
- A produção de rede entre VMs e o servidor de processos não é suficiente para enviar dados de replicação para o servidor de processo.
- A produção de rede entre o servidor de processo e o Azure não é suficiente para enviar dados de replicação do servidor de processo para OZure.

Todas estas questões podem afetar o objetivo do ponto de recuperação (RPO) dos VM. 

**Porquê?** Porque gerar um ponto de recuperação para um VM requer que todos os discos do VM tenham um ponto comum. Se um disco tem uma alta taxa de churn, a replicação é lenta, ou o servidor de processo não é o ideal, impacta a eficiência com que os pontos de recuperação são criados.

## <a name="monitor-proactively"></a>Monitorizar proativamente

Para evitar problemas com o servidor de processo, é importante:

- Compreenda os requisitos específicos para os servidores de processos utilizando [a capacidade e orientação de dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations), e certifique-se de que os servidores de processo são implementados e funcionando de acordo com as recomendações.
- Monitorize alertas e problemas de resolução de problemas à medida que ocorrem, para manter os servidores de processo funcionando eficientemente.


## <a name="process-server-alerts"></a>Alertas de servidor de processo

O servidor de processo gera uma série de alertas de saúde, resumidos na tabela seguinte.

**Tipo de alerta** | **Detalhes**
--- | ---
![Bom estado de funcionamento][green] | O servidor de processo está conectado e saudável.
![Aviso][yellow] | Utilização do CPU > 80% nos últimos 15 minutos
![Aviso][yellow] | O uso da memória > 80% nos últimos 15 minutos
![Aviso][yellow] | Espaço livre de pasta cache < 30% nos últimos 15 minutos
![Aviso][yellow] | A Recuperação do Site monitoriza os dados pendentes/de saída a cada cinco minutos e estima que os dados na cache do servidor do processo não podem ser enviados para a Azure dentro de 30 minutos.
![Aviso][yellow] | Os serviços de servidor de processo não estão a funcionar nos últimos 15 minutos
![Crítico][red] | Utilização do CPU > 95% nos últimos 15 minutos
![Crítico][red] | O uso da memória > 95% nos últimos 15 minutos
![Crítico][red] | Espaço livre de pasta cache < 25% nos últimos 15 minutos
![Crítico][red] | A Recuperação do Site monitoriza os dados pendentes/de saída a cada cinco minutos e estima que os dados na cache do servidor do processo não podem ser enviados para a Azure dentro de 45 minutos.
![Crítico][red] | Sem batimentos cardíacos do servidor de processo durante 15 minutos.

![Chave de mesa](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O estado geral de saúde do servidor de processo baseia-se no pior alerta gerado.



## <a name="monitor-process-server-health"></a>Monitorizar a saúde do servidor do processo

Pode monitorizar o estado de saúde dos seus servidores de processo da seguinte forma: 

1. Para monitorizar a saúde e o estado de replicação de uma máquina replicada, e do seu servidor de processo, no cofre > **itens replicados,** clique na máquina que pretende monitorizar.
2. Na **Replication Health,** pode monitorizar o estado de saúde em VM. Clique no estado para aprofundar para obter detalhes de erro.

    ![Processe a saúde do servidor no painel de instrumentos VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Na **Saúde do Servidor de Processos,** pode monitorizar o estado do servidor de processo. Acalme-se para mais detalhes.

    ![Processar detalhes do servidor no painel de instrumentos VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. A saúde também pode ser monitorizada usando a representação gráfica na página VM.
    - Um servidor de processo de escala será destacado em laranja se houver avisos associados ao mesmo, e vermelho se tiver algum problema crítico. 
    - Se o servidor de processo estiver a ser em execução na implementação predefinida no servidor de configuração, o servidor de configuração será realçado em conformidade.
    - Para perfurar, clique no servidor de configuração ou no servidor de processo. Note quaisquer problemas e quaisquer recomendações de reparação.

Também pode monitorizar servidores de processo no cofre sob **a Infraestrutura de Recuperação do Local.** Na **Gestão da sua infraestrutura de recuperação do site,** clique em **Servidores de Configuração**. Selecione o servidor de configuração associado ao servidor de processo e faça um exercício para baixo em detalhes do servidor de processo.


## <a name="next-steps"></a>Passos seguintes

- Se tiver algum problema com servidores de processo, siga a nossa [orientação de resolução de problemas](vmware-physical-azure-troubleshoot-process-server.md)
- Se precisar de mais ajuda, publique a sua pergunta na [página de perguntas do Microsoft Q&Uma página de perguntas para a recuperação do site Azure](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
