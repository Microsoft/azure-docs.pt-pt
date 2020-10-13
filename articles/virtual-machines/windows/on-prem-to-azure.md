---
title: Migrar de AWS e outras plataformas para Discos Geridos em Azure
description: Crie VMs em Azure usando VHDs carregados de outras nuvens como AWS ou outras plataformas de virtualização e aproveite os Discos Geridos Azure.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f363b8fc2fec659ee1b101c13d1ba6ac629f29d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835494"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrar dos Serviços Web da Amazon (AWS) e de outras plataformas para Discos Geridos em Azure

Pode carregar ficheiros VHD a partir de soluções de virtualização AWS ou no local para a Azure para criar VMs que tirem partido de Discos Geridos. Azure Managed Disks remove a necessidade de gerir as contas de armazenamento de VMs Azure IaaS. Basta especificar o tipo (Premium ou Standard) e o tamanho do disco de que necessita, e o Azure cria e gere o disco para si. 

Pode carregar VHDs generalizados e especializados. 
- **Generalizado VHD** - removeu todas as informações da sua conta pessoal usando a Sysprep. 
- **Specialized VHD** - mantém as contas de utilizador, aplicações e outros dados estatais do seu VM original. 

> [!IMPORTANT]
> Antes de enviar qualquer VHD para Azure, deve seguir [Preparar um VHD ou VHDX do Windows para fazer o upload para Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Cenário                                                                                                                         | Documentação                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Tem instâncias AWS EC2 existentes que gostaria de migrar para VMs Azure usando discos geridos                              | [Mover um VM da Amazon Web Services (AWS) para Azure](aws-to-azure.md)                           |
| Tem um VM de outra plataforma de virtualização que gostaria de usar como imagem para criar vários VMs Azure. | [Faça upload de um VHD generalizado e use-o para criar um novo VM em Azure](upload-generalized-managed.md) |
| Você tem um VM personalizado único que você gostaria de recriar em Azure.                                                      | [Faça o upload de um VHD especializado para a Azure e crie um novo VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Visão geral dos discos geridos

A Azure Managed Disks simplifica a gestão de VM removendo a necessidade de gerir contas de armazenamento. Os Discos Geridos também beneficiam de uma melhor fiabilidade dos VMs num Conjunto de Disponibilidade. Garante que os discos de diferentes VMs num Conjunto de Disponibilidades estão suficientemente isolados uns dos outros para evitar um único ponto de falha. Coloca automaticamente discos de diferentes VMs num Conjunto de Disponibilidade em diferentes unidades de escala de armazenamento (selos) que limitam o impacto de falhas de unidade de escala de armazenamento única causadas por falhas de hardware e software.
Com base nas suas necessidades, pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte o nosso artigo [Selecione um tipo de disco](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Plano para a migração para Discos Geridos

Esta secção ajuda-o a tomar a melhor decisão sobre os tipos de VM e discos.

Se está a planear migrar de discos não geridos para discos geridos, deve estar ciente de que os utilizadores com a função [de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) não poderão alterar o tamanho do VM (como poderiam pré-conversão). Isto porque os VMs com discos geridos exigem que o utilizador tenha a permissão microsoft.Compute/disks/write nos discos DE.

### <a name="location"></a>Localização

Escolha um local onde os Discos Geridos Azure estão disponíveis. Se estiver a migrar para Discos Geridos Premium, certifique-se também de que o armazenamento Premium está disponível na região para onde planeia migrar. Consulte [os Serviços Azure por Região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre os locais disponíveis.

### <a name="vm-sizes"></a>Tamanhos de VM

Se estiver a migrar para Discos Geridos Premium, tem de atualizar o tamanho do tamanho do VM para o tamanho do Armazenamento Premium disponível na região onde o VM está localizado. Reveja os tamanhos VM que são capazes de armazenamento premium. As especificações do tamanho Azure VM estão listadas em [Tamanhos para máquinas virtuais](../sizes.md).
Reveja as características de desempenho das máquinas virtuais que funcionam com o Premium Storage e escolha o tamanho VM mais adequado que melhor se adapte à sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível no seu VM para conduzir o tráfego do disco.

### <a name="disk-sizes"></a>Tamanhos de disco

**Discos geridos premium**

Existem sete tipos de discos geridos premium que podem ser usados com o seu VM e cada um tem IOPs específicos e limites de produção. Tenha em consideração estes limites ao escolher o tipo de disco Premium para o seu VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e cargas máximas.

| Tipo de discos premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 125 MB por segundo |150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Discos geridos padrão**

Existem sete tipos de discos geridos padrão que podem ser usados com o seu VM. Cada um deles tem uma capacidade diferente, mas tem os mesmos IOPS e limites de produção. Escolha o tipo de discos Standard Managed com base nas necessidades de capacidade da sua aplicação.

| Tipo de Disco Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPs por disco       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

### <a name="disk-caching-policy"></a>Política de cache de disco 

**Discos geridos premium**

Por predefinição, a política de cache do disco é *Read-Only* para todos os discos de dados Premium e *Read-Write* para o disco do sistema operativo Premium ligado ao VM. Esta definição de configuração é recomendada para obter o melhor desempenho para os IOs da sua aplicação. Para discos de dados pesados ou apenas de escrita (tais como ficheiros de registo do SQL Server), desative o cache do disco para que possa obter um melhor desempenho da aplicação.

### <a name="pricing"></a>Preços

Reveja os [preços dos Discos Geridos.](https://azure.microsoft.com/pricing/details/managed-disks/) O preço dos Discos Geridos Premium é o mesmo que os Discos Premium Não Geridos. Mas os preços dos Discos Geridos Padrão são diferentes dos Discos Não Geridos Padrão.


## <a name="next-steps"></a>Passos Seguintes

- Antes de enviar qualquer VHD para Azure, deve seguir [Preparar um VHD ou VHDX do Windows para fazer o upload para Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)