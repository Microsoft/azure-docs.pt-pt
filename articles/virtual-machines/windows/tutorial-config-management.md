---
title: Tutorial – gerenciar a configuração de máquina virtual do Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a identificar alterações e gerenciar atualizações de pacote em uma máquina virtual do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 96e73b228604db519beb5284ee5a8fb8dc4c4f66
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376108"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: monitorar alterações e atualizar uma máquina virtual do Windows no Azure

O Azure [controle de alterações](../../automation/change-tracking.md) permite que você identifique facilmente as alterações e [Gerenciamento de atualizações](../../automation/automation-update-management.md) permite que você gerencie atualizações do sistema operacional para suas VMs do Windows do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerir atualizações do Windows
> * Monitorizar alterações e inventário

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

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

## <a name="manage-windows-updates"></a>Gerir atualizações do Windows

Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure.
Diretamente a partir da VM, pode avaliar rapidamente o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM.

Para obter informações sobre preços, consulte [preços de automação para o gerenciamento de atualizações](https://azure.microsoft.com/pricing/details/automation/).

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

Escolha o espaço de trabalho Log Analytics e a conta de automação e clique em **habilitar** para habilitar a solução. A solução demora até 15 minutos a ativar.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* Área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatização](../../automation/automation-offering-get-started.md)
* Uma [Função de trabalho de runbook híbrida](../../automation/automation-hybrid-runbook-worker.md) está ativada na VM

O ecrã **Gestão de Atualizações** é apresentado. Configure o local, Log Analytics o espaço de trabalho e a conta de automação para usar e clique em **habilitar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

![Ativar a solução de Gestão de atualizações](./media/tutorial-monitoring/manageupdates-update-enable.png)

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser. Depois que a solução é habilitada, as informações sobre as atualizações ausentes na VM fluem para Azure Monitor logs. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Após a conclusão da avaliação de atualizações, verá uma lista de atualizações em falta no separador **Atualizações em falta**.

 ![Ver o estado de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova Implementação de Atualização para a VM, clique em **Agendar a implementação da atualização** na parte superior do ecrã **Gestão de atualizações**. No ecrã **Nova implementação de atualização**, especifique as seguintes informações:

Para criar uma nova implantação de atualização, selecione **agendar implantação de atualização**. A página **nova implantação de atualizações** é aberta. Insira valores para as propriedades descritas na tabela a seguir e clique em **criar**:

| Propriedade | Descrição |
| --- | --- |
| Nome |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema Operativo| Linux ou Windows|
| Grupos a serem atualizados |Para computadores do Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e marcas para criar um grupo dinâmico de VMs do Azure para incluir em sua implantação. </br></br>Para computadores não Azure, selecione uma pesquisa salva existente para selecionar um grupo de computadores não Azure a serem incluídos na implantação. </br></br>Para saber mais, confira [grupos dinâmicos](../../automation/automation-update-management-groups.md)|
| Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou escolha a máquina na lista suspensa e selecione computadores individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores em logs de Azure Monitor, consulte [grupos de computadores em logs de Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|Classificações de atualização|Selecione todas as classificações de atualização de que você precisa|
|Incluir/excluir atualizações|Isso abre a página **incluir/excluir** . As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter mais informações sobre como a inclusão é tratada, consulte [agendar uma implantação de atualização](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment) |
|Configurações de agendamento|Selecione a hora para iniciar e selecione uma vez ou recorrente para a recorrência|
| Pré-scripts + pós-scripts|Selecione os scripts a serem executados antes e depois da implantação|
| Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos e não mais do que 6 horas |
| Controle de reinicialização| Determina como as reinicializações devem ser tratadas. As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

As implantações de atualização também podem ser criadas programaticamente. Para saber como criar uma implantação de atualização com a API REST, consulte [Software Update Configurations-Create](/rest/api/automation/softwareupdateconfigurations/create). Também há um runbook de exemplo que pode ser usado para criar uma implantação semanal de atualização. Para saber mais sobre esse runbook, confira [criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Depois de concluir a configuração do agendamento, clique no botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que a tabela **Agendada** mostra o agendamento da implementação que criou.

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

Configure o local, Log Analytics o espaço de trabalho e a conta de automação para usar e clique em **habilitar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização. Apesar de as soluções estarem separadas no menu, tratam-se da mesma solução. Ativar uma ativa a outra na VM.

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou e examinou Controle de Alterações e Gerenciamento de Atualizações para sua VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Gerir atualizações do Windows
> * Monitorizar alterações e inventário

Avance para o próximo tutorial para saber mais sobre como monitorar sua VM.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitor.md)
