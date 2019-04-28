---
title: Migrar do AWS e de outras plataformas para os Managed Disks no Azure | Documentos da Microsoft
description: Criar VMs no Azure com VHDs carregados a partir de outras clouds, como o AWS ou de outras plataformas de Virtualização e tire partido de Managed Disks do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42ad7bc10cb7b93bd4db9260f950ae4ca12aba44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126901"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrar a partir do Amazon Web Services (AWS) e outras plataformas para os Managed Disks no Azure

Pode carregar ficheiros VHD soluções de Virtualização do AWS ou no local ao Azure para criar VMs que tiram partido dos Managed Disks. Managed Disks do Azure remove a necessidade de gerir contas de armazenamento para VMs IaaS do Azure. Precisa apenas especificar o tipo (Premium ou Standard) e o tamanho do disco é necessário, e o Azure cria e gere o disco por si. 

Pode carregar VHDs generalizadas e especializadas. 
- **Um VHD generalizado** -teve todas as suas informações de conta pessoal removidas com o Sysprep. 
- **Especializada VHD** -mantém as contas de utilizador, aplicativos e outros dados de estado da original VM. 

> [!IMPORTANT]
> Antes de carregar qualquer VHD para o Azure, deve seguir [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Cenário                                                                                                                         | Documentação                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Tem instâncias do AWS EC2 existentes que gostaria que a migração para VMs do Azure com discos geridos                              | [Mover uma VM do Amazon Web Services (AWS) para o Azure](aws-to-azure.md)                           |
| Tem uma VM a partir de outra plataforma de Virtualização que pretende utilizar como uma imagem para criar várias VMs do Azure. | [Carregar um VHD generalizado e utilizá-lo para criar uma nova VM no Azure](upload-generalized-managed.md) |
| Tem uma VM personalizada de forma exclusiva que gostaria de recriar no Azure.                                                      | [Carregar um VHD especializado para o Azure e criar uma nova VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Descrição geral de discos geridos

Managed Disks do Azure simplifica o gerenciamento de VM ao remover a necessidade de gerir contas de armazenamento. Discos geridos também benefício de confiabilidade de VMs num conjunto de disponibilidade. Ele garante que os discos de VMs diferentes no conjunto de disponibilidade estão suficientemente isolados uns dos outros para evitar um ponto único de falha. Coloca automaticamente discos de VMs diferentes no conjunto de disponibilidade em unidades de escala de armazenamento diferentes (carimbos de data /) que limita o impacto das falhas de unidade de escala de armazenamento únicas provocadas por hardware e de falhas de software.
Com base nas suas necessidades, pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponível, consulte nosso artigo [selecionar um tipo de disco](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planear a migração para os Managed Disks

Esta secção ajuda-o a tomar a melhor decisão em tipos VM e disco.

Se estiver a planear a migração de discos não geridos para discos geridos, deve estar ciente que os utilizadores com o [contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) função não será possível alterar o tamanho da VM (como pré-conversão de). Isto acontece porque as VMs com discos geridos pedir ao utilizador que tem a permissão de Microsoft.Compute/disks/write nos discos de SO.

### <a name="location"></a>Location

Escolha uma localização onde o Managed Disks do Azure estão disponíveis. Se estiver a migrar para Premium Managed Disks, certifique-se também que o armazenamento Premium está disponível na região em que estiver a planear migrar para o. Ver [serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre localizações disponíveis.

### <a name="vm-sizes"></a>Tamanhos de VM

Se estiver a migrar para Premium Managed Disks, terá de atualizar o tamanho da VM para o armazenamento Premium com capacidade de tamanho disponível na região onde está localizada a VM. Reveja os tamanhos VM que são compatíveis com o armazenamento Premium. As especificações de tamanho de VM do Azure estão listadas na [tamanhos de máquinas virtuais](sizes.md).
Reveja as características de desempenho de máquinas virtuais que funcionam com o armazenamento Premium e escolha o tamanho VM mais adequado que melhor se adequa aos sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco.

### <a name="disk-sizes"></a>Tamanhos de disco

**Premium Managed Disks**

Existem sete tipos de discos geridos premium, que podem ser utilizados com a VM e cada uma tem específicos IOPs e débito limites. Leve em consideração estes limites ao escolher o tipo de disco Premium para a sua VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e cargas de pico.

| Tipo de discos Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 125 MB por segundo |150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Discos geridos Standard**

Existem sete tipos de discos geridos padrão que podem ser utilizados com a VM. Cada um deles tem capacidade diferente, mas têm a mesma IOPS e limites de débito. Escolha o tipo de discos geridos Standard com base nas necessidades de capacidade do seu aplicativo.

| Tipo de Disco Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPs por disco       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

### <a name="disk-caching-policy"></a>Política de colocação em cache do disco 

**Premium Managed Disks**

Por predefinição, é a política de colocação em cache *só de leitura* em todos os discos de dados de Premium, e *leitura-escrita* para o disco de sistema operativo Premium ligados à VM. Esta definição de configuração é recomendada para alcançar o desempenho ideal para a IOs sua aplicação. Para discos de dados de escrita intensiva ou só de escrita (por exemplo, ficheiros de registo do SQL Server), desative o cache em disco para que pode obter um melhor desempenho do aplicativo.

### <a name="pricing"></a>Preços

Reveja os [os preços dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/). Os preços dos Premium Managed Disks são mesmo que os discos não geridos Premium. Mas os preços dos discos geridos Standard são diferente de discos não geridos Standard.


## <a name="next-steps"></a>Próximos Passos

- Antes de carregar qualquer VHD para o Azure, deve seguir [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
