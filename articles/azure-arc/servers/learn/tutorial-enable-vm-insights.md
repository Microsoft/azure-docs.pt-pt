---
title: Tutorial - Monitorize uma máquina híbrida com Monitor Azure para VMs
description: Saiba como recolher e analisar dados a partir de uma máquina híbrida no Azure Monitor.
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 409ad0976e02e42e385e22a103cfc06af5a4f3f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587699"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>Tutorial: Monitorize uma máquina híbrida com Monitor Azure para VMs

[O Azure Monitor](../overview.md) pode recolher dados diretamente das suas máquinas híbridas num espaço de trabalho log Analytics para análise e correlação detalhadas. Normalmente, isto implicaria instalar o [agente Log Analytics](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) na máquina utilizando um método de script, manual ou automatizado seguindo os seus padrões de gestão de configuração. Os servidores ativados pelo Arc introduziram recentemente suporte para instalar as [extensões VM](../manage-vm-extensions.md) do agente de Log Analytics e Dependency para Windows e Linux, permitindo ao Azure Monitor recolher dados dos seus VMs não-Azure.

Este tutorial mostra-lhe como configurar e recolher dados das suas máquinas Linux ou Windows, permitindo ao Azure Monitor para VMs seguindo um conjunto simplificado de passos, que simplifica a experiência e demora um período de tempo mais curto.  

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* A funcionalidade de extensão VM só está disponível na lista de [regiões apoiadas.](../overview.md#supported-regions)

* Consulte [sistemas operativos suportados](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo dos servidores que está a ativar é suportado pelo Azure Monitor para VMs.

* Reveja os requisitos de firewall para o agente Log Analytics fornecido na [visão geral](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements)do agente Log Analytics . O Azure Monitor para o agente VMs Map Dependency não transmite quaisquer dados em si, e não requer alterações em firewalls ou portas.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-azure-monitor-for-vms"></a>Ativar monitor Azure para VMs

1. Lançar o serviço Azure Arc no portal Azure clicando em **todos os serviços,** procurando e selecionando **máquinas - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Pesquisa de servidores ativados arc em todos os serviços" border="false":::

1. Na página **Machines - Azure Arc,** selecione a máquina conectada que criou no artigo [de arranque rápido.](quick-enable-hybrid-vm.md)

1. A partir do painel esquerdo sob a secção **de monitorização,** selecione **Insights** e, em seguida, **Ative**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Selecione a opção Insights do menu da mão esquerda" border="false":::

1. Na página Azure Monitor **Insights Onboarding,** é solicitado que crie um espaço de trabalho. Para este tutorial, não recomendamos que selecione um espaço de trabalho log analytics existente se já tiver um. Selecione o padrão, que é um espaço de trabalho com um nome único na mesma região que a sua máquina conectada registada. Este espaço de trabalho é criado e configurado para si.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Ativar o Monitor Azure para a página VMs" border="false":::

1. Recebe mensagens de estado enquanto a configuração é executada. Este processo demora alguns minutos à medida que as extensões são instaladas na sua máquina ligada.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Ativar o Monitor Azure para a mensagem de estado do progresso dos VMs" border="false":::

    Quando estiver concluída, recebe-se uma mensagem de que a máquina foi a bordo com sucesso e que a perceção foi implantada com sucesso.

## <a name="view-data-collected"></a>Ver os dados recolhidos

Após a implementação e configuração concluídas, selecione **Insights** e, em seguida, selecione o **separador Desempenho.** No separador Performance, mostra um grupo selecionado de contadores de desempenho recolhidos do sistema operativo convidado da sua máquina. Desloque-se para baixo para ver mais contadores e mova o rato sobre um gráfico para ver a média e percentículas tiradas a partir do momento em que a extensão VM Log Analytics foi instalada na máquina.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Monitor Azure para gráficos de desempenho de VMs para máquina selecionada" border="false":::

Selecione **Mapa** para abrir a funcionalidade de mapas, que mostra os processos em execução na máquina e as suas dependências. Selecione **Propriedades** para abrir o painel de propriedades se ainda não estiver aberto.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Monitor Azure para mapa de VMs para máquina selecionada" border="false":::

Expanda os processos para a sua máquina. Selecione um dos processos para ver os seus detalhes e destacar as suas dependências.

Selecione a sua máquina novamente e, em seguida, **selecione Registar Eventos**. Você vê uma lista de tabelas que estão armazenadas no espaço de trabalho Log Analytics para a máquina. Esta lista será diferente dependendo se estiver a utilizar uma máquina Windows ou Linux. Selecione a tabela **Evento.** A tabela **Evento** inclui todos os eventos a partir do registo de eventos do Windows. O Log Analytics abre com uma simples consulta para recuperar as entradas de registo de eventos recolhidas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Monitor, veja o seguinte artigo:

> [!div class="nextstepaction"]
> [Descrição geral do Azure Monitor](../../../azure-monitor/overview.md)