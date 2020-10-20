---
title: Tutorial - Gerir a configuração da máquina virtual do Windows no Azure
description: Neste tutorial, aprende-se a identificar alterações e a gerir atualizações de pacotes numa máquina virtual do Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8d6e10b025b9e9524982b1558beacfab1970eb59
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216441"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorize alterações e atualize uma máquina virtual do Windows em Azure

Com a Azure [Change Tracking](../../automation/change-tracking/overview.md) and [Update Management,](../../automation/update-management/overview.md)pode facilmente identificar alterações nas suas máquinas virtuais do Windows em Azure e gerir as atualizações do sistema operativo para esses VMs.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Gerir as atualizações do Windows.
> * Monitorize alterações e inventário.

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar os passos neste artigo. Tem ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta Azure.

Para abrir qualquer bloco de código em Cloud Shell, basta **selecioná-lo** a partir do canto superior direito do bloco de código.

Também pode abrir o Cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copy** para copiar blocos de código, cole-os no separador Cloud Shell e selecione a tecla 'Introduzir' para executar o código.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para configurar a monitorização e a gestão de atualizações do Azure neste tutorial, precisa de uma VM do Windows no Azure.

Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1):

```azurepowershell-interactive
$cred = Get-Credential
```

Em seguida, crie o VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria um VM nomeado `myVM` no `East US` local. Se já não existirem, o grupo de recursos `myResourceGroupMonitor` e os recursos de rede de apoio são criados:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos até que os recursos e a VM sejam criados.

## <a name="manage-windows-updates"></a>Gerir atualizações do Windows

A Atualização Management ajuda-o a gerir atualizações e patches para os seus VMs do Azure Windows. Diretamente do seu VM, pode rapidamente:

- Avaliar o estado das atualizações disponíveis.
- Agendar a instalação das atualizações necessárias.
- Os resultados da implementação da revisão para verificar as atualizações foram aplicados com sucesso ao VM.

Para obter informações sobre preços, consulte [os preços da Automatização para a gestão de Atualização](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

Para ativar a Gestão de Atualização para o seu VM:

1. No lado esquerdo da janela, selecione **máquinas Virtuais**.
1. Escolha um VM da lista.
1. No painel de **operações** da janela VM, selecione **a gestão de Atualização**.
1. Abre-se a janela **"Gestão de Atualização ativa".**

A validação é feita para determinar se a Gestão de Atualização está ativada para este VM. A validação inclui verificações de um espaço de trabalho Log Analytics, para uma conta de Automação ligada, e para saber se a solução está no espaço de trabalho.

Utiliza um espaço de trabalho [log Analytics](../../azure-monitor/log-query/log-query-overview.md) para recolher dados gerados por funcionalidades e serviços como a Gestão de Atualizações. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

Para realizar ações adicionais em VMs que requerem atualizações, pode utilizar a Azure Automation para executar runbooks contra VMs. Tais ações incluem o download ou aplicação de atualizações.

O processo de validação também verifica se o VM é a provisionado com o Microsoft Monitoring Agent (MMA) e Automation Hybrid Runbook Worker. Utiliza o agente para comunicar com o VM e obter informações sobre o estado da atualização.

Na janela Gestão de **Atualização ativa,** escolha o espaço de trabalho e a conta de automatização do Log Analytics e, em seguida, selecione **Enable**. A solução leva até 15 minutos para ficar ativada.

Qualquer um dos seguintes pré-requisitos que faltam durante o embarque são automaticamente adicionados:

* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) espaço de trabalho
* [Automatização](../../automation/index.yml)
* Um [trabalhador de runbook híbrido,](../../automation/automation-hybrid-runbook-worker.md)que está habilitado no VM

Depois de ativar a solução, abre-se a janela **de gestão 'Actualização'.** Configure a localização, log Analytics espaço de trabalho e conta de automação para usar e, em seguida, selecione **Enable**. Se estas opções parecerem escurecidas, outra solução de automação está ativada para o VM, e essa solução e conta de Automação devem ser utilizadas.

![Ativar a solução de Gestão de Atualização](./media/tutorial-monitoring/manageupdates-update-enable.png)

