---
title: Considerações de design para conjuntos de escala de máquinas virtuais azure
description: Conheça as considerações de design para os seus Conjuntos de Escala de Máquinas Virtuais Azure. Compare as funcionalidades de conjuntos de escala com as funcionalidades VM.
keywords: máquina virtual linux,conjuntos de escala de máquina virtual
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: b427319fdba634ea3c61681baa30547450709dc1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390469"
---
# <a name="design-considerations-for-scale-sets"></a>Considerações de design para conjuntos de escala
Este artigo discute considerações de design para conjuntos de escala de máquina virtual. Para obter informações sobre o que são os conjuntos de escala de máquina virtual, consulte a visão geral dos [conjuntos](virtual-machine-scale-sets-overview.md)de escala de máquina virtual .

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando usar conjuntos de escala em vez de máquinas virtuais?
Geralmente, os conjuntos de escala são úteis para implantar infraestruturas altamente disponíveis onde um conjunto de máquinas tem configuração semelhante. No entanto, algumas funcionalidades só estão disponíveis em conjuntos de escala, enquanto outras funcionalidades só estão disponíveis em VMs. Para tomar uma decisão informada sobre quando usar cada tecnologia, deve primeiro dar uma olhada em algumas das funcionalidades comumente utilizadas que estão disponíveis em conjuntos de escala, mas não vMs:

### <a name="scale-set-specific-features"></a>Características específicas do conjunto de escala

- Assim que especificar a configuração do conjunto de escala, pode atualizar a propriedade de *capacidade* para implantar mais VMs em paralelo. Este processo é melhor do que escrever um guião para orquestrar a implantação de muitos VMs individuais em paralelo.
- Pode utilizar a [escala Automática Azure para escalar automaticamente um conjunto](./virtual-machine-scale-sets-autoscale-overview.md) de escala, mas não os VMs individuais.
- Pode [reimagem de conjuntos de vMs](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) mas [não vMs individuais.](https://docs.microsoft.com/rest/api/compute/virtualmachines)
- Pode [sobressumar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) a escala de configuração de VMs para um aumento da fiabilidade e tempos de implantação mais rápidos. Não pode fornecer VMs individuais a menos que escreva código personalizado para realizar esta ação.
- Pode especificar uma política de [upgrade](./virtual-machine-scale-sets-upgrade-scale-set.md) para facilitar a implementação de upgrades em VMs no seu conjunto de escala. Com VMs individuais, você deve orquestrar atualizações por si mesmo.

### <a name="vm-specific-features"></a>Características específicas do VM

Atualmente, algumas funcionalidades estão disponíveis apenas em VMs:

- Pode capturar uma imagem de um VM individual, mas não de um VM num conjunto de escala.
- Pode migrar um VM individual de discos nativos para discos geridos, mas não pode migrar instâncias vm num conjunto de escala.
- Pode atribuir endereços IP públicos IPv6 a cartões individuais de interface de rede vM (NICs), mas não pode fazê-lo para instâncias vm num conjunto de escala. Pode atribuir endereços IP públicos IPv6 para carregar os equilibradores na frente de vMs individuais ou vMs de escala.

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de escala com discos geridos azure
Conjuntos de escala podem ser criados com [Discos Geridos Azure](../virtual-machines/windows/managed-disks-overview.md) em vez de contas tradicionais de armazenamento Azure. Os Discos Geridos proporcionam os seguintes benefícios:
- Não é preciso pré-criar um conjunto de contas de armazenamento Azure para os VMs definidos em escala.
- Pode definir os discos de [dados anexados](virtual-machine-scale-sets-attached-disks.md) para os VMs no seu conjunto de escala.
- Os conjuntos de escala podem ser configurados para [suportar até 1.000 VMs num conjunto](virtual-machine-scale-sets-placement-groups.md). 

Se tiver um modelo existente, também pode [atualizar o modelo para utilizar discos geridos](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerido pelo utilizador
Um conjunto de escala que não é definido com discos geridos azure baseia-se em contas de armazenamento criadas pelo utilizador para armazenar os discos OS dos VMs no conjunto. Recomenda-se ou menos um rácio de 20 VMs por conta de armazenamento para atingir o máximo de IO e também tirar partido do excesso de _oferta_ (ver abaixo). Recomenda-se também que espalhe os caracteres iniciantes dos nomes da conta de armazenamento através do alfabeto. Fazê-lo ajuda a espalhar carga em diferentes sistemas internos. 


## <a name="overprovisioning"></a>Excesso de oferta
Os conjuntos de escala atualmente não estão em incumprimento para VMs "overprovisioning". Com o excesso de provisionamento ligado, o conjunto de escala realmente gira mais VMs do que pediu, em seguida, elimina os VMextra extra uma vez que o número solicitado de VMs são aprovisionados com sucesso. O excesso de oferta melhora as taxas de sucesso e reduz o tempo de implantação. Não está cobrado pelos VMextra extras, e eles não contam para os seus limites de quota.

Embora o excesso de oferta melhore as taxas de sucesso, pode causar comportamentos confusos para uma aplicação que não foi concebida para lidar com VMs extras que aparecem e depois desaparecem. Para desligar o excesso de fornecimento, certifique-se de que tem a seguinte corda no seu modelo: `"overprovision": "false"`. Mais detalhes podem ser encontrados na [documentação da Scale set REST API](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se o seu conjunto de escala utilizar o armazenamento gerido pelo utilizador, e desativar o excesso de oferta, pode ter mais de 20 VMs por conta de armazenamento, mas não é aconselhável ultrapassar os 40 por razões de desempenho da OI. 

## <a name="limits"></a>Limites
Um conjunto de escala construído sobre uma imagem do Marketplace (também conhecida como imagem de plataforma) e configurado para utilizar Discos Geridos Azure suporta uma capacidade de até 1.000 VMs. Se configurar a sua balança definida para suportar mais de 100 VMs, nem todos os cenários funcionam da mesma forma (por exemplo, o equilíbrio de carga). Para mais informações, consulte [Trabalhar com grandes conjuntos](virtual-machine-scale-sets-placement-groups.md)de escala de máquinavirtual . 

Um conjunto de escala configurado com contas de armazenamento geridas pelo utilizador está atualmente limitado a 100 VMs (e 5 contas de armazenamento são recomendadas para esta escala).

Um conjunto de escala construído sobre uma imagem personalizada (construída por si) pode ter uma capacidade de até 600 VMs quando configurado com discos Geridos azure. Se o conjunto de escala estiver configurado com contas de armazenamento geridas pelo utilizador, deve criar todos os VHDs do disco OS dentro de uma conta de armazenamento. Como resultado, o número máximo recomendado de VMs num conjunto de escala construído sobre uma imagem personalizada e armazenamento gerido pelo utilizador é de 20. Se desligar o excesso de oferta, pode subir até aos 40.

Para mais VMs do que estes limites permitem, você precisa implementar conjuntos de escala múltiplas como mostrado [neste modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

