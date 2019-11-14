---
title: Migrar do AWS e de outras plataformas para Managed Disks no Azure
description: Crie VMs no Azure usando VHDs carregados de outras nuvens, como AWS ou outras plataformas de virtualização, e aproveite o Azure Managed Disks.
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
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbce2969ccb508c2bf3ee33730d0b112caa45c9e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033052"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrar de Amazon Web Services (AWS) e outras plataformas para Managed Disks no Azure

Você pode carregar arquivos VHD de soluções de virtualização AWS ou local para o Azure para criar VMs que tiram proveito de Managed Disks. O Managed Disks do Azure elimina a necessidade de gerenciar contas de armazenamento para VMs IaaS do Azure. Você precisa especificar apenas o tipo (Premium ou Standard) e o tamanho do disco necessário, e o Azure cria e gerencia o disco para você. 

Você pode carregar VHDs generalizados e especializados. 
- **VHD generalizado** -teve todas as informações de sua conta pessoal removidas usando o Sysprep. 
- **VHD especializado** – mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. 

> [!IMPORTANT]
> Antes de carregar qualquer VHD no Azure, você deve seguir [preparar um VHD do Windows ou VHDX para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Cenário                                                                                                                         | Documentação                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Você tem instâncias AWS EC2 existentes que gostaria de migrar para VMs do Azure usando Managed disks                              | [Mover uma VM do Amazon Web Services (AWS) para o Azure](aws-to-azure.md)                           |
| Você tem uma VM de outra plataforma de virtualização que deseja usar como uma imagem para criar várias VMs do Azure. | [Carregar um VHD generalizado e usá-lo para criar uma nova VM no Azure](upload-generalized-managed.md) |
| Você tem uma VM exclusivamente personalizada que deseja recriar no Azure.                                                      | [Carregar um VHD especializado no Azure e criar uma nova VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Visão geral do Managed Disks

O Azure Managed Disks simplifica o gerenciamento de VM, eliminando a necessidade de gerenciar contas de armazenamento. Managed Disks também se beneficiam da melhor confiabilidade das VMs em um conjunto de disponibilidade. Ele garante que os discos de diferentes VMs em um conjunto de disponibilidade estejam suficientemente isolados entre si para evitar um ponto único de falha. Ele coloca automaticamente discos de diferentes VMs em um conjunto de disponibilidade em diferentes unidades de escala de armazenamento (carimbos), o que limita o impacto de falhas de unidade de escala de armazenamento único causadas devido a falhas de hardware e software.
Com base em suas necessidades, você pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte nosso artigo [selecionar um tipo de disco](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planejar a migração para Managed Disks

Esta seção ajuda você a tomar a melhor decisão sobre tipos de disco e VM.

Se você estiver planejando a migração de discos não gerenciados para o Managed disks, você deve estar ciente de que os usuários com a função [colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) não poderão alterar o tamanho da VM (pois eles poderiam ser previamente convertidos). Isso ocorre porque as VMs com discos gerenciados exigem que o usuário tenha a permissão Microsoft. Compute/disks/Write nos discos do sistema operacional.

### <a name="location"></a>Localização

Escolha um local onde o Azure Managed Disks está disponível. Se você estiver migrando para o Managed Disks Premium, verifique também se o armazenamento Premium está disponível na região em que você planeja migrar. Consulte [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre locais disponíveis.

### <a name="vm-sizes"></a>Tamanhos de VM

Se estiver migrando para o Premium Managed Disks, você precisará atualizar o tamanho da VM para o tamanho com capacidade de armazenamento Premium disponível na região em que a VM está localizada. Examine os tamanhos de VM que são capazes de armazenar o armazenamento Premium. As especificações de tamanho de VM do Azure são listadas em [tamanhos de máquinas virtuais](sizes.md).
Examine as características de desempenho de máquinas virtuais que funcionam com o armazenamento Premium e escolha o tamanho de VM mais apropriado que melhor atenda à sua carga de trabalho. Verifique se há largura de banda suficiente disponível em sua VM para direcionar o tráfego de disco.

### <a name="disk-sizes"></a>Tamanhos de disco

**Managed Disks Premium**

Há sete tipos de discos gerenciados Premium que podem ser usados com sua VM e cada um tem IOPs e limites de taxa de transferência específicos. Leve em consideração esses limites ao escolher o tipo de disco Premium para sua VM com base nas necessidades do seu aplicativo em termos de capacidade, desempenho, escalabilidade e picos de carga.

| Tipo de discos Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 125 MB por segundo |150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Managed Disks padrão**

Há sete tipos de Managed disks padrão que podem ser usados com sua VM. Cada uma delas tem capacidade diferente, mas tem os mesmos limites de IOPS e taxa de transferência. Escolha o tipo de Managed disks padrão com base nas necessidades de capacidade do seu aplicativo.

| Tipo de Disco Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPs por disco       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

### <a name="disk-caching-policy"></a>Política de cache de disco 

**Managed Disks Premium**

Por padrão, a política de cache de disco é *somente leitura* para todos os discos de dados Premium e *leitura/gravação* para o disco do sistema operacional Premium anexado à VM. Essa definição de configuração é recomendada para alcançar o desempenho ideal para o IOs do seu aplicativo. Para discos de dados de gravação intensa ou somente gravação (como SQL Server arquivos de log), desabilite o cache de disco para que você possa obter melhor desempenho do aplicativo.

### <a name="pricing"></a>Preços

Examine os [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). O preço do Managed Disks Premium é o mesmo que os discos não gerenciados Premium. Mas os preços para Managed Disks padrão são diferentes dos discos não gerenciados padrão.


## <a name="next-steps"></a>Passos Seguintes

- Antes de carregar qualquer VHD no Azure, você deve seguir [preparar um VHD do Windows ou VHDX para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
