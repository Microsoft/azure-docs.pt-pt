---
title: Tutorial-gerenciar a configuração de máquina virtual do Linux no Azure
description: Neste tutorial, você aprenderá a identificar alterações e gerenciar atualizações de pacote em uma máquina virtual Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5dd35ed2f4533d19cc3a59788d6422416dce13f5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034559"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Tutorial: monitorar alterações e atualizar uma máquina virtual do Linux no Azure

O Azure [controle de alterações](../../automation/change-tracking.md) permite que você identifique facilmente as alterações e [Gerenciamento de atualizações](../../automation/automation-update-management.md) permite que você gerencie atualizações do sistema operacional para suas VMs Linux do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerir atualizações do Windows
> * Monitorizar alterações e inventário

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupMonitor* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM* e gera chaves SSH caso estas ainda não existam em *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Gerenciar atualizações de software

A gestão de atualizações permite-lhe gerir atualizações e correções para as suas máquinas virtuais do Linux.
Diretamente a partir da VM, pode avaliar rapidamente o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM.

Para obter informações sobre preços, veja [Preços de Automatização para Gestão de atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Ativar a Gestão de atualizações

Ativar a Gestão de atualizações para a VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações**, selecione **Gestão de atualizações**. É aberto o ecrã **Ativar Gestão de Atualizações**.

A validação é executada para determinar se a Gestão de atualizações está ativada para esta VM.
A validação inclui a verificação da existência de uma área de trabalho do Log Analytics e da conta de Automatização ligada, e se a solução está na área de trabalho.

A área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md) serve para recolher dados gerados pelas funcionalidades e serviços, tais como a Gestão de atualizações.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.
Para executar ações adicionais em VMs que necessitam de atualizações, a Automatização do Azure permite executar runbooks nas VMs, tais como transferir e aplicar atualizações.

O processo de validação também verifica se a VM está provisionada com o agente de Log Analytics e o Hybrid runbook Worker de automação. Este agente serve para comunicar com a VM e obter informações sobre o estado de atualização.

Escolha o espaço de trabalho Log Analytics e a conta de automação e selecione **habilitar** para habilitar a solução. A solução demora até 15 minutos a ativar.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* Área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Conta de automatização](../../automation/automation-offering-get-started.md)
* Uma [Função de trabalho de runbook híbrida](../../automation/automation-hybrid-runbook-worker.md) está ativada na VM

O ecrã **Gestão de Atualizações** é apresentado. Configure o local, Log Analytics espaço de trabalho e conta de automação para usar e selecione **habilitar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

![Ativar a solução de Gestão de atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser. Depois que a solução é habilitada, as informações sobre as atualizações ausentes na VM fluem para Azure Monitor logs. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Após a conclusão da avaliação de atualizações, verá uma lista de atualizações em falta no separador **Atualizações em falta**.

 ![Ver o estado de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

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
Selecione a implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

![Dashboard de estado de Implementação de Atualização para uma implementação específica](./media/tutorial-monitoring/manage-updates-view-results.png)

No mosaico **Resultados da atualização** encontra-se um resumo do número total de atualizações e os resultados de implementação da VM.
Na tabela à direita encontra-se uma divisão detalhada de cada atualização e os resultados da instalação, que podem ter um dos seguintes valores:

* **Não tentado** - a atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Com êxito** - a atualização foi executada com êxito.
* **Falhou** - a atualização falhou.

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione o mosaico **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="monitor-changes-and-inventory"></a>Monitorizar alterações e inventário

Você pode coletar e exibir o inventário de software, arquivos, daemons do Linux, serviços do Windows e chaves do registro do Windows em seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

### <a name="enable-change-and-inventory-management"></a>Ativar a Gestão de alterações e de inventário

Ativar a Gestão de alterações e de inventário na VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações**, selecione **Inventário** ou **Controlo de alterações**. É aberto o ecrã **Ativar o Controlo de Alterações e Inventário**.

Configure o local, Log Analytics espaço de trabalho e conta de automação para usar e selecione **habilitar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização. Apesar de as soluções estarem separadas no menu, tratam-se da mesma solução. Ativar uma ativa a outra na VM.

![Ativar o Controlo de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Após a ativação da solução, o inventário poderá demorar algum tempo a ser recolhido na VM antes de aparecerem dados.

### <a name="track-changes"></a>Controlar as alterações

Na sua VM, selecione **Controlo de Alterações**, em **OPERAÇÕES**. Selecione **Editar Definições**. É apresentada a página **Controlo de Alterações**. Selecione o tipo de definição que pretende controlar e selecione **+Adicionar** para configurar as definições. A opção disponível no Linux é **Linux Files**

Para obter informações detalhadas sobre o Controlo de Alterações, veja [Resolver problemas relacionados com alterações numa VM](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Ver o inventário

Na sua VM, selecione **Inventário**, em **OPERAÇÕES**. No separador **Software**, existe uma tabela que lista o software que foi encontrado. Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome, a versão, o fabricante e a hora da última atualização do software.

![Ver o inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorizar os Registos de Atividade e as alterações

Na página **Controlo de alterações** da sua VM, selecione **Gerir Ligação de Registo de Atividades**. Esta tarefa abre a página **Registo de atividades do Azure**. Selecione **Ligar** para ligar o Controlo de alterações ao registo de atividades do Azure para a sua VM.

Com esta definição ativada, navegue para a página **Descrição Geral** da sua VM e selecione **Parar** para parar a VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Quando esta estiver desalocada, selecione **Iniciar** para reiniciar a sua VM.

As operações de paragem e início de uma VM registam um evento no respetivo registo de atividades. Regresse à página **Controlo de alterações**. Selecione o separador **Eventos** na parte inferior da página. Passado algum tempo, os eventos são apresentados no gráfico e na tabela. É possível selecionar cada um dos eventos para ver informações detalhadas sobre os mesmos.

![Ver alterações no registo de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico mostra as alterações ocorridas ao longo do tempo. Após ter adicionado uma ligação do Registo de Atividades, o gráfico de linhas existente na parte superior apresenta os eventos do Registo de Atividades do Azure. Cada linha do gráfico de barras representa um tipo diferente de alteração passível de controlo. Estes tipos são daemons, ficheiros e software do Linux. O separador Alterações mostra os detalhes das alterações apresentadas na visualização por ordem descendente de data/hora em que a alteração ocorreu (mais recente primeiro).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou e examinou Controle de Alterações e Gerenciamento de Atualizações para sua VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Gerenciar atualizações do Linux
> * Monitorizar alterações e inventário

Avance para o próximo tutorial para saber mais sobre como monitorar sua VM.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitor.md)
