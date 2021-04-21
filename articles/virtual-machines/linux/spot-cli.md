---
title: Utilize o CLI para implantar máquinas virtuais Azure Spot
description: Saiba como usar o CLI para implementar máquinas virtuais Azure Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789614"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Implementar máquinas virtuais Azure Spot utilizando o Azure CLI

A utilização de [Máquinas Virtuais Azure Spot](../spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma economia de custos significativa. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar máquinas virtuais Azure Spot. Portanto, as máquinas virtuais Azure Spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços das máquinas virtuais Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Tem a opção de definir um preço máximo que está disposto a pagar, por hora, pelo VM. O preço máximo de uma Máquina Virtual Azure Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço do VM será o preço atual para a Máquina Virtual Azure Spot ou o preço de um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível. Para obter mais informações sobre a fixação do preço máximo, consulte [máquinas virtuais Azure Spot - Preços](../spot-vms.md#pricing).

O processo de criação de uma Máquina Virtual Azure Spot utilizando o Azure CLI é o mesmo que detalhado no [artigo de arranque rápido](./quick-create-cli.md). Basta adicionar o parâmetro "--prioridade Spot", definir o `--eviction-policy` Deallocate (este é o padrão) ou `Delete` , e fornecer um preço máximo ou `-1` . 


## <a name="install-azure-cli"></a>Instalar a CLI do Azure

Para criar máquinas virtuais Azure Spot, tem de executar a versão Azure CLI 2.0.74 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Inicie sessão no Azure com [início de sessão](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Criar uma máquina virtual Azure Spot

Este exemplo mostra como implementar uma Máquina Virtual Linux Azure Spot que não será despejada com base no preço. A política de despejo está definida para negociar o VM, para que possa ser reiniciada mais tarde. Se pretender eliminar o VM e o disco subjacente quando o VM for despejado, desajuste `--eviction-policy` para `Delete` .

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Após a criação do VM, pode consultar para ver o preço máximo de faturação para todos os VMs do grupo de recursos.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simular um despejo

Pode simular um despejo de uma Máquina Virtual Azure Spot usando REST, PowerShell ou o CLI, para testar quão bem a sua aplicação irá responder a um despejo súbito.

Na maioria dos casos, irá querer utilizar as Máquinas Virtuais REST API [- Simular o Despejo](/rest/api/compute/virtualmachines/simulateeviction) para ajudar no teste automatizado de aplicações. Para rest, um `Response Code: 204` meio que o despejo simulado foi bem sucedido. Pode combinar despejos simulados com o [serviço Evento Agendado,](scheduled-events.md)para automatizar como a sua aplicação irá responder quando o VM for despejado.

Para ver eventos agendados em ação, assista [Azure Friday - Usando Azure Eventos Agendados para preparar a manutenção de VM](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Teste rápido

Para um teste rápido para mostrar como um despejo simulado vai funcionar, vamos percorrer consultando o serviço de eventos agendado para ver como é quando simula um despejo usando o Azure CLI.

O serviço De Evento Agendado está habilitado para o seu serviço na primeira vez que faz um pedido de eventos. 

Remoto para o seu VM e, em seguida, abra um pedido de comando. 

A partir do pedido de comando no seu VM, escreva:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Esta primeira resposta pode demorar até 2 minutos. A partir de agora, devem exibir a saída quase imediatamente.

A partir de um computador que tem o CLI Azure instalado (como a sua máquina local), simular um despejo usando [az vm simular despejo](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Substitua o nome do grupo de recursos e o nome VM pelo seu próprio. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

A saída de resposta terá `Status: Succeeded` se o pedido tiver sido feito com sucesso.

Volte rapidamente à sua ligação remota à sua Máquina Virtual Spot e volte a consultar o ponto de partida de Eventos Agendados. Repita o seguinte comando até obter uma saída que contenha mais informações:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Quando o Serviço de Eventos Agendados receber a notificação de despejo, receberá uma resposta semelhante a esta:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Pode ver `"EventType":"Preempt"` isso, e o recurso é o recurso VM `"Resources":["myspotvm"]` . 

Também pode ver quando o VM será despejado verificando o `"NotBefore"` - o VM não será despejado antes do tempo dado, de modo que é a sua janela para o seu pedido fechar graciosamente.


## <a name="next-steps"></a>Passos seguintes

Também pode criar uma máquina virtual Azure Spot utilizando [a Azure PowerShell,](../windows/spot-powershell.md) [portal](../spot-portal.md)ou um [modelo](spot-template.md).

Consultar informações sobre preços correntes utilizando os preços de [retalho Azure API](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre a Máquina Virtual Azure Spot. O `meterName` e vai conter `skuName` `Spot` ambos.

Se encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).