A solução de Gestão de Atualização pode demorar até 15 minutos para ficar ativada. Durante este período, não feche a janela do browser. Após a solução estar ativada, informações sobre atualizações em falta nos fluxos VM para registos do Monitor Azure. Pode levar de 30 minutos a 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Após a gestão da atualização estar ativada, aparece a janela **de gestão de atualização.** Após a avaliação das atualizações estar concluída, vê uma lista de atualizações em falta no separador **Atualizações Desaparecidas.**

 ![Ver o estado de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Escolha quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implementação de atualização para o VM, selecione **a implementação** da atualização do calendário no topo da janela de **gestão de atualização.** Na janela de implantação da **nova atualização,** especifique as seguintes informações:

| Opção | Descrição |
| --- | --- |
| **Nome** |Introduza um nome único para identificar a implementação da atualização. |
|**Sistema operativo**| Selecione **Linux** ou **Windows**.|
| **Grupos a atualizar** |Para os VMs alojados no Azure, defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e tags. Esta consulta constrói um grupo dinâmico de VMs azure-hosted para incluir na sua implementação. </br></br>Para VMs não hospedados no Azure, selecione uma pesquisa guardada existente. Com esta pesquisa, pode selecionar um grupo destes VMs para incluir na implementação. </br></br> Para saber mais, consulte [Grupos Dinâmicos.](../../automation/update-management/configure-groups.md)|
| **Máquinas a atualizar** |Selecione **Pesquisa Save**, **Grupo importado**ou **Máquinas**.<br/><br/>Se selecionar **Máquinas,** pode escolher máquinas individuais da lista de drop-down. A prontidão de cada máquina é mostrada na coluna de **PRONTIDÃO DO AGENTE DE ATUALIZAÇÃO** da tabela.</br></br> Para conhecer os diferentes métodos de criação de grupos informáticos em registos do Monitor Azure, consulte [grupos de computador em registos do Monitor Azure](../../azure-monitor/platform/computer-groups.md) |
|**Classificações de atualizações**|Escolha todas as classificações de atualização necessárias.|
|**Incluir/excluir atualizações**|Selecione esta opção para abrir o painel **Incluir/Excluir.** As atualizações a incluir e as que serão excluídas estão em separado. Para obter mais informações sobre como a inclusão é tratada, consulte [Agendar uma Atualização.](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) |
|**Definições de agenda**|Escolha a hora de começar e selecione **uma vez** ou **recorrente**.|
| **Pré-scripts + Post-scripts**|Escolha os scripts para executar antes e depois da sua implantação.|
| **Janela de manutenção** | Introduza o número de minutos definidos para atualizações. Os valores válidos variam entre 30 e 360 minutos. |
| **Reiniciar o controlo**| Selecione como as reinicializações são manuseadas. As seleções disponíveis são:<ul><li>**Reiniciar se necessário**</li><li>**Reiniciar sempre**</li><li>**Nunca reiniciar**</li><li>**Apenas reiniciar**</li></ul>**Reiniciar se necessário** é a seleção predefinida. Se selecionar **Apenas reiniciar,** as atualizações não estão instaladas.|

Depois de ter terminado de configurar o horário, clique em **Criar** para voltar ao painel de estado. A tabela **Agendada** mostra o calendário de implantação que criou.

Também pode criar implementações de atualização programáticamente. Para aprender a criar uma implementação de atualização com a API REST, consulte [configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create). Há também um livro de amostras que pode usar para criar uma implementação semanal de atualização. Para saber mais sobre este livro de bordo, consulte [Criar uma atualização semanal para um ou mais VMs num grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação programada, pode ver o estado de implementação no separador **de implementações** de atualização da janela de **gestão de atualização.**

Se a implantação estiver atualmente em execução, o seu estado mostra como "em curso". Após a conclusão bem sucedida, o estado muda para "Sucesso". Mas se alguma atualização na implementação falhar, o estado é "Parcialmente falhado".

Selecione a atualização completa para ver o painel de instrumentos para essa implementação.

![Dashboard de estado de Implementação de Atualização para uma implementação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

O azulejo **de resultados** da atualização mostra um resumo do número total de atualizações e resultados de implantação no VM. A tabela à direita apresenta uma desagregação detalhada de cada atualização e dos resultados da instalação. Cada resultado tem um dos seguintes valores:

* **Não tentado:** A atualização não está instalada. Não havia tempo suficiente disponível com base na duração definida da janela de manutenção.
* **Sucesso**: A atualização foi bem sucedida.
* **Falha:** A atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione o azulejo **de saída** para ver o fluxo de trabalho do runbook responsável pela gestão da implementação da atualização no VM alvo.

Selecione **Errors** para ver informações detalhadas sobre eventuais erros de implementação.

## <a name="monitor-changes-and-inventory"></a>Monitorizar alterações e inventário

Pode recolher e visualizar um inventário do software, ficheiros, daemons Linux, serviços Windows e chaves de registo do Windows nos seus computadores. Rastrear as configurações das suas máquinas ajuda-o a identificar problemas operacionais em todo o seu ambiente e a compreender melhor o estado das suas máquinas.

### <a name="enable-change-and-inventory-management"></a>Permitir a mudança e a gestão de inventário

Para ativar a gestão de alterações e inventários para o seu VM:

1. No lado esquerdo da janela, selecione **máquinas Virtuais**.
1. Escolha um VM da lista.
1. Em **Operações** na janela VM, selecione **o rastreio de Inventário** ou **Alteração.**
1. **Abre-se o painel de rastreio de alterações ativa** e inventário.

Configure a localização, log analytics espaço de trabalho e conta de automação para usar e, em seguida, selecione **Enable**. Se as opções parecerem escurecidas, já está ativada uma solução de automação para o VM. Nesse caso, deve ser utilizado o espaço de trabalho já ativado e a conta automação.

Mesmo que as soluções apareçam separadamente no menu, são a mesma solução. Ativar uma ativa a outra na VM.

![Ativar o Controlo de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois de a solução ter sido ativada, pode levar algum tempo para que o inventário seja recolhido no VM antes que os dados apareçam.

### <a name="track-changes"></a>Controlar as alterações

No seu VM em **OPERAÇÕES,** selecione **'Alterar's Tracking'** e, em seguida, selecione **Definições de Edição**. O **painel de rastreio** de mudança abre. Selecione o tipo de definição que pretende controlar e selecione **+Adicionar** para configurar as definições.

As opções de definições disponíveis para Windows são:

* Registo do Windows
* Ficheiros do Windows

Para obter informações detalhadas sobre o Change Tracking, consulte [as alterações de resolução de problemas num VM](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Ver inventário

No seu VM **selecione Inventário** em **OPERAÇÕES**. No separador **Software,** há uma tabela que mostra o software que tinha sido encontrado. Os detalhes de alto nível para cada registo de software aparecem na tabela. Estes detalhes incluem o nome do software, versão, editor e última hora renovada.

![Ver inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorize registos de atividades e alterações

A partir da janela **de rastreio change** no seu VM, selecione Gerir a **Ligação de Registo de Atividade** para abrir o painel de registo de atividade **azure.** Selecione **Connect** para ligar Change Tracking ao registo de atividade Azure para o seu VM.

Depois de o Change Tracking estar ativado, vá ao painel **de visão geral** para o seu VM e selecione **Stop** para parar o seu VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Depois de o VM ser transacionado, selecione **Iniciar** a reiniciar o seu VM.

Parar e reiniciar um VM regista um evento no seu registo de atividade. Volte para o painel **de rastreio change** e selecione o **separador Eventos** na parte inferior do painel. Depois de um tempo, os eventos aparecem na tabela e na mesa. Pode selecionar cada evento para ver informações detalhadas para esse evento.

![Ver alterações no registo de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico anterior mostra alterações que ocorreram ao longo do tempo. Depois de adicionar uma ligação Azure Activity Log, o gráfico de linha na parte superior apresenta eventos de Registo de Atividade Azure.

Cada linha de gráficos de barras representa um tipo de mudança de rastreável diferente. Estes tipos são daemons Linux, ficheiros, chaves de registo do Windows, software e serviços windows. O separador **Alterar** mostra os detalhes da alteração. As alterações aparecem na ordem de quando cada ocorreu, com a alteração mais recente mostrada primeiro.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura e reviu a Change Tracking and Update Management para o seu VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e VM.
> * Gerir as atualizações do Windows.
> * Monitorize alterações e inventário.

Vá ao próximo tutorial para saber sobre a monitorização do seu VM.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitor.md)
