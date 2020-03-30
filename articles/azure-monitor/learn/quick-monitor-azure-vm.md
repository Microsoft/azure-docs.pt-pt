---
title: Monitorize um recurso Azure com o Monitor Azure
description: Saiba como recolher e analisar dados para um recurso Azure no Monitor Azure.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79503658"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Quickstart: Monitorize uma máquina virtual Azure com Monitor Azure
[O Azure Monitor](../overview.md) começa a recolher dados de máquinas virtuais Azure no momento em que são criadas. Neste arranque rápido, você vai fazer uma breve caminhada através dos dados que são recolhidos automaticamente para um VM Azure e como vê-lo no portal Azure. Em seguida, irá ativar o [Monitor Azure para VMs](../insights/vminsights-overview.md) para o seu VM, o que permitirá aos agentes no VM recolher e analisar dados do sistema operativo de hóspedes, incluindo processos e suas dependências.

Este início rápido pressupõe que tem uma máquina virtual do Azure. Caso contrário, pode criar um [VM Windows](../../virtual-machines/windows/quick-create-portal.md) ou criar um [VM Linux](../../virtual-machines/linux/quick-create-cli.md) seguindo os nossos quickstarts VM.

Para descrições mais detalhadas dos dados de monitorização recolhidos dos recursos do Azure, consulte a Monitorização das [máquinas virtuais Azure com o Monitor Azure](../insights/monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Complete o Monitor um recurso Azure rapidamente.
[Complete o Monitor de um recurso Azure com o Monitor Azure](quick-monitor-azure-resource.md) para visualizar a página geral, o registo de atividade e as métricas para um VM na sua subscrição. Os VMs Azure recolhem os mesmos dados de monitorização que qualquer outro recurso Azure, mas isto é apenas para o VM hospedeiro. O resto deste quickstart concentrar-se-á na monitorização do sistema operativo dos hóspedes e das suas cargas de trabalho.


## <a name="enable-azure-monitor-for-vms"></a>Ativar o Monitor Azure para VMs
Enquanto as métricas e registos de atividade serão recolhidos para o VM anfitrião, você precisa de um agente e alguma configuração para recolher e analisar dados de monitorização do sistema operativo do hóspede e suas cargas de trabalho. O Monitor Azure para VMs instala estes agentes e fornece funcionalidades poderosas adicionais para monitorizar as suas máquinas virtuais.

1. Vá ao menu para a sua máquina virtual.
2. Clique em **Ir a Insights** a partir do azulejo na página **'Visão Geral'** ou clique em **Insights** a partir do menu **monitor.**

    ![Página de visão geral](media/quick-monitor-azure-vm/overview-insights.png)

3. Se o Monitor Azure para VMs ainda não tiver sido ativado para a máquina virtual, clique em **Ativar**. 

    ![Ativar insights](media/quick-monitor-azure-vm/enable-insights.png)

4. Se a máquina virtual ainda não estiver ligada a um espaço de trabalho do Log Analytics, será solicitado a selecionar um espaço de trabalho existente ou criar um novo. Selecione o padrão que é um espaço de trabalho com um nome único na mesma região que a sua máquina virtual.

    ![Selecionar área de trabalho](media/quick-monitor-azure-vm/select-workspace.png)

5. O embarque levará alguns minutos à medida que as extensões estiverem ativadas e os agentes são instalados na sua máquina virtual. Quando estiver completo, recebes uma mensagem de que as ideias foram implementadas com sucesso. Clique no **Monitor Azure** para abrir o Monitor Azure para VMs.

    ![Monitor Azure Aberto](media/quick-monitor-azure-vm/azure-monitor.png)

6. Você verá o seu VM com quaisquer outros VMs na sua subscrição que estejam a bordo. Selecione o separador **Não monitorizado** se pretender visualizar máquinas virtuais na sua subscrição que não estejam a bordo.

    ![Introdução](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Configurar a área de trabalho
Quando cria um novo espaço de trabalho do Log Analytics, tem de ser configurado para recolher registos. Esta configuração só precisa de ser executada uma vez que a configuração é enviada para quaisquer máquinas virtuais que se conectem a ela.

1. Selecione **a configuração do Espaço de Trabalho** e, em seguida, selecione o seu espaço de trabalho.

2. Selecione **configurações avançadas**

    ![Definições Avançadas do Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Recolha de dados da VM do Windows


2. Selecione **Dados** e, em seguida, selecione **Registos de Eventos do Windows**.

3. Adicione um registo de evento digitando o nome do registo.  Digite **o Sistema** e, em seguida, selecione o sinal **+** de mais .

4. Na tabela, verifique as gravidades **Erro** e **Aviso**.

5. Selecione **Guardar** na parte superior da página para guardar a configuração.

### <a name="data-collection-from-linux-vm"></a>Recolha de dados da VM do Linux

1. Selecione **Syslog**.  

2. Adicione um registo de evento digitando o nome do registo.  Digite **syslog** e, **+** em seguida, selecione o sinal de mais .  

3. Na tabela, desmarque as severidades **Info,** **Notice** e **Debug**. 

4. Selecione **Guardar** na parte superior da página para guardar a configuração.

## <a name="view-data-collected"></a>Ver os dados recolhidos

7. Clique na sua máquina virtual e, em seguida, selecione o separador **Performance.** Isto mostra um grupo selecionado de contadores de desempenho recolhidos do sistema operativo de hóspedes do seu VM. Desça para ver mais contadores e mova o rato sobre um gráfico para ver a média e os percentículos em momentos diferentes.

    ![Desempenho](media/quick-monitor-azure-vm/performance.png)

9. Selecione **Mapa** para abrir a funcionalidade de mapas que mostra os processos em execução na máquina virtual e suas dependências. Selecione **Propriedades** para abrir o painel de propriedades se ainda não estiver aberta.

    ![Mapa](media/quick-monitor-azure-vm/map.png)

11. Expanda os processos para a sua máquina virtual. Selecione um dos processos para ver os seus detalhes e para destacar as suas dependências.

    ![Processos](media/quick-monitor-azure-vm/processes.png)

12. Selecione novamente a sua máquina virtual e, em seguida, selecione **Log Events**. 

    ![Eventos de log](media/quick-monitor-azure-vm/log-events.png)

13. Você vê uma lista de tabelas que estão armazenadas no espaço de trabalho log Analytics para a máquina virtual. Esta lista será diferente dependendo se está a utilizar uma máquina virtual Windows ou Linux. Clique na tabela **de eventos.** Isto inclui todos os eventos do registo de eventos do Windows. O Log Analytics abre com uma simples consulta para recuperar entradas de registo de eventos.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, ativou o Monitor Azure para VMs para uma máquina virtual e configurou o espaço de trabalho do Log Analytics para recolher eventos para o sistema operativo dos hóspedes. Para saber como ver e analisar os dados, avance para o tutorial.

> [!div class="nextstepaction"]
> [Ver ou analisar dados no Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
