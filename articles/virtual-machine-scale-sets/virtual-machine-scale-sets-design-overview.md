---
title: Considerações de design para conjuntos de escala de máquina virtual Azure
description: Conheça as considerações de design para os seus conjuntos de balanças de máquinas virtuais Azure. Compare as funcionalidades de conjuntos de escala com as funcionalidades VM.
keywords: máquina virtual linux, conjuntos de escala de máquina virtual
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774971"
---
# <a name="design-considerations-for-scale-sets"></a>Considerações de design para conjuntos de escalas
Este artigo discute considerações de design para conjuntos de escala de máquina virtual. Para obter informações sobre quais são os conjuntos de escala de máquina virtual, consulte a [visão geral dos conjuntos de escala de máquinas virtuais](./overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando usar conjuntos de escala em vez de máquinas virtuais?
Geralmente, os conjuntos de escala são úteis para implantar infraestruturas altamente disponíveis onde um conjunto de máquinas tem uma configuração semelhante. No entanto, algumas funcionalidades só estão disponíveis em conjuntos de escala, enquanto outras funcionalidades só estão disponíveis em VMs. Para tomar uma decisão informada sobre quando usar cada tecnologia, você deve primeiro dar uma olhada em algumas das funcionalidades geralmente usadas que estão disponíveis em conjuntos de escala, mas não VMs:

### <a name="scale-set-specific-features"></a>Características específicas do conjunto de escala

- Uma vez especificada a configuração definida na escala, pode atualizar a propriedade de *capacidade* para implantar mais VMs em paralelo. Este processo é melhor do que escrever um script para orquestrar a implementação de muitos VMs individuais em paralelo.
- Pode [utilizar o Azure Autoscale para escalar automaticamente um conjunto de escala,](./virtual-machine-scale-sets-autoscale-overview.md) mas não vMs individuais.
- Pode [reimagem conjunto de VMs,](/rest/api/compute/virtualmachinescalesets/reimage) mas [não VMs individuais.](/rest/api/compute/virtualmachines)
- Pode [aumentar a](#overprovisioning) escala de VMs para aumentar a fiabilidade e os tempos de implementação mais rápidos. Não é possível exagerar nos VM individuais a menos que escreva código personalizado para realizar esta ação.
- Pode especificar uma [política de atualização](./virtual-machine-scale-sets-upgrade-scale-set.md) para facilitar a colocação de upgrades em VMs no seu conjunto de escala. Com VMs individuais, você deve orquestrar atualizações por si mesmo.

### <a name="vm-specific-features"></a>Características específicas do VM

Algumas funcionalidades estão atualmente apenas disponíveis em VMs:

- Pode capturar uma imagem de um VM individual, mas não de um VM num conjunto de escala.
- Pode migrar um VM individual de discos nativos para discos geridos, mas não pode migrar casos de VM num conjunto de escala.
- Pode atribuir endereços IP públicos IPv6 a cartões de interface de rede virtual VM individuais (NICs), mas não pode fazê-lo para instâncias VM num conjunto de escala. Pode atribuir endereços IP públicos IPv6 a equilibradores de carga em frente a VMs individuais ou VMs definidos em escala.

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de escala com discos geridos Azure
Conjuntos de escala podem ser criados com [discos geridos Azure](../virtual-machines/managed-disks-overview.md) em vez de contas tradicionais de armazenamento Azure. Os Discos Geridos proporcionam os seguintes benefícios:
- Não é preciso pré-criar um conjunto de contas de armazenamento Azure para os VMs definidos na escala.
- Pode definir [discos de dados anexados](virtual-machine-scale-sets-attached-disks.md) para os VMs no seu conjunto de escala.
- Os conjuntos de escala podem ser configurados para [suportar até 1.000 VMs num conjunto](virtual-machine-scale-sets-placement-groups.md). 

Se tiver um modelo existente, também pode [atualizar o modelo para utilizar Discos Geridos](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerido pelo utilizador
Um conjunto de escala que não é definido com Discos Geridos Azure baseia-se em contas de armazenamento criadas pelo utilizador para armazenar os discos de SO dos VMs no conjunto. Recomenda-se um rácio de 20 VMs por conta de armazenamento ou menos para atingir o máximo de IO e também aproveitar _a sobreprovisão_ (ver abaixo). Recomenda-se também que espalhe os caracteres inicios dos nomes das contas de armazenamento através do alfabeto. Ao fazê-lo, ajuda a espalhar a carga por diferentes sistemas internos. 


## <a name="overprovisioning"></a>Excesso de revisão
A escala define atualmente o padrão para "superprovisionar" VMs. Com a sobreprovisionamento ligada, o conjunto de escala gira mais VMs do que pediu e, em seguida, elimina os VM extra assim que o número de VMs solicitado for provisionado com sucesso. O excesso de provisão melhora as taxas de sucesso e reduz o tempo de implementação. Não é cobrado pelos VM extra, e eles não contam para os seus limites de quota.

Embora o excesso de provisão melhore as taxas de sucesso do provisionamento, pode causar um comportamento confuso para uma aplicação que não é projetada para lidar com VMs extras que aparecem e depois desaparecem. Para desligar a supervisão, certifique-se de que tem a seguinte corda no seu modelo: `"overprovision": "false"` . Mais detalhes podem ser encontrados na documentação da [API do Scale set REST](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se o seu conjunto de escalas utilizar o armazenamento gerido pelo utilizador e desativar a superprovisionamento, pode ter mais de 20 VMs por conta de armazenamento, mas não é aconselhável ir acima de 40 por razões de desempenho de IO. 

## <a name="limits"></a>Limites
Um conjunto de escala construído numa imagem do Marketplace (também conhecida como imagem de plataforma) e configurado para usar discos geridos Azure suporta uma capacidade de até 1.000 VMs. Se configurar a sua escala definida para suportar mais de 100 VMs, nem todos os cenários funcionam da mesma forma (por exemplo, o equilíbrio de carga). Para obter mais informações, consulte [trabalhar com grandes conjuntos de balanças de máquinas virtuais.](virtual-machine-scale-sets-placement-groups.md) 

Um conjunto de escala configurado com contas de armazenamento geridas pelo utilizador está atualmente limitado a 100 VMs (e 5 contas de armazenamento são recomendadas para esta escala).

Um conjunto de escala construído sobre uma imagem personalizada (uma construída por si) pode ter uma capacidade de até 600 VMs quando configurado com discos geridos Azure. Se o conjunto de escalas estiver configurado com contas de armazenamento geridas pelo utilizador, deve criar todos os VHDs do disco DE NUMA conta de armazenamento. Como resultado, o número máximo recomendado de VMs em um conjunto de escala construído em uma imagem personalizada e armazenamento gerido pelo utilizador é de 20. Se desligar o excesso de revisão, pode chegar aos 40.

Para mais VMs do que estes limites permitem, você precisa implementar conjuntos de várias escalas como mostrado [neste modelo](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/).
