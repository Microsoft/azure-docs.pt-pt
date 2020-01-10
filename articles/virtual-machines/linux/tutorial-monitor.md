---
title: Tutorial-monitorar máquinas virtuais do Linux no Azure
description: Neste tutorial, você aprenderá a monitorar o desempenho e os componentes de aplicativos descobertos em execução nas máquinas virtuais Linux.
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
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: b06342d5034b820be4e6fd49436546a5aa7b7e02
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749790"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Tutorial: monitorar uma máquina virtual do Linux no Azure

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

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupMonitor* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). O exemplo seguinte cria uma VM com o nome *myVM* e gera chaves SSH caso estas ainda não existam em *~/.ssh/* :

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

Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. As contas de armazenamento têm de ter um nome globalmente exclusivo, ter entre 3 e 24 carateres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Neste exemplo, é utilizada uma cadeia aleatória para criar um nome de conta de armazenamento exclusivo.

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

Quando os diagnósticos de arranque estão ativados, sempre que parar e iniciar a VM, as informações sobre o processo de arranque são escritas num ficheiro de registo. Neste exemplo, desaloque primeiro a VM com o comando [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) da seguinte forma:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) da seguinte forma:

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

## <a name="enable-advanced-monitoring"></a>Habilitar monitoramento avançado

Para habilitar o monitoramento de sua VM do Azure com o Azure Monitor para VMs:

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)** .

3. Sobre o **Insights (pré-visualização)** página, selecione **Experimente agora o**.

    ![Ativar o Azure Monitor para as VMs para uma VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

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

3. Indique um **Nome** para o alerta, como *myAlertRule*

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
