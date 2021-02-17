---
title: Migrar a configuração da piscina do Lote de Cloud Services para Máquinas Virtuais
description: Saiba como atualizar a configuração da piscina para a configuração mais recente e recomendada
ms.topic: how-to
ms.date: 2/16/2021
ms.openlocfilehash: 9cbcf3864526bd8f8132f3b0f729e2d728e07bb8
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546045"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrar a configuração da piscina do Lote de Cloud Services para Máquinas Virtuais

Os lotes podem ser criados utilizando a [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) ou [a virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration). 'virtualMachineConfiguration' é a configuração recomendada, uma vez que suporta todas as capacidades do Batch. As piscinas 'cloudServiceConfiguration' não suportam todas as funcionalidades e não estão previstas novas funcionalidades.

Se utilizar piscinas de "cloudServiceConfiguration", é altamente recomendável que se mova para utilizar piscinas de "configuração virtual de investigação". Isto permitir-lhe-á beneficiar de todas as capacidades do Batch, tais como uma seleção alargada [de séries VM,](batch-pool-vm-sizes.md)VMs Linux, [contentores,](batch-docker-container-workloads.md) [redes virtuais Azure Resource Manager](batch-virtual-network.md)e [encriptação de discos de nó.](disk-encryption.md)

Este artigo descreve como migrar para a "Configuração virtual da Investigação".

## <a name="new-pools-are-required"></a>São necessárias novas piscinas

Os pools ativos existentes não podem ser atualizados de 'cloudServiceConfiguration' para 'virtualMachineConfiguration', devem ser criados novos pools. A criação de piscinas utilizando a 'configuração virtualMachine' é suportada por todas as APIs do lote, ferramentas de linha de comando, portal Azure e o Batch Explorer UI.

**Os tutoriais [.NET](tutorial-parallel-dotnet.md) e [Python](tutorial-parallel-python.md) fornecem exemplos de criação de piscinas usando a "configuração virtual da investigação".**

## <a name="pool-configuration-differences"></a>Diferenças de configuração da piscina

O seguinte deve ser considerado na atualização da configuração da piscina:

- Os nós de piscina 'cloudServiceConfiguration' são sempre Windows OS, as piscinas 'virtualMachineConfiguration' podem ser Linux ou Windows OS.
- Em comparação com as piscinas 'cloudServiceConfiguration', as piscinas 'virtualMachineConfiguration' têm um conjunto de capacidades mais rico, tais como suporte a contentores, discos de dados e encriptação de discos.
- Os nós de piscina 'virtualMachineConfiguration' utilizam discos de OS geridos. O [tipo de disco gerido](../virtual-machines/disks-types.md) que é utilizado para cada nó depende do tamanho VM escolhido para a piscina. Se for especificado um tamanho VM 's' para a piscina, por exemplo , "Standard_D2s_v3", então é utilizado um SSD premium. Se for especificado um tamanho VM 'não-s', por exemplo , 'Standard_D2_v3', então é utilizado um HDD padrão.

   > [!IMPORTANT]
   > Tal como acontece com máquinas virtuais e conjuntos de balanças de máquinas virtuais, o disco gerido pelo SISTEMA utilizado para cada nó incorre num custo, que é adicional ao custo dos VMs. Não existe um custo de disco DE PARA os nós 'cloudServiceConfiguration', uma vez que o disco OS é criado nos nós locais SSD.

- O arranque de piscinas e nó e os tempos de eliminação podem diferir ligeiramente entre piscinas 'cloudServiceConfiguration' e piscinas 'virtualMachineConfiguration'.

## <a name="azure-data-factory-custom-activity-pools"></a>Piscinas de atividade personalizada azure Data Factory

As piscinas Azure Batch podem ser usadas para executar atividades personalizadas da Data Factory. Quaisquer piscinas de 'cloudServiceConfiguration' utilizadas para executar atividades personalizadas terão de ser eliminadas e novas piscinas 'virtualMachineConfiguration' criadas.

- As condutas devem ser pausadas antes de eliminar/recriar para garantir que não serão interrompidas execuções.
- O mesmo id de piscina pode ser usado para evitar alterações na configuração do serviço ligado.
- Retomar os oleodutos quando novas piscinas tiverem sido criadas.

Para mais informações sobre a utilização do Azure Batch para executar atividades personalizadas da Data Factory:

- [Serviço ligado a Azure Batch](../data-factory/compute-linked-services.md#azure-batch-linked-service)
- [Atividades personalizadas num oleoduto da Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as configurações da piscina.](nodes-and-pools.md#configurations)
- Saiba mais sobre [as melhores práticas da piscina.](best-practices.md#pools)
- Referência API REST para [a adição de piscina](/rest/api/batchservice/pool/add) e [configuração virtual.](/rest/api/batchservice/pool/add#virtualmachineconfiguration)