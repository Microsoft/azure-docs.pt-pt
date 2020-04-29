---
title: Use o portal para implantar VMs Spot Azure
description: Aprenda a usar o Azure PowerShell para implementar VMs spot para economizar em custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 6e7723a437e90807063e3c3b7af2bf068dca5b9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100656"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Implementar VMs spot usando o portal Azure

A utilização de [VMs spot](spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. A qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despejará VMs spot. Por isso, os VMs spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes de v/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços dos VMs spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços vm para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre a fixação do preço máximo, consulte [Spot VMs - Preços](spot-vms.md#pricing).

Tem opção de definir um preço máximo que está disposto a pagar, por hora, para o VM. O preço máximo de um Spot VM pode ser fixado em dólares americanos (USD), utilizando até 5 lugares decimais. Por exemplo, `0.05701`o valor seria um preço máximo de $0.05701 USD por hora. Se fixar o preço `-1`máximo para ser, o VM não será despejado com base no preço. O preço do VM será o preço atual para o local ou o preço de um VM padrão, o que sempre é menor, desde que haja capacidade e quota disponíveis.


## <a name="create-the-vm"></a>Crie a VM

O processo de criação de um VM que utiliza VMs spot é o mesmo que detalhado no [arranque rápido](quick-create-portal.md). Quando estiver a implantar um VM, pode optar por utilizar uma instância de spot Azure.


No separador **Basics,** na secção de detalhes da **ocorrência,** **não** é o padrão para a utilização de uma instância de spot Azure.

![Captura de ecrã para escolher não, não use uma instância de spot Azure](media/spot-portal/no.png)

Seleciona **Sim,** a secção expande-se e pode escolher o seu tipo de despejo e a sua política de [despejo.](spot-vms.md#eviction-policy) 

![Captura de ecrã para escolher sim, use uma instância de spot Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Passos seguintes

Também pode criar VMs spot utilizando [powerShell](spot-powershell.md).