---
title: Tutorial - Monitor Windows máquinas virtuais em Azure
description: Neste tutorial, aprende-se a monitorizar o desempenho e descobriu componentes de aplicações em execução nas suas máquinas virtuais Windows.
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
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74112483"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorize uma máquina virtual do Windows em Azure

A monitorização do Azure utiliza agentes para recolher dados de arranque e desempenho de VMs Azure, armazenar estes dados no armazenamento Azure, e torná-lo acessível através do portal, do módulo Azure PowerShell e do Azure CLI. A monitorização avançada é entregue com o Monitor Azure para VMs através da recolha de métricas de desempenho, descobrir componentes de aplicação instalados no VM, e inclui gráficos de desempenho e mapa de dependência.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar diagnósticos de arranque numa VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião da VM
> * Ativar o Monitor Azure para VMs
> * Ver métricas de desempenho vm
> * Criar um alerta

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um [https://shell.azure.com/powershell](https://shell.azure.com/powershell)separado separado browser, indo para . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para configurar a monitorização e a gestão de atualizações do Azure neste tutorial, precisa de uma VM do Windows no Azure. Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie o VM com [New-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) O exemplo seguinte cria uma VM com o nome *myVM* na localização *EastUS*. Se ainda não existirem, serão criados o grupo de recursos *myResourceGroupMonitorMonitor* e recursos de rede de apoio:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos até que os recursos e a VM sejam criados.

## <a name="view-boot-diagnostics"></a>Ver diagnósticos de arranque

À medida que as máquinas virtuais do Windows arrancam, o agente de diagnóstico de arranque captura a saída de ecrã que pode ser utilizada para fins de resolução de problemas. Esta capacidade está ativada por predefinição. As imagens capturadas são armazenadas numa conta de armazenamento Azure, que também é criada por padrão.

Pode obter os dados de diagnóstico de arranque com o comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). No exemplo seguinte, os diagnósticos de arranque são transferidos para a raiz da unidade *C:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Ver métricas de anfitrião

Uma VM do Windows tem um Anfitrião dedicado no Azure com o qual interage. As métricas são recolhidas automaticamente para o Anfitrião e podem ser visualizadas no portal do Azure.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Métricas** no painel da VM e, em seguida, selecione qualquer uma das métricas de Anfitrião em **Métricas disponíveis** para ver o desempenho da VM de Anfitrião.

    ![Ver métricas de anfitrião](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Ativar monitorização avançada

Para permitir a monitorização do seu VM Azure com monitor Azure para VMs:

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página VM, na secção **monitora,** selecione **Insights (pré-visualização)**.

3. Na página **Insights (pré-visualização),** selecione **Tente agora**.

    ![Ativar o Monitor Azure para VMs para um VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Na página de **Onboarding Do Monitor Azure** Insights, se tiver um espaço de trabalho existente no Log Analytics na mesma subscrição, selecione-o na lista de drop-down.  

    A lista pré-seleciona o espaço de trabalho padrão e a localização onde o VM é implantado na subscrição. 

    >[!NOTE]
    >Para criar um novo espaço de trabalho log analytics para armazenar os dados de monitorização do VM, consulte Criar um espaço de [trabalho Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). O seu espaço de trabalho Log Analytics deve pertencer a uma das [regiões apoiadas.](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)

Depois de ter ativado a monitorização, poderá ter de esperar alguns minutos para poder ver as métricas de desempenho do VM.

![Ativar o Monitor Azure para monitorização de VMs processamento de implementação](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Ver métricas de desempenho vm

O Monitor Azure para VMs inclui um conjunto de gráficos de desempenho que visam vários indicadores de desempenho chave (KPIs) para ajudá-lo a determinar o quão bem uma máquina virtual está a funcionar. Para aceder ao seu VM, execute os seguintes passos.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página VM, na secção **monitora,** selecione **Insights (pré-visualização)**.

3. Selecione o separador **Performance.**

Esta página não só inclui gráficos de utilização de desempenho, mas também uma tabela que mostra cada disco lógico descoberto, a sua capacidade, utilização e média total por cada medida.

## <a name="create-alerts"></a>Criar alertas

Pode criar alertas com base em métricas de desempenho específicas. Podem ser utilizados alertas para notificá-lo quando a utilização média da CPU excede um determinado limiar ou o espaço livre em disco disponível diminuir até uma determinada quantidade, por exemplo. Os alertas são apresentados no portal do Azure ou podem ser enviados por e-mail. Também pode acionar runbooks da Automatização do Azure ou do Azure Logic Apps em resposta aos alertas gerados.

O exemplo seguinte cria um alerta para a utilização média da CPU.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Clique em **Regras de alerta** no painel da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior do painel de alertas.

3. Indique um **Nome** para o alerta, como *myAlertRule*

4. Para acionar um alerta quando a percentagem da CPU excede 1.0 durante cinco minutos, mantenha todas as outras predefinições selecionadas.

5. Opcionalmente, selecione a caixa para *Proprietários, contribuidores e leitores do e-mail* para enviar uma notificação por e-mail. A ação predefinida é apresentar uma notificação no portal.

6. Clique no botão **OK.**

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste e viste o desempenho do teu VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Ativar diagnósticos de arranque na VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião
> * Ativar o Monitor Azure para VMs
> * Ver métricas da VM
> * Criar um alerta

Avance para o próximo tutorial para saber mais sobre o Centro de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir segurança de VM](../../security/fundamentals/overview.md)
