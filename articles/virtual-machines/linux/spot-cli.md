---
title: Utilize o CLI para implantar VMs de Spot Azure (Pré-visualização)
description: Aprenda a usar o CLI para implantar VMs De Mancha Azure para economizar custos.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163097"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Pré-visualização: Implemente VMs spot utilizando o Azure CLI

A utilização de [VMs Azure Spot](spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada a uma significativa poupança de custos. A qualquer momento em que o Azure precise de capacidade de volta, a infraestrutura Azure despejará VMs spot. Por isso, os VMs spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes de v/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços dos VMs spot são variáveis, com base na região e no SKU. Para mais informações, consulte os preços vm para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Tem opção de definir um preço máximo que está disposto a pagar, por hora, para o VM. O preço máximo de um Spot VM pode ser fixado em dólares americanos (USD), utilizando até 5 lugares decimais. Por exemplo, `0.98765`o valor seria um preço máximo de $0.98765 USD por hora. Se fixar o preço `-1`máximo para ser, o VM não será despejado com base no preço. O preço do VM será o preço atual para o Spot ou o preço de um VM padrão, o que sempre é menor, desde que haja capacidade e quota disponíveis. Para obter mais informações sobre a fixação do preço máximo, consulte [Spot VMs - Preços](spot-vms.md#pricing).

O processo de criação de um VM com Spot utilizando o CLI Azure é o mesmo que detalhado no [artigo quickstart](/azure/virtual-machines/linux/quick-create-cli). Basta adicionar o parâmetro "--ponto prioritário" e `-1`fornecer um preço máximo ou .

> [!IMPORTANT]
> Os casos de spot estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>



## <a name="install-azure-cli"></a>Instalar a CLI do Azure

Para criar VMs spot, precisa de estar a executar a versão Azure CLI 2.0.74 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Inicie sessão no Azure com [login az](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Criar um Spot VM

Este exemplo mostra como implementar um VM Linux Spot que não será despejado com base no preço. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Após a criação do VM, você pode consultar para ver o preço máximo de faturação para todos os VMs no grupo de recursos.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Passos seguintes**

Também pode criar um Spot VM utilizando [o Azure PowerShell](../windows/spot-powershell.md) ou um [modelo](spot-template.md).

Se encontrar um erro, consulte [códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
