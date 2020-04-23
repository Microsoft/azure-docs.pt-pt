---
title: Migrar da AWS e de outras plataformas para discos geridos em Azure
description: Crie VMs em Azure usando VHDs carregados de outras nuvens como AWS ou outras plataformas de virtualização e aproveite os Discos Geridos azure.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc283d1845926b79fb541d8ccb011fe853f50484
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870288"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrar de Amazon Web Services (AWS) e outras plataformas para Discos Geridos em Azure

Pode enviar ficheiros VHD a partir de soluções de virtualização AWS ou no local para o Azure para criar VMs que tirem partido dos Discos Geridos. Os Discos Geridos azure remove a necessidade de gerir as contas de armazenamento para VMs Azure IaaS. Só tem de especificar o tipo (Premium ou Standard) e o tamanho do disco que necessita, e o Azure cria e gere o disco para si. 

Pode fazer o upload de VHDs generalizados e especializados. 
- **Generalizado VHD** - teve todas as suas informações pessoais de conta removidas usando sysprep. 
- **VHD especializado** - mantém as contas, aplicações e outros dados do estado do seu VM original. 

> [!IMPORTANT]
> Antes de enviar qualquer VHD para Azure, deve seguir [Prepare um Windows VHD ou VHDX para fazer o upload para O Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Cenário                                                                                                                         | Documentação                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Tem instâncias AWS EC2 existentes que gostaria de migrar para VMs Azure usando discos geridos                              | [Mova um VM da Amazon Web Services (AWS) para O Azure](aws-to-azure.md)                           |
| Você tem um VM de outra plataforma de virtualização que você gostaria de usar como uma imagem para criar vários VMs Azure. | [Faça upload de um VHD generalizado e use-o para criar um novo VM em Azure](upload-generalized-managed.md) |
| Você tem um VM personalizado exclusivo que você gostaria de recriar em Azure.                                                      | [Faça upload de um VHD especializado para o Azure e crie um novo VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Visão geral dos discos geridos

A Azure Managed Disks simplifica a gestão da VM removendo a necessidade de gerir as contas de armazenamento. Os Discos Geridos também beneficiam de uma melhor fiabilidade dos VMs num Conjunto de Disponibilidade. Garante que os discos de diferentes VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros para evitar um único ponto de falha. Coloca automaticamente discos de diferentes VMs num Conjunto de Disponibilidade em diferentes unidades de escala de armazenamento (selos) que limita o impacto das falhas da unidade de armazenamento único causadas devido a falhas de hardware e software.
Com base nas suas necessidades, pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte o nosso artigo [Selecione um tipo](disks-types.md)de disco .

## <a name="plan-for-the-migration-to-managed-disks"></a>Plano para a migração para Discos Geridos

Esta secção ajuda-o a tomar a melhor decisão sobre os tipos de VM e disco.

Se está a planear migrar de discos não geridos para discos geridos, deve estar ciente de que os utilizadores com a função de Colaborador de [Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) não serão capazes de alterar o tamanho do VM (uma vez que poderiam pré-conversão). Isto porque os VMs com discos geridos exigem que o utilizador tenha a permissão Microsoft.Compute/disks/write nos discos OS.

### <a name="location"></a>Localização

Escolha um local onde estejam disponíveis discos geridos pelo Azure. Se estiver a migrar para Discos Premium Geridos, certifique-se também de que o armazenamento Premium está disponível na região para onde planeia migrar. Consulte os [Serviços Azure por Região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre os locais disponíveis.

### <a name="vm-sizes"></a>Tamanhos de VM

Se estiver a migrar para Discos Premium Geridos, tem de atualizar o tamanho do VM para o tamanho premium de armazenamento disponível na região onde se encontra o VM. Reveja os tamanhos vm que são premium armazenamento capazes. As especificações do tamanho Do VM Azure estão listadas em [tamanhos para máquinas virtuais.](sizes.md)
Reveja as características de desempenho das máquinas virtuais que funcionam com o Armazenamento Premium e escolha o tamanho VM mais adequado que melhor se adapte à sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível no seu VM para conduzir o tráfego do disco.

### <a name="disk-sizes"></a>Tamanhos de disco

**Discos geridos premium**

Existem sete tipos de discos geridos premium que podem ser usados com o seu VM e cada um tem IOPs específicos e limites de entrada. Tenha em conta estes limites ao escolher o tipo de disco Premium para o seu VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e pico de cargas máximas.

| Tipo de discos premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 125 MB por segundo |150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Discos geridos padrão**

Existem sete tipos de discos geridos padrão que podem ser usados com o seu VM. Cada um deles tem capacidades diferentes, mas têm os mesmos limites de IOPS e de entrada. Escolha o tipo de discos Standard Managed com base nas necessidades de capacidade da sua aplicação.

| Tipo de Disco Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPs por disco       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

### <a name="disk-caching-policy"></a>Política de cache de disco 

**Discos geridos premium**

Por predefinição, a política de cache de disco é *Read-Only* para todos os discos de dados Premium e *Read-Write* para o disco do sistema operativo Premium ligado ao VM. Esta configuração de configuração é recomendada para obter o desempenho ideal para os IOs da sua aplicação. Para discos de dados de escrita pesadas ou escritas (como ficheiros de registo do SQL Server), desative o cache do disco para que possa obter um melhor desempenho de aplicação.

### <a name="pricing"></a>Preços

Reveja os [preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/). O preço dos Discos Premium Geridos é o mesmo que os Discos Premium Não Geridos. Mas os preços dos Discos Geridos Standard são diferentes dos Discos Não Geridos Standard.


## <a name="next-steps"></a>Passos Seguintes

- Antes de enviar qualquer VHD para Azure, deve seguir [Prepare um Windows VHD ou VHDX para fazer o upload para O Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
