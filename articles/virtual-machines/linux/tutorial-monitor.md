---
title: Tutorial - Monitor Máquinas virtuais Linux em Azure
description: Neste tutorial, aprende-se a monitorizar o desempenho e a descobrir componentes de aplicações em execução nas suas máquinas virtuais Linux.
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
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f100df39ad92a3e8062c01a48a9f68730a3badb8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736722"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Tutorial: Monitorize uma máquina virtual Linux em Azure

A monitorização do Azure utiliza agentes para recolher dados de boot e desempenho a partir de VMs Azure, armazenar estes dados no armazenamento Azure, e torná-lo acessível através do portal, o módulo Azure PowerShell e Azure CLI. A monitorização avançada é entregue com o Azure Monitor para VMs, recolhendo métricas de desempenho, descobrendo componentes de aplicação instalados no VM, e inclui gráficos de desempenho e mapa de dependência.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar diagnósticos de arranque numa VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião da VM
> * Ativar monitor Azure para VMs
> * Ver métricas de desempenho em VM
> * Criar um alerta

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupMonitor* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo seguinte cria uma VM com o nome *myVM* e gera chaves SSH caso estas ainda não existam em *~/.ssh/*:

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

Antes de ativar os diagnósticos de arranque, tem de ser criada uma conta de armazenamento para guardar os registos de arranque. As contas de armazenamento têm de ter um nome globalmente exclusivo, ter entre 3 e 24 carateres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). Neste exemplo, é utilizada uma cadeia aleatória para criar um nome de conta de armazenamento exclusivo.

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

Ative agora os diagnósticos de arranque com [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). O valor `--storage` é o URI do blob recolhido no passo anterior.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Ver diagnósticos de arranque

Quando os diagnósticos de arranque estão ativados, sempre que parar e iniciar a VM, as informações sobre o processo de arranque são escritas num ficheiro de registo. Neste exemplo, desaloque primeiro a VM com o comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate) da seguinte forma:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start](/cli/azure/vm#az_vm_start) da seguinte forma:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Pode obter os dados de diagnósticos de arranque para *myVM* com o comando [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) da seguinte forma:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Ver métricas de anfitrião

Uma VM do Linux tem um anfitrião dedicado no Azure com o qual interage. As métricas são recolhidas automaticamente para o anfitrião e podem ser visualizadas no portal do Azure da seguinte forma:

1. No portal do Azure, selecione **Grupos de Recursos**, escolha **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver o desempenho da VM de anfitrião, selecione **Métricas** na janela da VM e, em seguida, escolha qualquer uma das métricas *[Anfitrião]* em **Métricas disponíveis**.

    ![Ver métricas de anfitrião](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Permitir monitorização avançada

Para permitir a monitorização do seu Azure VM com monitor Azure para VMs:

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página VM, na secção **De Monitorização,** selecione **Insights (pré-visualização)**.

3. Na página **Insights (pré-visualização),** selecione **Tente agora**.

    ![Ativar o Monitor Azure para VMs para um VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Na página **Azure Monitor Insights Onboarding,** se tiver um espaço de trabalho log analytics existente na mesma subscrição, selecione-o na lista de drop-down.  

    A lista pré-seleciona o espaço de trabalho predefinido e o local onde o VM é implantado na subscrição. 

    >[!NOTE]
    >Para criar um novo espaço de trabalho log Analytics para armazenar os dados de monitorização a partir do VM, consulte Criar um espaço de [trabalho Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). O espaço de trabalho deve pertencer a uma das [regiões apoiadas.](../../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)

Depois de ativar a monitorização, poderá ter de esperar alguns minutos antes de poder ver as métricas de desempenho do VM.

![Ativar o Monitor Azure para o processamento de implementação de monitorização de VMs](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Ver métricas de desempenho em VM

O Azure Monitor para VMs inclui um conjunto de gráficos de desempenho que visam vários indicadores-chave de desempenho (KPI's) para ajudá-lo a determinar o quão bem uma máquina virtual está a funcionar. Para aceder a partir do seu VM, execute os seguintes passos.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página VM, na secção **De Monitorização,** selecione **Insights (pré-visualização)**.

3. Selecione o separador **Desempenho**.

Esta página inclui não só gráficos de utilização de desempenho, mas também uma tabela que mostra cada disco lógico descoberto, a sua capacidade, utilização e média total por cada medida.

## <a name="create-alerts"></a>Criar alertas

Pode criar alertas com base em métricas de desempenho específicas. Podem ser utilizados alertas para notificá-lo quando a utilização média da CPU excede um determinado limiar ou o espaço livre em disco disponível diminuir até uma determinada quantidade, por exemplo. Os alertas são apresentados no portal do Azure ou podem ser enviados por e-mail. Também pode acionar runbooks da Automatização do Azure ou do Azure Logic Apps em resposta aos alertas gerados.

O exemplo seguinte cria um alerta para a utilização média da CPU.

1. No portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Clique em **Regras de alerta** no painel da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior do painel de alertas.

3. Indique um **Nome** para o alerta, como *myAlertRule*

4. Para acionar um alerta quando a percentagem da CPU excede 1.0 durante cinco minutos, mantenha todas as outras predefinições selecionadas.

5. Opcionalmente, selecione a caixa para *Proprietários, contribuidores e leitores do e-mail* para enviar uma notificação por e-mail. A ação predefinida é apresentar uma notificação no portal.

6. Clique no botão **OK.**

## <a name="next-steps"></a>Próximos passos

Neste tutorial, configuraste e viste o desempenho do teu VM. Aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Ativar diagnósticos de arranque na VM
> * Ver diagnósticos de arranque
> * Ver métricas de anfitrião
> * Ativar monitor Azure para VMs
> * Ver métricas da VM
> * Criar um alerta

Avance para o próximo tutorial para saber mais sobre o Centro de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerir segurança de VM](../tutorial-azure-security.md)
