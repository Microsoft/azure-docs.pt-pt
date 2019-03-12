---
title: Tutorial – Monitorizar e atualizar máquinas virtuais do Linux no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a monitorizar diagnósticos de arranque e métricas de desempenho e a gerir atualizações de pacotes numa máquina virtual do Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fba54fa1d2ca6675b41728b460a07515b05758f8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531594"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Tutorial: Monitorizar e atualizar uma máquina virtual do Linux no Azure

Para garantir que as máquinas virtuais (VMs) no Azure estão a ser executadas corretamente, pode consultar diagnósticos de arranque e métricas de desempenho, bem como gerir as atualizações de pacotes. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar diagnósticos de arranque na VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião
> * Ativar a extensão de diagnóstico na VM
> * Ver métricas da VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerir atualizações de pacotes
> * Monitorizar alterações e inventário
> * Configurar a monitorização avançada

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupMonitor* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM* e gera chaves SSH caso estas ainda não existam em *~/.ssh/*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Ativar diagnósticos de arranque

À medida que as VMs do Linux arrancam, a extensão de diagnóstico de arranque captura a saída de arranque e guarda-a no armazenamento do Azure. Estes dados podem ser utilizados para resolver problemas de arranque das VMs. Os diagnósticos de arranque não são ativados automaticamente ao criar uma VM do Linux através da CLI do Azure.

Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. As contas de armazenamento têm de ter um nome globalmente exclusivo, ter entre 3 e 24 carateres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). Neste exemplo, é utilizada uma cadeia aleatória para criar um nome de conta de armazenamento exclusivo.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Ao ativar os diagnósticos de arranque, é necessário o URI para o contentor de armazenamento de blobs. O comando seguinte consulta a conta de armazenamento para devolver este URI. O valor do URI é armazenado no nome de variável *bloburi*, utilizado no próximo passo.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Ative agora os diagnósticos de arranque com [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). O valor `--storage` é o URI do blob recolhido no passo anterior.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Ver diagnósticos de arranque

Quando os diagnósticos de arranque estão ativados, sempre que parar e iniciar a VM, as informações sobre o processo de arranque são escritas num ficheiro de registo. Neste exemplo, desaloque primeiro a VM com o comando [az vm deallocate](/cli/azure/vm#az-vm-deallocate) da seguinte forma:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start]( /cli/azure/vm#az-vm-stop) da seguinte forma:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Pode obter os dados de diagnósticos de arranque para *myVM* com o comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) da seguinte forma:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Ver métricas de anfitrião

Uma VM do Linux tem um anfitrião dedicado no Azure com o qual interage. As métricas são recolhidas automaticamente para o anfitrião e podem ser visualizadas no portal do Azure da seguinte forma:

1. No portal do Azure, selecione **Grupos de Recursos**, escolha **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver o desempenho da VM de anfitrião, selecione **Métricas** na janela da VM e, em seguida, escolha qualquer uma das métricas *[Anfitrião]* em **Métricas disponíveis**.

    ![Ver métricas de anfitrião](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar a extensão de diagnóstico

As métricas de anfitrião básicas estão disponíveis, mas para ver métricas mais granulares e específicas da VM, tem de instalar a extensão de diagnóstico do Azure na VM. A extensão de diagnóstico do Azure permite que sejam obtidos dados de monitorização e diagnóstico adicionais da VM. Pode ver estas métricas de desempenho e criar alertas com base no desempenho da VM. A extensão de diagnóstico é instalada através do portal do Azure da seguinte forma:

1. No portal do Azure, escolha **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Selecione **Definições de diagnóstico**. No menu pendente *Escolher uma conta de armazenamento*, se ainda não estiver selecionada, escolha a conta *mydiagdata [1234]* criada na secção anterior.
1. Selecione o botão **Ativar a monitorização ao nível do convidado**.

    ![Ver métricas de diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Ver métricas da VM

Pode ver as métricas da VM da mesma forma que vê as métricas da VM de anfitrião:

1. No portal do Azure, escolha **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver o desempenho da VM, selecione **Métricas** na janela da VM e, em seguida, selecione qualquer uma das métricas de diagnóstico *[Convidado]* em **Métricas disponíveis**.

    ![Ver métricas da VM](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Criar alertas

Pode criar alertas com base em métricas de desempenho específicas. Podem ser utilizados alertas para notificá-lo quando a utilização média da CPU excede um determinado limiar ou o espaço livre em disco disponível diminuir até uma determinada quantidade, por exemplo. Os alertas são apresentados no portal do Azure ou podem ser enviados por e-mail. Também pode acionar runbooks da Automatização do Azure ou do Azure Logic Apps em resposta aos alertas gerados.

O exemplo seguinte cria um alerta para a utilização média da CPU.

1. No portal do Azure, selecione **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Selecione **Alertas (clássicos)** e, em seguida, escolha **Adicionar alerta de métrica (clássico)** na parte superior da janela de alertas.
3. Indique um **Nome** para o alerta, como *myAlertRule*
4. Para acionar um alerta quando a percentagem da CPU excede 1.0 durante cinco minutos, mantenha todas as outras predefinições selecionadas.
5. Opcionalmente, selecione a caixa para *Proprietários, contribuidores e leitores do e-mail* para enviar uma notificação por e-mail. A ação predefinida é apresentar uma notificação no portal.
6. Selecione o botão **OK**.

## <a name="manage-software-updates"></a>Gerir atualizações de software

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

O processo de validação também verifica se a VM está aprovisionada com o agente do Log Analytics e o trabalho de runbook híbrida de automatização. Este agente serve para comunicar com a VM e obter informações sobre o estado de atualização.

Escolha a conta de área de trabalho e a automatização do Log Analytics e selecione **ativar** para ativar a solução. A solução demora até 15 minutos a ativar.

Se for detetada a falta de qualquer um dos seguintes pré-requisitos durante a inclusão, estes serão adicionados automaticamente:

* Área de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Conta de automatização](../../automation/automation-offering-get-started.md)
* Uma [Função de trabalho de runbook híbrida](../../automation/automation-hybrid-runbook-worker.md) está ativada na VM

O ecrã **Gestão de Atualizações** é apresentado. Configurar a localização, a área de trabalho do Log Analytics e a conta de automatização para utilizar e selecione **ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

![Ativar a solução de Gestão de atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser. Após a solução está ativada, informações sobre atualizações em falta na VM são transmitidas para registos do Azure Monitor. Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Ver avaliação de atualizações

Depois de **Gestão de atualizações** ser ativada, o ecrã **Gestão de atualizações** aparece. Após a conclusão da avaliação de atualizações, verá uma lista de atualizações em falta no separador **Atualizações em falta**.

 ![Ver o estado de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implementação de atualizações

Para instalar atualizações, agende uma implementação que siga o seu agendamento e o período de administração da versão. Pode escolher quais os tipos de atualização a incluir na implementação. Por exemplo, pode incluir atualizações de segurança ou críticas e excluir update rollups.

Para agendar uma nova Implementação de Atualização para a VM, selecione **Agendar implementação da atualização** na parte superior do ecrã **Gestão de atualizações**. No ecrã **Nova implementação de atualização**, especifique as seguintes informações:

* **Nome** - Indique um nome exclusivo para identificar a implementação de atualizações.
* **Classificação da atualização** - Selecione os tipos de software que a implementação da atualização incluiu na implementação. Os tipos de classificação são:
  * Atualizações críticas e de segurança
  * Outras atualizações
* **Atualizações a Excluir** - pode indicar uma lista de nomes de pacotes que devem ser ignorados durante a implementação da atualização. Nomes dos pacotes suportam carateres universais (como, por exemplo \*kernel\*).

  ![Ecrã de Definições de Agendamento de Atualizações](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Definições da agenda** - Pode aceitar a data e hora predefinidas, que é 30 minutos após a hora atual, ou especificar uma hora diferente.
  Também pode especificar se a implementação ocorre uma vez ou configurar um agendamento periódico. Selecione a opção Periódico, em Periodicidade, para configurar um agendamento periódico.

  ![Ecrã de Definições de Agendamento de Atualizações](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Janela de manutenção (minutos)** - Especifique o período de tempo no qual pretende que a implementação da atualização ocorra. Isto ajuda a garantir que as alterações são realizadas nos seus períodos de administração definidos.

Depois de concluir a configuração do agendamento, selecione o botão **Criar** e regressará ao dashboard de estado.
Tenha em atenção que a tabela **Agendada** mostra o agendamento da implementação que criou.

> [!WARNING]
> Quanto a atualizações que requerem reinício, a VM é reiniciada automaticamente.

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

Pode recolher e ver o inventário de software, ficheiros, dos daemons do Linux, serviços do Windows e chaves de registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

### <a name="enable-change-and-inventory-management"></a>Ativar a Gestão de alterações e de inventário

Ativar a Gestão de alterações e de inventário na VM:

1. No lado esquerdo do ecrã, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. No ecrã da VM, na secção **Operações**, selecione **Inventário** ou **Controlo de alterações**. É aberto o ecrã **Ativar o Controlo de Alterações e Inventário**.

Configurar a localização, a área de trabalho do Log Analytics e a conta de automatização para utilizar e selecione **ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização. Apesar de as soluções estarem separadas no menu, tratam-se da mesma solução. Ativar uma ativa a outra na VM.

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

## <a name="advanced-monitoring"></a>Monitorização avançada

Pode fazer mais avançadas de monitorização da sua VM, com uma solução como [do Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md), que monitoriza máquinas de virtuais do Azure (VM) em escala ao analisar o desempenho e estado de funcionamento do Windows e VMs do Linux, incluindo os diferentes processos e interconectadas dependências em outros recursos e processos externos. Gestão de configuração das suas VMs do Azure é entregue com o [automatização do Azure](../../automation/automation-intro.md) solução controlo de alterações e inventário para identificar facilmente as alterações no seu ambiente. Gerir a compatibilidade de atualização é fornecido com a solução de gestão de atualizações de automatização do Azure.   

Do espaço de trabalho do Log Analytics a VM está ligada, também pode obter, consolidar e analisar os dados recolhidos com o [linguagem de consulta avançada](../../azure-monitor/log-query/log-query-overview.md). 

![Área de trabalho do Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou, reviu e geriu atualizações para uma VM. Aprendeu a:

> [!div class="checklist"]
> * Ativar diagnósticos de arranque na VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião
> * Ativar a extensão de diagnóstico na VM
> * Ver métricas da VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerir atualizações de pacotes
> * Monitorizar alterações e inventário
> * Configurar a monitorização avançada

Avance para o próximo tutorial para saber mais sobre o Centro de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir a segurança de VMs](./tutorial-azure-security.md)
