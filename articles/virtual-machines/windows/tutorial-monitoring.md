---
title: Tutorial – Monitorizar e atualizar máquinas virtuais do Windows no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a monitorizar diagnósticos de arranque e métricas de desempenho e a gerir atualizações de pacotes numa máquina virtual do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4f10a1ae406ab8a560051ac6305df8f8f9fbbd7b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438640"
---
# <a name="tutorial-monitor-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorizar e atualizar uma máquina virtual do Windows no Azure

A monitorização do Azure utiliza agentes para recolher dados de arranque e desempenho das VMs do Azure, armazena estes dados no armazenamento do Azure e torna-os acessíveis através do portal, do módulo Azure PowerShell e da CLI do Azure. A gestão de atualizações permite-lhe gerir atualizações e correções para as suas VMs do Windows do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar diagnósticos de arranque numa VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião da VM
> * Instalar a extensão de diagnóstico
> * Ver métricas da VM
> * Criar um alerta
> * Gerir atualizações do Windows
> * Monitorizar alterações e inventário
> * Configurar a monitorização avançada

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

À medida que as máquinas virtuais do Windows arrancam, o agente de diagnóstico de arranque captura a saída de ecrã que pode ser utilizada para fins de resolução de problemas. Esta capacidade está ativada por predefinição. As capturas de ecrã capturadas são armazenadas numa conta de armazenamento do Azure, que também é criada por predefinição.

Pode obter os dados de diagnóstico de arranque com o comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). No exemplo seguinte, os diagnósticos de arranque são transferidos para a raiz da unidade *C:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Ver métricas de anfitrião

