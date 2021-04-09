---
title: Migrar a configuração da piscina do Lote de Cloud Services para Máquinas Virtuais
description: Saiba como atualizar a configuração da piscina para a configuração mais recente e recomendada
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200563"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migrar a configuração da piscina do Lote de Cloud Services para Máquina Virtual

Atualmente, as piscinas batch podem ser criadas usando a [configuração virtual DesachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) ou [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). Recomendamos a utilização apenas da Configuração virtual da máquina, uma vez que esta configuração suporta todas as capacidades do Lote.

As piscinas de configuração de serviços de nuvem não suportam algumas das funcionalidades atuais do Batch e não suportam quaisquer funcionalidades recém-adicionadas. Não poderá criar novas piscinas 'CloudServiceConfiguration' ou adicionar novos nós às piscinas existentes [após 29 de fevereiro de 2024.](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)

Se as suas soluções Batch utilizarem atualmente piscinas de 'cloudServiceConfiguration', recomendamos a alteração para 'virtualMachineConfiguration' o mais rapidamente possível. Isto permitir-lhe-á beneficiar de todas as capacidades do Batch, tais como uma seleção alargada [de séries VM,](batch-pool-vm-sizes.md)VMs Linux, [contentores,](batch-docker-container-workloads.md) [redes virtuais Azure Resource Manager](batch-virtual-network.md)e [encriptação de discos de nó.](disk-encryption.md)

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Criar uma piscina usando a configuração da máquina virtual

Não é possível mudar um pool ativo existente que utilize a 'cloudServiceConfiguration' para utilizar a "configuração virtual da tecnologia". Em vez disso, terá de criar novas piscinas. Uma vez criado as suas novas piscinas 'virtualMachineConfiguration' e replicado todos os seus trabalhos e tarefas, pode eliminar as antigas piscinas 'cloudServiceConfiguration' que já não está a usar.

Todas as APIs do lote, ferramentas de linha de comando, o portal Azure e o Batch Explorer UI permitem criar piscinas usando a "configuração virtualMachineConfiguration".

Para uma passagem pelo processo de criação de piscinas que utilizem 'virtualMachineConfiguration, consulte o [tutorial .NET](tutorial-parallel-dotnet.md) ou o [tutorial python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Diferenças de configuração da piscina

Algumas das principais diferenças entre as duas configurações incluem:

- Os nós de piscina 'cloudServiceConfiguration' utilizam apenas o Windows OS. As piscinas 'virtualMachineConfiguration' podem utilizar o Linux ou o Windows OS.
- Em comparação com as piscinas 'cloudServiceConfiguration', as piscinas 'virtualMachineConfiguration' têm um conjunto de capacidades mais rico, tais como suporte a contentores, discos de dados e encriptação de discos.
- O arranque de piscinas e nó e os tempos de eliminação podem diferir ligeiramente entre piscinas 'cloudServiceConfiguration' e piscinas 'virtualMachineConfiguration'.
- Os nós de piscina 'virtualMachineConfiguration' utilizam discos de OS geridos. O [tipo de disco gerido](../virtual-machines/disks-types.md) que é utilizado para cada nó depende do tamanho VM escolhido para a piscina. Se for especificado um tamanho VM 's' para a piscina, por exemplo , "Standard_D2s_v3", então é utilizado um SSD premium. Se for especificado um tamanho VM 'não-s', por exemplo , 'Standard_D2_v3', então é utilizado um HDD padrão.

   > [!IMPORTANT]
   > Tal como acontece com máquinas virtuais e conjuntos de balanças de máquinas virtuais, o disco gerido pelo SISTEMA utilizado para cada nó incorre num custo, que é adicional ao custo dos VMs. Não existe um custo de disco DE PARA os nós 'cloudServiceConfiguration', uma vez que o disco DE É criado nos nós SSD local.

## <a name="azure-data-factory-custom-activity-pools"></a>Piscinas de atividade personalizada azure Data Factory

As piscinas Azure Batch podem ser usadas para executar atividades personalizadas da Data Factory. Quaisquer piscinas de 'cloudServiceConfiguration' utilizadas para executar atividades personalizadas terão de ser eliminadas e novas piscinas 'virtualMachineConfiguration' criadas.

Ao criar as suas novas piscinas para executar atividades personalizadas da Data Factory, siga estas práticas:

- Faça uma pausa em todos os oleodutos antes de criar as novas piscinas e apagar as antigas para garantir que nenhuma execução será interrompida.
- O mesmo id de piscina pode ser usado para evitar alterações na configuração do serviço ligado.
- Retomar os oleodutos quando novas piscinas tiverem sido criadas.

Para obter mais informações sobre a utilização do Azure Batch para executar atividades personalizadas da Data Factory, consulte [o serviço ligado ao Azure Batch](../data-factory/compute-linked-services.md#azure-batch-linked-service) e as  [atividades personalizadas num pipeline da Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as configurações da piscina.](nodes-and-pools.md#configurations)
- Saiba mais sobre [as melhores práticas da piscina.](best-practices.md#pools)
- Consulte a referência REST API para [a adição](/rest/api/batchservice/pool/add) de piscina e [configuração virtual.](/rest/api/batchservice/pool/add#virtualmachineconfiguration)