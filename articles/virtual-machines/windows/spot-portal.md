---
title: Use o portal para implantar VMs Spot Azure
description: Aprenda a usar o Azure PowerShell para implementar VMs spot para economizar em custos.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158983"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Pré-visualização: Implemente VMs spot utilizando o portal Azure

A utilização de [VMs spot](spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. A qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despejará VMs spot. Por isso, os VMs spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes de v/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços dos VMs spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços vm para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre a fixação do preço máximo, consulte [Spot VMs - Preços](spot-vms.md#pricing).

Tem opção de definir um preço máximo que está disposto a pagar, por hora, para o VM. O preço máximo de um Spot VM pode ser fixado em dólares americanos (USD), utilizando até 5 lugares decimais. Por exemplo, `0.05701`o valor seria um preço máximo de $0.05701 USD por hora. Se fixar o preço `-1`máximo para ser, o VM não será despejado com base no preço. O preço do VM será o preço atual para o local ou o preço de um VM padrão, o que sempre é menor, desde que haja capacidade e quota disponíveis.

> [!IMPORTANT]
> Os casos de spot estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>


## <a name="create-the-vm"></a>Crie a VM

O processo de criação de um VM que utiliza VMs spot é o mesmo que detalhado no [arranque rápido](quick-create-portal.md). Quando estiver a implantar um VM, pode optar por utilizar uma instância de spot Azure.


No separador **Basics,** na secção de detalhes da **ocorrência,** **não** é o padrão para a utilização de uma instância de spot Azure.

![Captura de ecrã para escolher não, não use uma instância de spot Azure](media/spot-portal/no.png)

Seleciona **Sim,** a secção expande-se e pode escolher o seu tipo de despejo e a sua política de [despejo.](spot-vms.md#eviction-policy) 

![Captura de ecrã para escolher sim, use uma instância de spot Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Passos seguintes

Também pode criar VMs spot utilizando [powerShell](spot-powershell.md).