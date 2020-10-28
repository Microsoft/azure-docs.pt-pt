---
title: Tutorial - Gerir a configuração da máquina virtual Linux em Azure
description: Neste tutorial, aprende-se a identificar alterações e a gerir atualizações de pacotes numa máquina virtual Linux
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
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 175c92c02196105e9fb1249e5b88d73bc8b87d48
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735229"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Tutorial: Monitorize alterações e atualize uma máquina virtual Linux em Azure

O Azure [Change Tracking](../../automation/change-tracking/overview.md) permite-lhe identificar facilmente alterações e [a Gestão de Atualização](../../automation/update-management/overview.md) permite-lhe gerir as atualizações do sistema operativo para os seus VMs Azure Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Gerir atualizações do Linux
> * Monitorizar alterações e inventário

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupMonitor* na localização *eastus* .

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

## <a name="manage-software-updates"></a>Gerir atualizações de software

A gestão de atualizações permite-lhe gerir atualizações e correções para as suas máquinas virtuais do Linux.
Diretamente a partir da VM, pode avaliar rapidamente o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM.

Para obter informações sobre preços, consulte [os preços da Automatização para a gestão de Atualização](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Ativar a Gestão de atualizações

Ativar a Gestão de atualizações para a VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais** .
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações** , selecione **Gestão de atualizações** . É aberto o ecrã **Ativar Gestão de Atualizações** .

A validação é executada para determinar se a Gestão de atualizações está ativada para esta VM.
A validação inclui a verificação da existência de uma área de trabalho do Log Analytics e da conta de Automatização ligada, e se a solução está na área de trabalho.

A área de trabalho do [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) serve para recolher dados gerados pelas funcionalidades e serviços, tais como a Gestão de atualizações.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.
Para executar ações adicionais em VMs que necessitam de atualizações, a Automatização do Azure permite executar runbooks nas VMs, tais como transferir e aplicar atualizações.

O processo de validação também verifica se o VM é a provisionado com o agente Log Analytics e o trabalhador híbrido automation. Este agente serve para comunicar com a VM e obter informações sobre o estado de atualização.

Escolha o espaço de trabalho do Log Analytics e a conta de automatização e selecione **Ative** para ativar a solução. A solução demora até 15 minutos a ativar.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) espaço de trabalho
* [Conta de automação](../../automation/index.yml)
* Uma [Função de trabalho de runbook híbrida](../../automation/automation-hybrid-runbook-worker.md) está ativada na VM

O ecrã **Gestão de Atualizações** é apresentado. Configure a localização, log Analytics espaço de trabalho e conta de automação para utilizar e selecionar **Enable** . Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

