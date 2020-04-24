---
title: Tutorial - Gerir a configuração da máquina virtual do Windows em Azure
description: Neste tutorial, você aprende como identificar alterações e gerir atualizações de pacotes numa máquina virtual do Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ed36dc669c8b89ba4a2b7831c6eb6f8742e73730
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100418"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorizar alterações e atualizar uma máquina virtual do Windows em Azure

Com o Azure [Change Tracking](../../automation/change-tracking.md) and [Update Management,](../../automation/automation-update-management.md)pode identificar facilmente alterações nas suas máquinas virtuais windows no Azure e gerir atualizações do sistema operativo para esses VMs.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerir as atualizações do Windows.
> * Monitorizar alterações e inventário.

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

A Azure Cloud Shell é uma concha interativa gratuita que pode suster para executar os passos deste artigo. Tem ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta Azure.

Para abrir qualquer bloco de código na Cloud Shell, basta selecionar **experimente** a partir do canto superior direito desse bloco de código.

Você também pode abrir Cloud Shell em [https://shell.azure.com/powershell](https://shell.azure.com/powershell)um separado de navegador indo para . Selecione **Copiar** para copiar blocos de código, cole-os no separador Cloud Shell e selecione a tecla Enter para executar o código.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para configurar a monitorização e a gestão de atualizações do Azure neste tutorial, precisa de uma VM do Windows no Azure.

Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Em seguida, crie o VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo seguinte cria `myVM` um `East US` VM nomeado no local. Se eles já não existirem, `myResourceGroupMonitor` o grupo de recursos e os recursos de rede de apoio são criados:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos até que os recursos e a VM sejam criados.

## <a name="manage-windows-updates"></a>Gerir atualizações do Windows

Update Management ajuda-o a gerir atualizações e patches para os seus VMs Do Windows Azure. Diretamente do seu VM, pode rapidamente:

- Avaliar o estado das atualizações disponíveis.
- Agendar a instalação das atualizações necessárias.
- Os resultados da implementação da revisão para verificar as atualizações foram aplicados com sucesso ao VM.

Para obter informações sobre preços, consulte [os preços da Automatização para a gestão de Atualizações.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

Para ativar a Gestão de Atualizações para o seu VM:

1. No lado esquerdo da janela, selecione **máquinas virtuais**.
1. Escolha um VM da lista.
1. No painel **de operações** da janela VM, selecione Gestão de **Atualização**.
1. Abre-se a janela **Enable Update Management.**

A validação é feita para determinar se a Atualização está ativada para este VM. A validação inclui verificações para um espaço de trabalho log Analytics, para uma conta de Automação ligada, e para se a solução está no espaço de trabalho.

Utiliza um espaço de trabalho [de Log Analytics](../../log-analytics/log-analytics-overview.md) para recolher dados gerados por funcionalidades e serviços como a Atualização de Gestão. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

Para realizar ações adicionais em VMs que requerem atualizações, pode utilizar a Automatização Azure para executar livros de execução contra VMs. Tais ações incluem o download ou aplicação de atualizações.

O processo de validação também verifica se o VM está aprovisionado com o Microsoft Monitoring Agent (MMA) e o Automation Hybrid Runbook Worker. Utiliza o agente para comunicar com o VM e obter informações sobre o estado da atualização.

Na janela **Enable Update Management,** escolha o espaço de trabalho e a conta de automatização do Log Analytics e, em seguida, selecione **Enable**. A solução demora até 15 minutos a ser ativada.

Qualquer um dos seguintes pré-requisitos que faltam durante o embarque são automaticamente adicionados:

* [Log Analytics](../../log-analytics/log-analytics-overview.md) espaço de trabalho
* [Automatização](../../automation/automation-offering-get-started.md)
* Um trabalhador híbrido do livro de [corridas,](../../automation/automation-hybrid-runbook-worker.md)que está habilitado no VM

Depois de ativada a solução, abre-se a janela de **gestão da Atualização.** Configure a localização, log Analytics workspace e conta de Automação para usar e, em seguida, **selecione Ativar**. Se estas opções parecerem diminuídas, outra solução de automatização está ativada para o VM, e a conta de espaço de trabalho e automação da solução deve ser utilizada.

![Ativar a solução de Gestão de Atualização](./media/tutorial-monitoring/manageupdates-update-enable.png)

A solução De Gestão de Atualização pode demorar até 15 minutos a ser ativada. Durante este período, não feche a janela do browser. Após a solução ser ativada, as informações sobre as atualizações em falta nos fluxos vm para os registos do Monitor Azure. Pode levar de 30 minutos a 6 horas para os dados ficarem disponíveis para análise.

### <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Após a ativação da Atualização, aparece a janela de gestão da **Atualização.** Após a avaliação das atualizações estar concluída, consulte uma lista de atualizações em falta no separador **de atualizações Em falta.**

 ![Ver o estado de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Escolha quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implementação de atualização para o VM, selecione a implementação da **atualização do Schedule** no topo da janela de gestão de **atualizações.** Na nova janela de implementação da **atualização,** especifique as seguintes informações:

| Opção | Descrição |
| --- | --- |
| **Nome** |Introduza um nome único para identificar a implementação da atualização. |
|**Sistema Operativo**| Selecione **Linux** ou **Windows**.|
| **Grupos para atualizar** |Para VMs hospedados no Azure, defina uma consulta baseada numa combinação de subscrição, grupos de recursos, localizações e etiquetas. Esta consulta constrói um grupo dinâmico de VMs hospedados em Azure para incluir na sua implementação. </br></br>Para VMs não hospedados no Azure, selecione uma pesquisa guardada existente. Com esta pesquisa, pode selecionar um grupo destes VMs para incluir na implementação. </br></br> Para saber mais, consulte [Grupos Dinâmicos.](../../automation/automation-update-management-groups.md)|
| **Máquinas para atualizar** |Selecione **pesquisa saved,** **grupo importado**ou **máquinas**.<br/><br/>Se selecionar **Máquinas,** pode escolher máquinas individuais a partir da lista de lançamentos. A prontidão de cada máquina é mostrada na coluna DE PRONTIDÃO DO **AGENTE ATUALIZAÇÃO** da tabela.</br></br> Para conhecer os diferentes métodos de criação de grupos informáticos nos registos do Monitor Azure, consulte [grupos informáticos em registos do Monitor Azure](../../azure-monitor/platform/computer-groups.md) |
|**Classificações de atualização**|Escolha todas as classificações de atualização necessárias.|
|**Incluir/excluir atualizações**|Selecione esta opção para abrir o painel **Incluir/Excluir.** As atualizações a incluir e as que devem ser excluídas estão em separados separados. Para obter mais informações sobre como a inclusão é tratada, consulte [Agendar uma Implementação de Atualização](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment). |
|**Configurações de agenda**|Escolha a hora de começar e selecione **uma vez** ou **recorrente**.|
| **Pré-scripts + Post-scripts**|Escolha os scripts para executar antes e depois da sua implementação.|
| **Janela de manutenção** | Introduza o número de minutos definidos para atualizações. Os valores válidos variam entre os 30 e os 360 minutos. |
| **Controlo de reiniciar**| Selecione como as reinicializações são manipuladas. As seleções disponíveis são:<ul><li>**Reiniciar se necessário**</li><li>**Reiniciar sempre**</li><li>**Nunca reiniciar**</li><li>**Apenas reiniciar**</li></ul>**Reiniciar se necessário** é a seleção padrão. Se selecionar **Apenas reiniciar,** as atualizações não estão instaladas.|

Depois de terminar de configurar a programação, clique em **Criar** para voltar ao painel de status. A tabela **agendada** mostra o calendário de implantação que criou.

Também pode criar implementações de atualizações programáticamente. Para aprender a criar uma implementação de atualização com a Rest API, consulte configurações de [atualização](/rest/api/automation/softwareupdateconfigurations/create)de software - Criar . Há também um livro de amostras que pode usar para criar uma implementação semanal de atualização. Para saber mais sobre este livro de execução, consulte Criar uma implementação semanal de [atualização para um ou mais VMs num grupo](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)de recursos .

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação programada, pode ver o estado de implementação no separador de **implementações de Atualizações** da janela de gestão de **atualização.**

Se a implantação estiver em execução, o seu estado mostra-se como "Em curso". Após a conclusão bem sucedida, o estado muda para "Succeeded". Mas se alguma atualização na implementação falhar, o estado é "Parcialmente falhado".

Selecione a implementação completa da atualização para ver o painel de instrumentos para essa implementação.

![Dashboard de estado de Implementação de Atualização para uma implementação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

O azulejo de resultados da **Atualização** mostra um resumo do número total de atualizações e resultados de implementação no VM. A tabela à direita mostra uma desagregação detalhada de cada atualização e os resultados da instalação. Cada resultado tem um dos seguintes valores:

* **Não tentado**: A atualização não está instalada. Não havia tempo suficiente disponível com base na duração definida da janela de manutenção.
* **Sucesso**: A atualização foi bem sucedida.
* **Falha :** A atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione o azulejo **de saída** para ver o fluxo de trabalho do livro de execução responsável pela gestão da implementação da atualização no VM alvo.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros de implementação.

## <a name="monitor-changes-and-inventory"></a>Monitorizar alterações e inventário

Pode recolher e visualizar um inventário do software, ficheiros, daemons Linux, serviços Windows e chaves de registo windows nos seus computadores. Rastrear as configurações das suas máquinas ajuda-o a identificar problemas operacionais em todo o seu ambiente e a compreender melhor o estado das suas máquinas.

### <a name="enable-change-and-inventory-management"></a>Ativar a gestão de alterações e inventários

Para permitir a gestão de alterações e inventários para o seu VM:

1. No lado esquerdo da janela, selecione **máquinas virtuais**.
1. Escolha um VM da lista.
1. No âmbito **das operações** na janela VM, selecione o **inventário** ou o rastreio **de alteração**.
1. Abre-se o painel **de rastreio e inventário de alterações** ativas.

Configure a localização, o espaço de trabalho do Log Analytics e a conta de Automação para utilizar e, em seguida, selecione **Enable**. Se as opções parecerem diminuídas, uma solução de automação já está ativada para o VM. Nesse caso, deve ser utilizada a conta de espaço de trabalho e automação já ativada.

Mesmo que as soluções apareçam separadamente no menu, são a mesma solução. Ativar uma ativa a outra na VM.

![Ativar o Controlo de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois de a solução ter sido ativada, pode levar algum tempo para que o inventário seja recolhido no VM antes de aparecerem os dados.

### <a name="track-changes"></a>Controlar as alterações

No seu VM em **OPERAÇÕES,** selecione **'Change Tracking'** e, em seguida, selecione **Definições de Edição**. O painel **change tracking** abre. Selecione o tipo de definição que pretende controlar e selecione **+Adicionar** para configurar as definições.

As opções de definições disponíveis para Windows são:

* Registo do Windows
* Ficheiros do Windows

Para obter informações detalhadas sobre o Change Tracking, consulte [alterações de Resolução de Problemas num VM](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Ver o inventário

No seu VM selecione **Inventário** em **OPERAÇÕES**. No separador **Software,** há uma tabela que mostra o software que foi encontrado. Os detalhes de alto nível para cada registo de software aparecem na tabela. Estes detalhes incluem o nome do software, versão, editor e última hora renovada.

![Ver o inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorizar registos e alterações de atividades

A partir da janela de **rastreio change** no seu VM, selecione Manage Activity **Log Connection** para abrir o painel de log da **Atividade Azure.** Selecione **Ligar** para ligar o Rastreio de Mudança ao registo de atividade do Azure para o seu VM.

Depois de ativar o Change Tracking, vá ao painel de **visão geral** para o seu VM e selecione **Stop** para parar o vM. Quando lhe for pedido, selecione **Sim** para parar a VM. Depois de o VM ser deallo, selecione **Iniciar** a reiniciar o seu VM.

Parar e reiniciar um VM regista um evento no seu registo de atividade. Volte para o painel de **rastreio Change** e selecione o separador **Eventos** na parte inferior do painel. Passado algum tempo, os eventos aparecem na tabela e na mesa. Pode selecionar cada evento para ver informações detalhadas para esse evento.

![Ver alterações no registo de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico anterior mostra alterações que ocorreram ao longo do tempo. Depois de adicionar uma ligação de Log de Atividade Azure, o gráfico de linha na parte superior exibe eventos de Log de Atividade Azure.

Cada linha de gráficos de barras representa um tipo de mudança rastreável diferente. Estes tipos são daemons Linux, ficheiros, chaves de registo do Windows, software e serviços Windows. O separador **Change** mostra os detalhes da mudança. As alterações aparecem na ordem de quando cada um ocorreu, com a mudança mais recente mostrada primeiro.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurae e reviu a Gestão de Rastreio e Atualização de Alterações para o seu VM. Aprendeu a:

> [!div class="checklist"]
> * Crie um grupo de recursos e VM.
> * Gerir as atualizações do Windows.
> * Monitorizar alterações e inventário.

Vá ao próximo tutorial para aprender sobre monitorizar o seu VM.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitor.md)
