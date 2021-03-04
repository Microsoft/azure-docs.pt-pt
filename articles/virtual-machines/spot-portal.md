---
title: Utilize o portal para implementar máquinas virtuais Azure Spot
description: Como utilizar o Azure PowerShell para implementar máquinas virtuais spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098575"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Implementar máquinas virtuais Azure Spot utilizando o portal Azure

A utilização de [Máquinas Virtuais Azure Spot](spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma economia de custos significativa. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar máquinas virtuais Azure Spot. Portanto, as máquinas virtuais Azure Spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços das máquinas virtuais Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre a fixação do preço máximo, consulte [máquinas virtuais Azure Spot - Preços](spot-vms.md#pricing).

Tem a opção de definir um preço máximo que está disposto a pagar, por hora, pelo VM. O preço máximo de uma Máquina Virtual Azure Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.05701` seria um preço máximo de $0.05701 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço do VM será o preço atual para o spot ou o preço para um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível.

Quando o VM é despejado, tem a opção de eliminar o VM e o disco subjacente ou de negociar o VM para que possa ser reiniciado mais tarde.


## <a name="create-the-vm"></a>Criar a VM

Ao colocar um VM, pode optar por utilizar uma instância de ponto Azure.


No **separador Basics,** na secção Detalhes de **Instância,** **Não** é o padrão para a utilização de uma instância de spot Azure.

![Captura de ecrã para escolher não, não use uma instância de spot Azure](./media/spot-portal/no.png)

Se selecionar **Sim,** a secção expande-se e pode escolher o seu [tipo de despejo e política de despejo.](spot-vms.md#eviction-policy) 

![Captura de ecrã para escolher sim, use uma instância de spot Azure](./media/spot-portal/yes.png)

Também pode comparar as tarifas de preços e despejos com outras regiões similares, selecionando **o histórico de preços do View e comparando os preços nas regiões vizinhas.**

Neste exemplo, a região central do Canadá é mais barata e tem uma taxa de despejo mais baixa do que a região oriental dos EUA.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Screenshot das opções da região com a diferença de preços e taxas de despejo.":::

Pode alterar a região selecionando a escolha que funciona melhor para si e, em seguida, selecionando **OK**.

## <a name="simulate-an-eviction"></a>Simular um despejo

Você pode [simular um despejo](/rest/api/compute/virtualmachines/simulateeviction) de uma Máquina Virtual Azure Spot, para testar quão bem a sua aplicação irá responder a um despejo súbito. 

Substitua as seguintes informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` significa que o despejo simulado foi bem sucedido. 

## <a name="next-steps"></a>Passos seguintes

Também pode criar máquinas virtuais Azure Spot utilizando [PowerShell,](./windows/spot-powershell.md) [CLI](./linux/spot-cli.md)ou um [modelo](./linux/spot-template.md).