![Ativar a solução de Gestão de atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser. Após a solução estar ativada, informações sobre atualizações em falta nos fluxos VM para registos do Monitor Azure. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Após a conclusão da avaliação de atualizações, verá uma lista de atualizações em falta no separador **Atualizações em falta** .

 ![Ver o estado de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova Implementação de Atualização para a VM, clique em **Agendar a implementação da atualização** na parte superior do ecrã **Gestão de atualizações** . No ecrã **Nova implementação de atualização** , especifique as seguintes informações:

Para criar uma nova implementação de atualização, selecione **a implementação da atualização do calendário** . A nova página **de implementação da nova atualização** abre. Introduza os valores para as propriedades descritas na tabela seguinte e, em seguida, clique em **Criar** :

| Propriedade | Descrição |
| --- | --- |
| Nome |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema Operativo| Linux ou Windows|
| Grupos a atualizar |Para máquinas Azure, defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e tags para construir um grupo dinâmico de VMs Azure para incluir na sua implementação. </br></br>Para máquinas não-Azure, selecione uma pesquisa guardada existente para selecionar um grupo de máquinas não-Azure para incluir na implementação. </br></br>Para saber mais, consulte [Grupos Dinâmicos](../../automation/update-management/configure-groups.md)|
| Máquinas a atualizar |Selecione uma pesquisa guardada, grupo importado ou escolha a Máquina a partir do drop-down e selecione máquinas individuais. Se escolher **Máquinas** , a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE** .</br> Para conhecer os diferentes métodos de criação de grupos informáticos em registos do Monitor Azure, consulte [grupos de computador em registos do Monitor Azure](../../azure-monitor/platform/computer-groups.md) |
|Classificações de atualizações|Selecione todas as classificações de atualização que precisa|
|Incluir/excluir atualizações|Isto abre a página **Incluir/Excluir.** As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter mais informações sobre como a inclusão é tratada, consulte [Agendar uma Implementação de Atualização](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) |
|Definições de agenda|Selecione a hora de início e selecione uma vez ou recorrente para a recorrência|
| Pré-scripts + Post-scripts|Selecione os scripts para executar antes e depois da sua implantação|
| Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos e não mais de 6 horas |
| Reiniciar o controlo| Determina como as reinicializações devem ser tratadas. As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

As Implementações de Atualização também podem ser criadas programáticamente. Para aprender a criar uma Implementação de Atualização com a API REST, consulte [configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create). Há também um livro de amostras que pode ser usado para criar uma implementação semanal de atualização. Para saber mais sobre este livro de bordo, consulte [Criar uma atualização semanal para um ou mais VMs num grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Depois de concluir a configuração do agendamento, clique no botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que a tabela **Agendada** mostra o agendamento da implementação que criou.

### <a name="view-results-of-an-update-deployment"></a>Ver resultados de uma implementação de atualização

Após o início da implementação agendada, pode ver o estado dessa implementação no separador **Implementações de atualização** no ecrã **Gestão de atualizações** .
Se estiver em execução, o respetivo estado é apresentado como **Em curso** . Depois de concluir, se for bem sucedida, muda para **Com êxito** .
Se existir uma falha numa ou mais atualizações na implementação, o estado é **Falha parcial** .
Selecione a implementação da atualização concluída para ver o dashboard relativo a essa implementação de atualização.

![Dashboard de estado de Implementação de Atualização para uma implementação específica](./media/tutorial-monitoring/manage-updates-view-results.png)

No mosaico **Resultados da atualização** encontra-se um resumo do número total de atualizações e os resultados de implementação da VM.
Na tabela à direita encontra-se uma divisão detalhada de cada atualização e os resultados da instalação, que podem ter um dos seguintes valores:

* **Não foi tentada** - a atualização não foi instalada porque não havia tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Conseguiu** - a atualização conseguiu
* **Falhou** - a atualização falhou

Selecione **Todos os registos** para ver todas as entradas de registo criadas pela implementação.

Selecione o mosaico **Saída** para ver o fluxo de tarefas do runbook responsável pela gestão da implementação da atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre os erros da implementação.

## <a name="monitor-changes-and-inventory"></a>Monitorizar alterações e inventário

Pode recolher e visualizar o inventário para software, ficheiros, daemons Linux, Serviços Windows e chaves de registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

### <a name="enable-change-and-inventory-management"></a>Ativar a Gestão de alterações e de inventário

Ativar a Gestão de alterações e de inventário na VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais** .
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações** , selecione **Inventário** ou **Controlo de alterações** . É aberto o ecrã **Ativar o Controlo de Alterações e Inventário** .

Configure a localização, log Analytics espaço de trabalho e conta de automação para utilizar e selecionar **Enable** . Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização. Apesar de as soluções estarem separadas no menu, tratam-se da mesma solução. Ativar uma ativa a outra na VM.

![Ativar o Controlo de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Após a ativação da solução, o inventário poderá demorar algum tempo a ser recolhido na VM antes de aparecerem dados.

### <a name="track-changes"></a>Controlar as alterações

Na sua VM, selecione **Controlo de Alterações** , em **OPERAÇÕES** . Selecione **Editar Definições** . É apresentada a página **Controlo de Alterações** . Selecione o tipo de definição que pretende controlar e selecione **+Adicionar** para configurar as definições. A opção disponível no Linux é **Linux Files**

Para obter informações detalhadas sobre o Controlo de Alterações, veja [Resolver problemas relacionados com alterações numa VM](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Ver inventário

Na sua VM, selecione **Inventário** , em **OPERAÇÕES** . No separador **Software** , existe uma tabela que lista o software que foi encontrado. Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome, a versão, o fabricante e a hora da última atualização do software.

![Ver inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorizar os Registos de Atividade e as alterações

Na página **Controlo de alterações** da sua VM, selecione **Gerir Ligação de Registo de Atividades** . Esta tarefa abre a página **Registo de atividades do Azure** . Selecione **Ligar** para ligar o Controlo de alterações ao registo de atividades do Azure para a sua VM.

Com esta definição ativada, navegue para a página **Descrição Geral** da sua VM e selecione **Parar** para parar a VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Quando esta estiver desalocada, selecione **Iniciar** para reiniciar a sua VM.

As operações de paragem e início de uma VM registam um evento no respetivo registo de atividades. Regresse à página **Controlo de alterações** . Selecione o separador **Eventos** na parte inferior da página. Passado algum tempo, os eventos são apresentados no gráfico e na tabela. É possível selecionar cada um dos eventos para ver informações detalhadas sobre os mesmos.

![Ver alterações no registo de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico mostra as alterações ocorridas ao longo do tempo. Após ter adicionado uma ligação do Registo de Atividades, o gráfico de linhas existente na parte superior apresenta os eventos do Registo de Atividades do Azure. Cada linha do gráfico de barras representa um tipo diferente de alteração passível de controlo. Estes tipos são daemons, ficheiros e software do Linux. O separador Alterações mostra os detalhes das alterações apresentadas na visualização por ordem descendente de data/hora em que a alteração ocorreu (mais recente primeiro).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura e reviu a Change Tracking and Update Management para o seu VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Gerir atualizações do Linux
> * Monitorizar alterações e inventário

Avance para o próximo tutorial para aprender sobre a monitorização do seu VM.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitor.md)
