---
title: Considerações de design para conjuntos de dimensionamento de Máquina Virtual do Azure | Documentos da Microsoft
description: Saiba mais sobre as considerações de design para os seus conjuntos de dimensionamento de Máquina Virtual do Azure
keywords: máquina virtual do Linux, os conjuntos de dimensionamento de máquina virtual
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
ms.openlocfilehash: 67bbad7e73f33d73d4c3f1d4f7e5599d2ef914e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618477"
---
# <a name="design-considerations-for-scale-sets"></a>Considerações de design para conjuntos de dimensionamento
Este artigo aborda considerações de design para conjuntos de dimensionamento de Máquina Virtual. Para obter informações sobre quais são os conjuntos de dimensionamento de máquinas virtuais, consulte [descrição geral dos conjuntos de dimensionamento de Máquina Virtual](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando utilizar o dimensionamento define em vez de máquinas virtuais?
Em geral, os conjuntos de dimensionamento são úteis para a implementação de infraestrutura de elevada disponibilidade em que um conjunto de máquinas com configuração semelhante. No entanto, a alguns recursos só estão disponíveis nos conjuntos de dimensionamento, enquanto outros recursos só estão disponíveis nas VMs. Para poder tomar uma decisão informada sobre quando utilizar cada tecnologia, primeiro deve levar uma olhada em alguns dos recursos comumente usados que estão disponíveis nos conjuntos de dimensionamento, mas não VMs:

### <a name="scale-set-specific-features"></a>Funcionalidades de específicas do conjunto de dimensionamento

- Depois de especificar o conjunto de dimensionamento configuração, pode atualizar o *capacidade* propriedade para implementar mais VMs em paralelo. Este processo é melhor do que escrever um script para orquestrar a implementação de muitas VMs individuais em paralelo.
- Pode [utilizar o dimensionamento automático do Azure para dimensionar automaticamente um conjunto de dimensionamento](./virtual-machine-scale-sets-autoscale-overview.md) mas VMs não individuais.
- Pode [VMs de conjunto de dimensionamento de recriação de imagem](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) mas [VMs individuais não](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Pode [sobreaprovisionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) VMs de conjunto de dimensionamento para maior confiabilidade e tempos de implantação mais rápidos. Não é possível sobreaprovisionar VMs individuais, a menos que escrever código personalizado para efetuar esta ação.
- Pode especificar uma [política de atualização](./virtual-machine-scale-sets-upgrade-scale-set.md) torna mais fácil de implementar as atualizações entre VMs no conjunto de dimensionamento. Com as VMs individuais, deve orquestrar atualizações por conta própria.

### <a name="vm-specific-features"></a>Recursos específicos da VM

Algumas funcionalidades só estão atualmente disponíveis nas VMs:

- Pode capturar uma imagem a partir de uma VM individual, mas não a partir de uma VM num conjunto de dimensionamento.
- Pode migrar uma VM individual de discos de nativos para os managed disks, mas não é possível migrar instâncias de VM num conjunto de dimensionamento.
- Pode atribuir endereços IP públicos IPv6 para placas de interface individuais rede virtual de VM (NICs), mas não é possível fazê-lo para instâncias de VM num conjunto de dimensionamento. Pode atribuir endereços IP públicos IPv6 para carregar balanceadores à frente de qualquer uma das VMs individuais ou conjunto de dimensionamento de VMs.

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de dimensionamento com Managed Disks do Azure
Conjuntos de dimensionamento podem ser criados com [Managed Disks do Azure](../virtual-machines/windows/managed-disks-overview.md) em vez de contas de armazenamento do Azure tradicional. Discos geridos fornecem as seguintes vantagens:
- Não é necessário pré-criar um conjunto de contas de armazenamento do Azure para VMs do conjunto de dimensionamento.
- Pode definir [discos de dados anexados](virtual-machine-scale-sets-attached-disks.md) para as VMs no seu dimensionamento definido.
- Conjuntos de dimensionamento podem ser configurados para [suportam até 1000 VMs num conjunto de](virtual-machine-scale-sets-placement-groups.md). 

Se tiver um modelo existente, também pode [atualizar o modelo para utilizar os Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerida pelo utilizador
Um conjunto de dimensionamento que não está definido com os Azure Managed Disks baseia-se em contas de armazenamento criados pelo utilizador para armazenar os discos de SO das VMs no conjunto. Um rácio de 20 VMs por conta de armazenamento ou menos é recomendado para alcançar a e/s máximo e também aproveitar _aprovisionar em excesso_ (ver abaixo). Também é recomendável que atravessando os carateres iniciais dos nomes de conta de armazenamento do alfabeto. Isso ajuda a distribuir a carga por diferentes sistemas internos. 


## <a name="overprovisioning"></a>Aprovisionar em excesso
Conjuntos de dimensionamento atualmente predefinido para "aprovisionamento em excesso" VMs. Com a opção ativada, a escala definida, na verdade, rotações segurança de VMs mais do que solicitou, em seguida, elimina a VMs adicionais depois do número pedido de VMs é aprovisionado com êxito. Aprovisionar em excesso melhora a taxas de êxito de aprovisionamento e reduz o tempo de implementação. Não é cobrada a VMs adicionais, e elas não contam para os limites de quota.

Enquanto o excesso melhorar as taxas de êxito de aprovisionamento, pode causar um comportamento confuso para uma aplicação que não foi concebido para lidar com VMs adicionais que aparecem e, em seguida, desaparecendo. Para ativar a aprovisionar em excesso terminar, certifique-se de ter a seguinte cadeia de caracteres no seu modelo: `"overprovision": "false"`. Podem encontrar mais detalhes no [documentação da API de REST do conjunto de dimensionamento](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se o conjunto de dimensionamento utiliza o armazenamento gerido pelo utilizador e desativar a opção de aprovisionar em excesso, pode ter mais de 20 VMs por conta de armazenamento, mas não é recomendado para passar de 40 por motivos de desempenho de e/s. 

## <a name="limits"></a>Limits
Um conjunto de dimensionamento criados numa imagem do Marketplace (também conhecido como uma imagem de plataforma) e configurado para utilizar Managed Disks do Azure suporta uma capacidade de até 1000 VMs. Se configurar o seu conjunto de dimensionamento para suportar mais de 100 VMs, o nem todos os cenários funcionam da mesma (por exemplo o balanceamento de carga). Para obter mais informações, consulte [trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande](virtual-machine-scale-sets-placement-groups.md). 

Um conjunto configurado com contas de armazenamento gerida pelo utilizador de dimensionamento está limitado a 100 VMs (e 5 contas de armazenamento são recomendadas para desta escala).

Um conjunto de dimensionamento criado numa imagem personalizada (um criado por si) pode ter uma capacidade de até 600 VMs quando configurado com os Azure Managed disks. Se o conjunto de dimensionamento está configurado com contas de armazenamento gerida pelo utilizador, tem de criar todos os VHDs de discos de SO numa mesma conta de armazenamento. Como resultado, o máximo recomendado número de VMs num conjunto de dimensionamento criado numa imagem personalizada e de armazenamento gerida pelo utilizador é 20. Se desativar a aprovisionar em excesso, é possível ir até 40.

Para obter mais VMs que permitir que estes limites, terá de implementar vários conjuntos de dimensionamento, conforme mostrado na [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

