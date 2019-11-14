---
title: Tutorial – gerenciar a configuração de máquina virtual do Windows no Azure
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
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064806"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: monitorar alterações e atualizar uma máquina virtual do Windows no Azure

Com o Azure [controle de alterações](../../automation/change-tracking.md) e [Gerenciamento de atualizações](../../automation/automation-update-management.md), você pode facilmente identificar alterações em suas máquinas virtuais do Windows no Azure e gerenciar atualizações do sistema operacional para essas VMs.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerenciar atualizações do Windows.
> * Monitore as alterações e o inventário.

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as etapas neste artigo. Ele tem ferramentas comuns do Azure pré-instalados e configuradas para usar com sua conta do Azure.

Para abrir qualquer bloco de código no Cloud Shell, basta selecionar **experimentar** no canto superior direito desse bloco de código.

Você também pode abrir Cloud Shell em uma guia separada do navegador acessando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **copiar** para copiar blocos de código, Cole-os na guia Cloud Shell e selecione a tecla Enter para executar o código.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para configurar a monitorização e a gestão de atualizações do Azure neste tutorial, precisa de uma VM do Windows no Azure.

Primeiro, defina um nome de utilizador e palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Em seguida, crie a VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada `myVM` no `East US` local. Se eles ainda não existirem, o grupo de recursos `myResourceGroupMonitor` e recursos de rede de suporte serão criados:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos até que os recursos e a VM sejam criados.

## <a name="manage-windows-updates"></a>Gerir atualizações do Windows

Gerenciamento de Atualizações ajuda a gerenciar atualizações e patches para suas VMs Windows do Azure. Diretamente da sua VM, você pode rapidamente:

- Avaliar o estado das atualizações disponíveis.
- Agendar a instalação das atualizações necessárias.
- Examine os resultados da implantação para verificar se as atualizações foram aplicadas com êxito à VM.

Para obter informações sobre preços, consulte [preços de automação para o gerenciamento de atualizações](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

Para habilitar Gerenciamento de Atualizações para sua VM:

1. No lado mais à esquerda da janela, selecione **máquinas virtuais**.
1. Escolha uma VM na lista.
1. No painel **operações** da janela VM, selecione gerenciamento de **atualizações**.
1. A janela **habilitar gerenciamento de atualizações** é aberta.

A validação é feita para determinar se Gerenciamento de Atualizações está habilitada para esta VM. A validação inclui verificações de um espaço de trabalho Log Analytics, para uma conta de automação vinculada e se a solução está no espaço de trabalho.

Você usa um espaço de trabalho [log Analytics](../../log-analytics/log-analytics-overview.md) para coletar dados gerados por recursos e serviços, como gerenciamento de atualizações. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

Para executar ações adicionais em VMs que exigem atualizações, você pode usar a automação do Azure para executar runbooks em VMs. Essas ações incluem baixar ou aplicar atualizações.

O processo de validação também verifica se a VM é provisionada com o Microsoft Monitoring Agent (MMA) e Hybrid Runbook Worker de automação. Você usa o agente para se comunicar com a VM e obter informações sobre o status da atualização.

Na janela **habilitar gerenciamento de atualizações** , escolha o espaço de trabalho log Analytics e a conta de automação e, em seguida, selecione **habilitar**. A solução leva até 15 minutos para ser habilitada.

Qualquer um dos seguintes pré-requisitos ausentes durante a integração é adicionado automaticamente:

* Área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatização](../../automation/automation-offering-get-started.md)
* Um [Hybrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md), que está habilitado na VM

Depois que a solução for habilitada, a janela **Gerenciamento de atualizações** será aberta. Configure o local, Log Analytics o espaço de trabalho e a conta de automação a serem usados e, em seguida, selecione **habilitar**. Se essas opções aparecerem esmaecidas, outra solução de automação será habilitada para a VM e o espaço de trabalho da solução e a conta de automação deverão ser usados.

![Habilitar Gerenciamento de Atualizações solução](./media/tutorial-monitoring/manageupdates-update-enable.png)

A solução Gerenciamento de Atualizações pode levar até 15 minutos para ser habilitada. Durante este período, não feche a janela do browser. Depois que a solução é habilitada, as informações sobre as atualizações ausentes na VM fluem para Azure Monitor logs. Pode levar de 30 minutos a 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-an-update-assessment"></a>Ver avaliações de atualizações

