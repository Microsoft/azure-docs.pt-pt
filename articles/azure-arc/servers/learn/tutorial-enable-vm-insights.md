---
title: Tutorial - Monitorize uma máquina híbrida com insights VM do Monitor Azure
description: Saiba como recolher e analisar dados a partir de uma máquina híbrida no Azure Monitor.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834269"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Tutorial: Monitorize uma máquina híbrida com insights VM

[O Azure Monitor](../../../azure-monitor/overview.md) pode recolher dados diretamente das suas máquinas híbridas num espaço de trabalho log Analytics para análise e correlação detalhadas. Normalmente, isto implicaria instalar o [agente Log Analytics](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) na máquina utilizando um método de script, manual ou automatizado seguindo os seus padrões de gestão de configuração. Os servidores ativados pelo Arc introduziram recentemente suporte para instalar as [extensões VM](../manage-vm-extensions.md) do agente de Log Analytics e Dependency para Windows e Linux, permitindo insights [VM](../../../azure-monitor/vm/vminsights-overview.md) para recolher dados dos seus VMs não-Azure.

Este tutorial mostra-lhe como configurar e recolher dados das suas máquinas Linux ou Windows, permitindo insights em VM seguindo um conjunto simplificado de passos, que simplifica a experiência e demora um período de tempo mais curto.  

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* A funcionalidade de extensão VM só está disponível na lista de [regiões apoiadas.](../overview.md#supported-regions)

* Consulte [sistemas operativos suportados](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo dos servidores que está a ativar é suportado por insights VM.

* Reveja os requisitos de firewall para o agente Log Analytics fornecido na [visão geral](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements)do agente Log Analytics . O agente VM insights Map Dependency não transmite quaisquer dados em si, e não requer alterações em firewalls ou portas.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-vm-insights"></a>Ativar insights VM

1. Lançar o serviço Azure Arc no portal Azure clicando em **todos os serviços,** procurando e selecionando **máquinas - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Pesquisa de servidores ativados arc em todos os serviços" border="false":::

1. Na página **Machines - Azure Arc,** selecione a máquina conectada que criou no artigo [de arranque rápido.](quick-enable-hybrid-vm.md)

1. A partir do painel esquerdo sob a secção **de monitorização,** selecione **Insights** e, em seguida, **Ative**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Selecione a opção Insights do menu da mão esquerda" border="false":::

1. Na página Azure Monitor **Insights Onboarding,** é solicitado que crie um espaço de trabalho. Para este tutorial, não recomendamos que selecione um espaço de trabalho log analytics existente se já tiver um. Selecione o padrão, que é um espaço de trabalho com um nome único na mesma região que a sua máquina conectada registada. Este espaço de trabalho é criado e configurado para si.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Ativar a página de insights VM" border="false":::

1. Recebe mensagens de estado enquanto a configuração é executada. Este processo demora alguns minutos à medida que as extensões são instaladas na sua máquina ligada.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Ativar a mensagem de estado de progresso dos conhecimentos VM" border="false":::

    Quando estiver concluída, recebe-se uma mensagem de que a máquina foi a bordo com sucesso e que a perceção foi implantada com sucesso.

## <a name="view-data-collected"></a>Ver os dados recolhidos

Após a implementação e configuração concluídas, selecione **Insights** e, em seguida, selecione o **separador Desempenho.** No separador Performance, mostra um grupo selecionado de contadores de desempenho recolhidos do sistema operativo convidado da sua máquina. Desloque-se para baixo para ver mais contadores e mova o rato sobre um gráfico para ver a média e percentículas tiradas a partir do momento em que a extensão VM Log Analytics foi instalada na máquina.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="VM insights Gráficos de desempenho para máquina selecionada" border="false":::

Selecione **Mapa** para abrir a funcionalidade de mapas, que mostra os processos em execução na máquina e as suas dependências. Selecione **Propriedades** para abrir o painel de propriedades se ainda não estiver aberto.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="VM insights Mapa para máquina selecionada" border="false":::

Expanda os processos para a sua máquina. Selecione um dos processos para ver os seus detalhes e destacar as suas dependências.

Selecione a sua máquina novamente e, em seguida, **selecione Registar Eventos**. Você vê uma lista de tabelas que estão armazenadas no espaço de trabalho Log Analytics para a máquina. Esta lista será diferente dependendo se estiver a utilizar uma máquina Windows ou Linux. Selecione a tabela **Evento.** A tabela **Evento** inclui todos os eventos a partir do registo de eventos do Windows. O Log Analytics abre com uma simples consulta para recuperar as entradas de registo de eventos recolhidas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Monitor, veja o seguinte artigo:

> [!div class="nextstepaction"]
> [Descrição geral do Azure Monitor](../../../azure-monitor/overview.md)