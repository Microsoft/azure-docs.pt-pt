---
title: Utilize o portal para implantar VMs Azure Spot
description: Aprenda a usar o Azure PowerShell para implementar VMs spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 6e7723a437e90807063e3c3b7af2bf068dca5b9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82100656"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Implementar VMs spot utilizando o portal Azure

A utilização [de VMs spot](spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar os VM spot. Por isso, os VM spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços dos VM spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre a fixação do preço máximo, consulte [Spot VMs - Preços](spot-vms.md#pricing).

Tem a opção de definir um preço máximo que está disposto a pagar, por hora, pelo VM. O preço máximo de um Spot VM pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.05701` seria um preço máximo de $0.05701 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço do VM será o preço atual para o spot ou o preço para um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível.


## <a name="create-the-vm"></a>Crie a VM

O processo de criação de um VM que utilize VMs spot é o mesmo que detalhado no [arranque rápido.](quick-create-portal.md) Ao colocar um VM, pode optar por utilizar uma instância de ponto Azure.


No **separador Basics,** na secção Detalhes de **Instância,** **Não** é o padrão para a utilização de uma instância de spot Azure.

![Captura de ecrã para escolher não, não use uma instância de spot Azure](media/spot-portal/no.png)

Selecione **Sim,** a secção expande e pode escolher o seu [tipo de despejo e política de despejo.](spot-vms.md#eviction-policy) 

![Captura de ecrã para escolher sim, use uma instância de spot Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Passos seguintes

Também pode criar VMs spot utilizando [PowerShell](spot-powershell.md).