Depois que Gerenciamento de Atualizações estiver habilitado, a janela **Gerenciamento de atualizações** será exibida. Após a conclusão da avaliação das atualizações, você verá uma lista de atualizações ausentes na guia **atualizações ausentes** .

 ![Ver o estado de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Você escolhe quais tipos de atualização deseja incluir na implantação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova implantação de atualização para a VM, selecione **agendar implantação de atualização** na parte superior da janela **Gerenciamento de atualizações** . Na janela **nova implantação de atualização** , especifique as seguintes informações:

| Opção | Descrição |
| --- | --- |
| **Nome** |Insira um nome exclusivo para identificar a implantação da atualização. |
|**Sistema operativo**| Selecione **Linux** ou **Windows**.|
| **Grupos a serem atualizados** |Para VMs hospedadas no Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e marcas. Essa consulta cria um grupo dinâmico de VMs hospedadas no Azure para incluir em sua implantação. </br></br>Para VMs não hospedadas no Azure, selecione uma pesquisa salva existente. Com essa pesquisa, você pode selecionar um grupo dessas VMs para incluir na implantação. </br></br> Para saber mais, confira [grupos dinâmicos](../../automation/automation-update-management-groups.md).|
| **Computadores para atualizar** |Selecione **pesquisa salva**, **grupo importado**ou **computadores**.<br/><br/>Se você selecionar **computadores**, poderá escolher computadores individuais na lista suspensa. A prontidão de cada máquina é mostrada na coluna **prontidão do agente de atualização** da tabela.</br></br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores em logs de Azure Monitor, consulte [grupos de computadores em logs de Azure monitor](../../azure-monitor/platform/computer-groups.md) |
|**Classificações de atualização**|Escolha todas as classificações de atualização necessárias.|
|**Incluir/excluir atualizações**|Selecione esta opção para abrir o painel **incluir/excluir** . As atualizações a serem incluídas e aquelas a serem excluídas estão em guias separadas. Para obter mais informações sobre como a inclusão é tratada, consulte [agendar uma implantação de atualização](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment). |
|**Configurações de agendamento**|Escolha o horário de início e selecione **uma vez** ou **recorrente**.|
| **Pré-scripts + pós-scripts**|Escolha os scripts a serem executados antes e depois da implantação.|
| **Janela de manutenção** | Insira o número de minutos definido para atualizações. Os valores válidos variam de 30 a 360 minutos. |
| **Controle de reinicialização**| Selecione como as reinicializações são tratadas. As seleções disponíveis são:<ul><li>**Reinicializar se necessário**</li><li>**Sempre reinicializar**</li><li>**Nunca reinicializar**</li><li>**Somente reinicialização**</li></ul>**Reinicializar se necessário** é a seleção padrão. Se você selecionar **apenas reinicializar**, as atualizações não serão instaladas.|

Depois de concluir a configuração da agenda, clique em **criar** para retornar ao painel de status. A tabela **agendada** mostra a agenda de implantação que você criou.

Você também pode criar implantações de atualização programaticamente. Para saber como criar uma implantação de atualização com a API REST, consulte [Software Update Configurations-Create](/rest/api/automation/softwareupdateconfigurations/create). Também há um runbook de exemplo que você pode usar para criar uma implantação semanal de atualização. Para saber mais sobre esse runbook, confira [criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Depois que a implantação agendada for iniciada, você poderá ver o status da implantação na guia **implantações de atualizações** da janela **Gerenciamento de atualizações** .

Se a implantação estiver em execução no momento, seu status será exibido como "em andamento". Após a conclusão bem-sucedida, o status será alterado para "êxito". Mas se alguma atualização na implantação falhar, o status será "parcialmente com falha".

Selecione a implantação de atualização concluída para ver o painel dessa implantação.

![Dashboard de estado de Implementação de Atualização para uma implementação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

O bloco **resultados da atualização** mostra um resumo do número total de atualizações e os resultados da implantação na VM. A tabela à direita mostra uma análise detalhada de cada atualização e os resultados da instalação. Cada resultado tem um dos seguintes valores:

* **Sem tentativa**: a atualização não está instalada. Não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Com êxito**: a atualização foi executada com êxito.
* **Falhou**: a atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione o bloco **saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Selecione **erros** para ver informações detalhadas sobre quaisquer erros de implantação.

## <a name="monitor-changes-and-inventory"></a>Monitorizar alterações e inventário

Você pode coletar e exibir um inventário do software, dos arquivos, dos daemons do Linux, dos serviços do Windows e das chaves do registro do Windows em seus computadores. Controlar as configurações de seus computadores ajuda a identificar problemas operacionais em seu ambiente e compreender melhor o estado de seus computadores.

### <a name="enable-change-and-inventory-management"></a>Habilitar gerenciamento de alterações e inventário

Para habilitar o gerenciamento de alterações e inventário para sua VM:

1. No lado mais à esquerda da janela, selecione **máquinas virtuais**.
1. Escolha uma VM na lista.
1. Em **operações** na janela VM, selecione **inventário** ou controle de **alterações**.
1. O painel **habilitar controle de alterações e inventário** é aberto.

Configure o local, o espaço de trabalho Log Analytics e a conta de automação a serem usados e, em seguida, selecione **habilitar**. Se as opções aparecerem esmaecidas, uma solução de automação já estará habilitada para a VM. Nesse caso, o espaço de trabalho já habilitado e a conta de automação devem ser usados.

Embora as soluções apareçam separadamente no menu, elas são a mesma solução. Ativar uma ativa a outra na VM.

![Ativar o Controlo de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois que a solução tiver sido habilitada, pode levar algum tempo para que o inventário seja coletado na VM antes que os dados apareçam.

### <a name="track-changes"></a>Controlar as alterações

Em sua VM em **operações**, selecione **controle de alterações** e, em seguida, selecione **Editar configurações**. O painel de **controle de alterações** é aberto. Selecione o tipo de definição que pretende controlar e selecione **+Adicionar** para configurar as definições.

As opções de configurações disponíveis para o Windows são:

* Registo do Windows
* Ficheiros do Windows

Para obter informações detalhadas sobre Controle de Alterações, consulte [solucionar problemas de alterações em uma VM](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Ver o inventário

Na sua VM, selecione **Inventário**, em **OPERAÇÕES**. Na guia **software** , há uma tabela que mostra o software que foi encontrado. Os detalhes de alto nível para cada registro de software aparecem na tabela. Esses detalhes incluem o nome do software, a versão, o editor e o horário da última atualização.

![Ver o inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorar logs de atividade e alterações

Na janela **controle de alterações** em sua VM, selecione **gerenciar conexão do log de atividades** para abrir o painel log de **atividades do Azure** . Selecione **conectar** para conectar controle de alterações ao log de atividades do Azure para sua VM.

Depois que o Controle de Alterações estiver habilitado, vá para o painel **visão geral** da VM e selecione **parar** para interromper a VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Depois que a VM for desalocada, selecione **Iniciar** para reiniciar a VM.

Parar e reiniciar uma VM registra um evento em seu log de atividades. Volte para o painel **controle de alterações** e selecione a guia **eventos** na parte inferior do painel. Após alguns instantes, os eventos aparecem no gráfico e na tabela. Você pode selecionar cada evento para exibir informações detalhadas para esse evento.

![Ver alterações no registo de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico anterior mostra as alterações que ocorreram ao longo do tempo. Depois de adicionar uma conexão do log de atividades do Azure, o grafo de linha na parte superior exibe os eventos do log de atividades do Azure.

Cada linha de gráficos de barras representa um tipo de alteração rastreável diferente. Esses tipos são daemons do Linux, arquivos, chaves do registro do Windows, software e serviços do Windows. A guia **alterar** mostra os detalhes da alteração. As alterações aparecem na ordem de cada ocorrência, com a alteração mais recente mostrada primeiro.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou e examinou Controle de Alterações e Gerenciamento de Atualizações para sua VM. Aprendeu a:

> [!div class="checklist"]
> * Crie um grupo de recursos e uma VM.
> * Gerenciar atualizações do Windows.
> * Monitore as alterações e o inventário.

Vá para o próximo tutorial para saber mais sobre como monitorar sua VM.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitor.md)