Uma VM do Windows tem um Anfitrião dedicado no Azure com o qual interage. As métricas são recolhidas automaticamente para o Anfitrião e podem ser visualizadas no portal do Azure.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Métricas** no painel da VM e, em seguida, selecione qualquer uma das métricas de Anfitrião em **Métricas disponíveis** para ver o desempenho da VM de Anfitrião.

    ![Ver métricas de anfitrião](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar a extensão de diagnóstico

As métricas de anfitrião básicas estão disponíveis, mas para ver métricas mais granulares e específicas da VM, tem de instalar a extensão de diagnóstico do Azure na VM. A extensão de diagnóstico do Azure permite que sejam obtidos dados de monitorização e diagnóstico adicionais da VM. Pode ver estas métricas de desempenho e criar alertas com base no desempenho da VM. A extensão de diagnóstico é instalada através do portal do Azure da seguinte forma:

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Definições de diagnóstico**. A lista mostra que os *Diagnósticos de arranque* já foram ativados na secção anterior. Clique na caixa de verificação para *Métricas básicas*.
3. Clique no botão **Ativar a monitorização ao nível do convidado**.

    ![Ver métricas de diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Ver métricas da VM

Pode ver as métricas da VM da mesma forma que vê as métricas da VM de anfitrião:

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Para ver o desempenho da VM, clique em **Métricas** no painel da VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **Métricas disponíveis**.

    ![Ver métricas da VM](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Criar alertas

Pode criar alertas com base em métricas de desempenho específicas. Podem ser utilizados alertas para notificá-lo quando a utilização média da CPU excede um determinado limiar ou o espaço livre em disco disponível diminuir até uma determinada quantidade, por exemplo. Os alertas são apresentados no portal do Azure ou podem ser enviados por e-mail. Também pode acionar runbooks da Automatização do Azure ou do Azure Logic Apps em resposta aos alertas gerados.

O exemplo seguinte cria um alerta para a utilização média da CPU.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Regras de alerta** no painel da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior do painel de alertas.
3. Indique um **Nome** para o alerta, como *myAlertRule*
4. Para acionar um alerta quando a percentagem da CPU excede 1.0 durante cinco minutos, mantenha todas as outras predefinições selecionadas.
5. Opcionalmente, selecione a caixa para *Proprietários, contribuidores e leitores do e-mail* para enviar uma notificação por e-mail. A ação predefinida é apresentar uma notificação no portal.
6. Clique no botão **OK**.

## <a name="manage-windows-updates"></a>Gerir atualizações do Windows

A gestão de atualizações permite-lhe gerir atualizações e correções para as suas VMs do Windows do Azure.
Diretamente a partir da VM, pode avaliar rapidamente o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM.

Para obter informações sobre preços, veja [Preços de Automatização para Gestão de atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Ativar a Gestão de atualizações

Ativar a Gestão de atualizações para a VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações**, clique em **Gestão de atualizações**. É aberto o ecrã **Ativar Gestão de Atualizações**.

A validação é executada para determinar se a Gestão de atualizações está ativada para esta VM.
A validação inclui a verificação da existência de uma área de trabalho do Log Analytics e da conta de Automatização ligada, e se a solução está na área de trabalho.

A área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md) serve para recolher dados gerados pelas funcionalidades e serviços, tais como a Gestão de atualizações.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.
Para executar ações adicionais em VMs que necessitam de atualizações, a Automatização do Azure permite executar runbooks nas VMs, tais como transferir e aplicar atualizações.

O processo de validação verifica ainda se a VM está aprovisionada com o Microsoft Monitoring Agent (MMA) e a função de trabalho de runbook híbrida de Automatização.
Este agente serve para comunicar com a VM e obter informações sobre o estado de atualização.

Escolha a área de trabalho e a conta de automatização do Log Analytics e clique em **Ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* Área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatização](../../automation/automation-offering-get-started.md)
* Uma [Função de trabalho de runbook híbrida](../../automation/automation-hybrid-runbook-worker.md) está ativada na VM

O ecrã **Gestão de Atualizações** é apresentado. Configure a localização, a área de trabalho do Log Analytics e a conta de Automatização a utilizar e clique em **Ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

![Ativar a solução de Gestão de atualizações](./media/tutorial-monitoring/manageupdates-update-enable.png)

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser. Após a ativação da solução, as informações sobre atualizações em falta na VM são transmitidas ao Log Analytics. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Após a conclusão da avaliação de atualizações, verá uma lista de atualizações em falta no separador **Atualizações em falta**.

 ![Ver o estado de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova Implementação de Atualização para a VM, clique em **Agendar a implementação da atualização** na parte superior do ecrã **Gestão de atualizações**. No ecrã **Nova implementação de atualização**, especifique as seguintes informações:

* **Nome** - Indique um nome exclusivo para identificar a implementação de atualizações.
* **Classificação da atualização** - Selecione os tipos de software que a implementação da atualização incluiu na implementação. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Update rollups
  * Pacotes de funcionalidades
  * Service packs
  * Atualizações de definições
  * Ferramentas
  * Atualizações

* **Definições da agenda** - Pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual, ou especificar uma hora diferente.
  Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Clique na opção Periódico, em Periodicidade, para configurar um agendamento periódico.

  ![Ecrã de Definições de Agendamento de Atualizações](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Janela de manutenção (minutos)** - Especifique o período de tempo no qual pretende que a implementação da atualização ocorra.  Isto ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

Depois de concluir a configuração do agendamento, clique no botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que a tabela **Agendada** mostra o agendamento da implementação que criou.

> [!WARNING]
> Quanto a atualizações que requerem reinício, a VM é reiniciada automaticamente.

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** no ecrã **Gestão de atualizações**.
Se estiver em execução, o respetivo estado é apresentado como **Em curso**. Depois de concluir, se for bem sucedida, muda para **Com êxito**.
Se existir uma falha numa ou mais atualizações na implementação, o estado é **Falha parcial**.
Clique na implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

![Dashboard de estado de Implementação de Atualização para uma implementação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

No mosaico **Resultados da atualização** encontra-se um resumo do número total de atualizações e os resultados de implementação da VM.
Na tabela à direita encontra-se uma divisão detalhada de cada atualização e os resultados da instalação, que podem ter um dos seguintes valores:

* **Não tentado** - a atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Com êxito** - a atualização foi executada com êxito.
* **Falhou** - a atualização falhou.

Clique em **Todos os registos** para ver todas as entradas de registo que a implementação criou.

Clique no mosaico **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="monitor-changes-and-inventory"></a>Monitorizar alterações e inventário

Pode recolher e ver o inventário do software, dos ficheiros, dos daemons do Linux, dos Serviços do Windows e das chaves do Registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

### <a name="enable-change-and-inventory-management"></a>Ativar a Gestão de alterações e de inventário

Ativar a Gestão de alterações e de inventário na VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações**, clique em **Inventário** ou em **Controlo de alterações**. É aberto o ecrã **Ativar o Controlo de Alterações e Inventário**.

Configure a localização, a área de trabalho do Log Analytics e a conta de Automatização a utilizar e clique em **Ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização. Apesar de as soluções estarem separadas no menu, tratam-se da mesma solução. Ativar uma ativa a outra na VM.

![Ativar o Controlo de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Após a ativação da solução, o inventário poderá demorar algum tempo a ser recolhido na VM antes de aparecerem dados.

### <a name="track-changes"></a>Controlar as alterações

Na sua VM, selecione **Controlo de Alterações**, em **OPERAÇÕES**. Clique em **Editar Definições**. É apresentada a página **Controlo de Alterações**. Selecione o tipo de definição que pretende controlar e clique em **+Adicionar** para configurar as definições. As opções disponíveis para o Windows são:

* Registo do Windows
* Ficheiros do Windows

Para obter informações detalhadas sobre o Controlo de Alterações, veja [Resolver problemas relacionados com alterações numa VM](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Ver o inventário

Na sua VM, selecione **Inventário**, em **OPERAÇÕES**. No separador **Software**, existe uma tabela que lista o software que foi encontrado. Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome, a versão, o fabricante e a hora da última atualização do software.

![Ver o inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorizar os Registos de Atividade e as alterações

Na página **Controlo de alterações** da sua VM, selecione **Gerir Ligação de Registo de Atividades**. Esta tarefa abre a página **Registo de atividades do Azure**. Selecione **Ligar** para ligar o Controlo de alterações ao registo de atividades do Azure para a sua VM.

Com esta definição ativada, navegue para a página **Descrição Geral** da sua VM e selecione **Parar** para parar a VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Quando esta estiver desalocada, selecione **Iniciar** para reiniciar a sua VM.

As operações de paragem e início de uma VM registam um evento no respetivo registo de atividades. Regresse à página **Controlo de alterações**. Selecione o separador **Eventos** na parte inferior da página. Passado algum tempo, os eventos são apresentados no gráfico e na tabela. É possível selecionar cada um dos eventos para ver informações detalhadas sobre os mesmos.

![Ver alterações no registo de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico mostra as alterações ocorridas ao longo do tempo. Após ter adicionado uma ligação do Registo de Atividades, o gráfico de linhas existente na parte superior apresenta os eventos do Registo de Atividades do Azure. Cada linha do gráfico de barras representa um tipo diferente de alteração passível de controlo. Estes tipos incluem daemons do Linux, ficheiros, chaves do Registo do Windows, software e serviços do Windows. O separador Alterações mostra os detalhes das alterações apresentadas na visualização por ordem descendente de data/hora em que a alteração ocorreu (mais recente primeiro).

## <a name="advanced-monitoring"></a>Monitorização avançada

Pode monitorizar a sua VM de forma mais avançada com as soluções como a Gestão de Atualizações e a Alteração e Inventário que a [Automatização do Azure](../../automation/automation-intro.md) proporciona.

Quando tiver acesso à área de trabalho do Log Analytics, pode localizar a chave e o identificador da mesma ao selecionar **Definições avançadas**, em **DEFINIÇÕES**. Utilize o [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) comando para adicionar a extensão do Microsoft Monitoring agent para a VM. Atualize os valores das variáveis no exemplo abaixo para refletir a chave e o ID da área de trabalho do Log Analytics.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzVMExtension -ResourceGroupName "myResourceGroupMonitor" `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName "myVM" `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location "East US"
```

Após alguns minutos, deverá ver a VM nova na área de trabalho do Log Analytics.

![Painel do Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou e reviu as VMs com o Centro de Segurança do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um grupo de recursos e uma VM
> * Ativar diagnósticos de arranque na VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião
> * Instalar a extensão de diagnóstico
> * Ver métricas da VM
> * Criar um alerta
> * Gerir atualizações do Windows
> * Monitorizar alterações e inventário
> * Configurar a monitorização avançada

Avance para o tutorial seguinte para saber mais sobre o centro de segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir a segurança de VMs](./tutorial-azure-security.md)
