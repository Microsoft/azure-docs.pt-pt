---
title: Considerações de design para conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba mais sobre as considerações de design para seus conjuntos de dimensionamento de máquinas virtuais do Azure. Compare os recursos dos conjuntos de dimensionamento com recursos de VM.
keywords: máquina virtual Linux, conjuntos de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: 4238e96465a1fd7ad3e73c62134437cd819fba8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359448"
---
# <a name="design-considerations-for-scale-sets"></a>Considerações de design para conjuntos de dimensionamento
Este artigo discute considerações de design para conjuntos de dimensionamento de máquinas virtuais. Para obter informações sobre quais conjuntos de dimensionamento de máquinas virtuais são, consulte [visão geral dos conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando usar conjuntos de dimensionamento em vez de máquinas virtuais?
Em geral, os conjuntos de dimensionamento são úteis para implantar a infraestrutura altamente disponível em que um conjunto de computadores tem configuração semelhante. No entanto, alguns recursos só estão disponíveis em conjuntos de dimensionamento, enquanto outros recursos só estão disponíveis em VMs. Para tomar uma decisão informada sobre quando usar cada tecnologia, primeiro dê uma olhada em alguns dos recursos mais usados que estão disponíveis em conjuntos de dimensionamento, mas não em VMs:

### <a name="scale-set-specific-features"></a>Recursos específicos do conjunto de dimensionamento

- Depois de especificar a configuração do conjunto de dimensionamento, você pode atualizar a propriedade *Capacity* para implantar mais VMs em paralelo. Esse processo é melhor do que escrever um script para orquestrar a implantação de várias VMs individuais em paralelo.
- Você pode [usar a autoescala do Azure para dimensionar automaticamente um conjunto de dimensionamento](./virtual-machine-scale-sets-autoscale-overview.md) , mas não para VMs individuais.
- Você pode refazer a [imagem de VMs do conjunto de dimensionamento](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) , mas [não de VMs individuais](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Você pode [provisionar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) as VMs do conjunto de dimensionamento para maior confiabilidade e tempos de implantação mais rápidos. Não é possível provisionar VMs individuais, a menos que você escreva código personalizado para executar esta ação.
- Você pode especificar uma [política de atualização](./virtual-machine-scale-sets-upgrade-scale-set.md) para facilitar a implementação de atualizações entre VMs em seu conjunto de dimensionamento. Com VMs individuais, você deve orquestrar as atualizações por conta própria.

### <a name="vm-specific-features"></a>Recursos específicos da VM

Alguns recursos estão disponíveis no momento apenas em VMs:

- Você pode capturar uma imagem de uma VM individual, mas não de uma VM em um conjunto de dimensionamento.
- Você pode migrar uma VM individual de discos nativos para discos gerenciados, mas não pode migrar instâncias de VM em um conjunto de dimensionamento.
- Você pode atribuir endereços IP públicos IPv6 a NICs (placas de interface de rede) virtuais individuais da VM, mas não pode fazer isso para instâncias de VM em um conjunto de dimensionamento. Você pode atribuir endereços IP públicos IPv6 a balanceadores de carga na frente de VMs individuais ou de VMs do conjunto de dimensionamento.

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de dimensionamento com o Azure Managed Disks
Os conjuntos de dimensionamento podem ser criados com o [azure Managed disks](../virtual-machines/windows/managed-disks-overview.md) em vez de contas de armazenamento do Azure tradicionais. Managed Disks fornecem os seguintes benefícios:
- Você não precisa criar previamente um conjunto de contas de armazenamento do Azure para as VMs do conjunto de dimensionamento.
- Você pode definir [discos de dados anexados](virtual-machine-scale-sets-attached-disks.md) para as VMs em seu conjunto de dimensionamento.
- Os conjuntos de dimensionamento podem ser configurados para [dar suporte a até 1.000 VMs em um conjunto](virtual-machine-scale-sets-placement-groups.md). 

Se você tiver um modelo existente, também poderá [atualizar o modelo para usar Managed disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerenciado pelo usuário
Um conjunto de dimensionamento que não é definido com o Azure Managed Disks depende de contas de armazenamento criadas pelo usuário para armazenar os discos do sistema operacional das VMs no conjunto. Uma taxa de 20 VMs por conta de armazenamento ou menos é recomendada para atingir o máximo de e/s e também aproveitar o excesso de _provisionamento_ (veja abaixo). Também é recomendável que você distribua os caracteres iniciais dos nomes de conta de armazenamento pelo alfabeto. Isso ajuda a espalhar a carga entre diferentes sistemas internos. 


## <a name="overprovisioning"></a>Excesso
Os conjuntos de dimensionamento atualmente são padrão para "excesso de provisionamento" de VMs. Com o excesso de provisionamento ativado, o conjunto de dimensionamento na verdade gira mais VMs do que você pediu e, em seguida, exclui as VMs extras depois que o número solicitado de VMs é provisionado com êxito. O provisionamento em excesso melhora as taxas de sucesso do provisionamento e reduz o tempo de implantação. Você não é cobrado pelas VMs extras e elas não contam para os limites de cota.

Embora o provisionamento em excesso melhore as taxas de sucesso de provisionamento, isso pode causar um comportamento confuso para um aplicativo que não foi projetado para lidar com VMs extras aparecendo e, em seguida, desaparecendo. Para desativar o excesso de provisionamento, verifique se você tem a seguinte cadeia de caracteres em seu modelo: `"overprovision": "false"`. Mais detalhes podem ser encontrados na [documentação da API REST do conjunto de dimensionamento](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se o conjunto de dimensionamento usar o armazenamento gerenciado pelo usuário e você desativar o provisionamento em excesso, você poderá ter mais de 20 VMs por conta de armazenamento, mas não é recomendável passar acima de 40 por motivos de desempenho de e/s. 

## <a name="limits"></a>Limites
Um conjunto de dimensionamento criado em uma imagem do Marketplace (também conhecido como uma imagem de plataforma) e configurado para usar o Azure Managed Disks dá suporte a uma capacidade de até 1.000 VMs. Se você configurar seu conjunto de dimensionamento para dar suporte a mais de 100 VMs, nem todos os cenários funcionam da mesma (por exemplo, balanceamento de carga). Para obter mais informações, consulte [trabalhando com grandes conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-placement-groups.md). 

Um conjunto de dimensionamento configurado com contas de armazenamento gerenciadas pelo usuário está limitado atualmente a 100 VMs (e 5 contas de armazenamento são recomendadas para essa escala).

Um conjunto de dimensionamento criado em uma imagem personalizada (criada por você) pode ter uma capacidade de até 600 VMs quando configurado com o Azure Managed disks. Se o conjunto de dimensionamento for configurado com contas de armazenamento gerenciadas pelo usuário, ele deverá criar todos os VHDs de disco do sistema operacional em uma conta de armazenamento. Como resultado, o número máximo recomendado de VMs em um conjunto de dimensionamento criado em uma imagem personalizada e no armazenamento gerenciado pelo usuário é 20. Se você desativar o provisionamento em excesso, poderá ir até 40.

Para mais VMs do que esses limites permitem, você precisa implantar vários conjuntos de dimensionamento, conforme mostrado neste [modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

