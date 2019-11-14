---
title: Tutorial-monitorar máquinas virtuais do Windows no Azure
description: Neste tutorial, você aprenderá a monitorar o desempenho e os componentes de aplicativos descobertos em execução nas máquinas virtuais do Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 13e5cc9ee45cf230668ef7a7cbe85b6437044643
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064760"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Tutorial: monitorar uma máquina virtual do Windows no Azure

O monitoramento do Azure usa agentes para coletar dados de inicialização e de desempenho de VMs do Azure, armazenar esses dados no armazenamento do Azure e torná-los acessíveis por meio do portal, do módulo Azure PowerShell e do CLI do Azure. O monitoramento avançado é fornecido com o Azure Monitor para VMs coletando métricas de desempenho, descobrir componentes de aplicativos instalados na VM e inclui gráficos de desempenho e mapa de dependências.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar diagnósticos de arranque numa VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião da VM
> * Habilitar Azure Monitor para VMs
> * Exibir métricas de desempenho da VM
> * Criar um alerta

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para configurar a monitorização e a gestão de atualizações do Azure neste tutorial, precisa de uma VM do Windows no Azure. Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, crie a VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVM* na localização *EastUS*. Se ainda não existirem, serão criados o grupo de recursos *myResourceGroupMonitorMonitor* e recursos de rede de apoio:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos até que os recursos e a VM sejam criados.

## <a name="view-boot-diagnostics"></a>Ver diagnósticos de arranque

À medida que as máquinas virtuais do Windows arrancam, o agente de diagnóstico de arranque captura a saída de ecrã que pode ser utilizada para fins de resolução de problemas. Esta capacidade está ativada por predefinição. As capturas de tela capturadas são armazenadas em uma conta de armazenamento do Azure, que também é criada por padrão.

Pode obter os dados de diagnóstico de arranque com o comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). No exemplo seguinte, os diagnósticos de arranque são transferidos para a raiz da unidade *C:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Ver métricas de anfitrião

Uma VM do Windows tem um Anfitrião dedicado no Azure com o qual interage. As métricas são recolhidas automaticamente para o Anfitrião e podem ser visualizadas no portal do Azure.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Métricas** no painel da VM e, em seguida, selecione qualquer uma das métricas de Anfitrião em **Métricas disponíveis** para ver o desempenho da VM de Anfitrião.

    ![Ver métricas de anfitrião](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Habilitar monitoramento avançado

Para habilitar o monitoramento de sua VM do Azure com o Azure Monitor para VMs:

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)** .

3. Sobre o **Insights (pré-visualização)** página, selecione **Experimente agora o**.

    ![Ativar o Azure Monitor para as VMs para uma VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Sobre o **integração de informações do Azure Monitor** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição, selecione-o na lista pendente.  

    A lista seleciona o espaço de trabalho padrão e o local onde a VM é implantada na assinatura. 

    >[!NOTE]
    >Para criar um novo espaço de trabalho Log Analytics para armazenar os dados de monitoramento da VM, consulte [criar um espaço de trabalho log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Seu espaço de trabalho do Log Analytics deve pertencer a uma das [regiões com suporte](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Depois de habilitar o monitoramento, talvez seja necessário aguardar vários minutos para poder exibir as métricas de desempenho da VM.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Exibir métricas de desempenho da VM

Azure Monitor para VMs inclui um conjunto de gráficos de desempenho que visam vários KPIs (indicadores chave de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Para acessar a partir de sua VM, execute as etapas a seguir.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)** .

3. Selecione a guia **desempenho** .

Esta página não inclui apenas gráficos de utilização de desempenho, mas também uma tabela que mostra cada disco lógico descoberto, sua capacidade, utilização e média total por cada medida.

## <a name="create-alerts"></a>Criar alertas

Pode criar alertas com base em métricas de desempenho específicas. Podem ser utilizados alertas para notificá-lo quando a utilização média da CPU excede um determinado limiar ou o espaço livre em disco disponível diminuir até uma determinada quantidade, por exemplo. Os alertas são apresentados no portal do Azure ou podem ser enviados por e-mail. Também pode acionar runbooks da Automatização do Azure ou do Azure Logic Apps em resposta aos alertas gerados.

O exemplo seguinte cria um alerta para a utilização média da CPU.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Clique em **Regras de alerta** no painel da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior do painel de alertas.

3. Forneça um **Nome** para o alerta, como *myAlertRule*

4. Para acionar um alerta quando a percentagem da CPU excede 1.0 durante cinco minutos, mantenha todas as outras predefinições selecionadas.

5. Opcionalmente, selecione a caixa para *Proprietários, contribuidores e leitores do e-mail* para enviar uma notificação por e-mail. A ação predefinida é apresentar uma notificação no portal.

6. Clique no botão **OK**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou e exibiu o desempenho de sua VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Ativar diagnósticos de arranque na VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião
> * Habilitar Azure Monitor para VMs
> * Ver métricas da VM
> * Criar um alerta

Avance para o próximo tutorial para saber mais sobre o Centro de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir a segurança de VMs](../../security/fundamentals/overview.md)
