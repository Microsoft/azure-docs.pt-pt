---
title: Migrar a configuração da piscina do Lote de Cloud Services para Máquinas Virtuais
description: Saiba como atualizar a configuração da piscina para a configuração mais recente e recomendada
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: b6f4184f7c4f133f74cb3157638b1621dad25fda
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97969032"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrar a configuração da piscina do Lote de Cloud Services para Máquinas Virtuais

Os lotes podem ser criados utilizando a [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) ou [a virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). 'virtualMachineConfiguration' é a configuração recomendada, uma vez que suporta todas as capacidades do Batch. As piscinas 'cloudServiceConfiguration' não suportam todas as funcionalidades e não estão previstas novas funcionalidades.

Se utilizar piscinas de "cloudServiceConfiguration", é altamente recomendável que se mova para utilizar piscinas de "configuração virtual de investigação". Isto permitir-lhe-á beneficiar de todas as capacidades do Batch, tais como uma seleção alargada [de séries VM,](batch-pool-vm-sizes.md)VMs Linux, [contentores,](batch-docker-container-workloads.md) [redes virtuais Azure Resource Manager](batch-virtual-network.md)e [encriptação de discos de nó.](disk-encryption.md)

Este artigo descreve como migrar para a "Configuração virtual da Investigação".

## <a name="new-pools-are-required"></a>São necessárias novas piscinas

Os pools ativos existentes não podem ser atualizados de 'cloudServiceConfiguration' para 'virtualMachineConfiguration', devem ser criados novos pools. A criação de piscinas utilizando a 'configuração virtualMachine' é suportada por todas as APIs do lote, ferramentas de linha de comando, portal Azure e o Batch Explorer UI.

Os tutoriais [.NET](tutorial-parallel-dotnet.md) e [Python](tutorial-parallel-python.md) fornecem exemplos de criação de piscinas usando a "configuração virtual da investigação".

## <a name="pool-configuration-differences"></a>Diferenças de configuração da piscina

O seguinte deve ser considerado na atualização da configuração da piscina:

- Os nós de piscina 'cloudServiceConfiguration' são sempre Windows OS, as piscinas 'virtualMachineConfiguration' podem ser Linux ou Windows OS.
- Em comparação com as piscinas 'cloudServiceConfiguration', as piscinas 'virtualMachineConfiguration' têm um conjunto de capacidades mais rico, tais como suporte a contentores, discos de dados e encriptação de discos.
- Os nós de piscina 'virtualMachineConfiguration' utilizam discos de OS geridos. O [tipo de disco gerido](../virtual-machines/disks-types.md) que é utilizado para cada nó depende do tamanho VM escolhido para a piscina. Se for especificado um tamanho VM 's' para a piscina, por exemplo , "Standard_D2s_v3", então é utilizado um SSD premium. Se for especificado um tamanho VM 'não-s', por exemplo , 'Standard_D2_v3', então é utilizado um HDD padrão.

   > [!IMPORTANT]
   > Tal como acontece com máquinas virtuais e conjuntos de balanças de máquinas virtuais, o disco gerido pelo SISTEMA utilizado para cada nó incorre num custo, que é adicional ao custo dos VMs. Não existe um custo de disco DE PARA os nós 'cloudServiceConfiguration', uma vez que o disco OS é criado nos nós locais SSD.

- O arranque de piscinas e nó e os tempos de eliminação podem diferir ligeiramente entre piscinas 'cloudServiceConfiguration' e piscinas 'virtualMachineConfiguration'.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as configurações da piscina.](nodes-and-pools.md#configurations)
- Saiba mais sobre [as melhores práticas da piscina.](best-practices.md#pools)
- Referência API REST para [a adição de piscina](https://docs.microsoft.com/rest/api/batchservice/pool/add) e [configuração virtual.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)
