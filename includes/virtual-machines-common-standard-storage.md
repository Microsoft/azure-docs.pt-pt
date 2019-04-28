---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 01/08/2019
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: ad57d373422e0fc310e51ac31f2a2e76999abf22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127162"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Armazenamento Standard económico e discos de VM do Azure geridos e não

Armazenamento Standard do Azure fornece suporte de discos de fiável de baixo custo para VMs com cargas de trabalho insensível à latência. Também suporta blobs, tabelas, filas e ficheiros. Com o armazenamento Standard, os dados são armazenados em unidades de disco rígido (HDDs). Ao trabalhar com VMs, pode utilizar os discos standard SSD e HDD para cenários de Dev/Test e menos cargas de trabalho críticas e discos SSD premium para aplicações de produção de missão crítica. Armazenamento Standard está disponível em todas as regiões do Azure. 

Este artigo se concentra no uso de discos standard de SSD e HDD. Para obter mais informações sobre a utilização de armazenamento com blobs, tabelas, filas e ficheiros, consulte [introdução ao armazenamento](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Tipos de disco

Existem duas formas de criar os discos standard para VMs do Azure:

**Discos não geridos**: Este tipo de disco é o método original onde pode gerir as contas de armazenamento utilizadas para armazenar os ficheiros VHD que correspondem aos discos das VMS. Ficheiros VHD são armazenados como blobs de páginas em contas de armazenamento. Discos não geridos podem ser anexados a qualquer tamanho de VM do Azure, incluindo as VMs que utilizam principalmente o armazenamento Premium, como a série DSv2 e GS. VMs do Azure suportam anexar vários discos padrão, permitindo que o máximo de 256 TiB de armazenamento por VM. Se utilizar os tamanhos de disco de pré-visualização, pode ter até PiB cerca de 2 de armazenamento por VM.

[**Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): Esta funcionalidade gerencia as contas de armazenamento utilizadas para os discos VM para. Especifique o tipo (Premium SSD, padrão de SSD ou HDD padrão) e o tamanho do disco é necessário, e o Azure cria e gere o disco por si. Não precisa se preocupar sobre os discos são colocados em várias contas de armazenamento para garantir que permaneça dentro dos limites de escalabilidade para as contas de armazenamento – Azure processa isto por si.

Embora ambos os tipos de discos estão disponíveis, recomendamos que utilize o Managed Disks para tirar partido dos seus muitos recursos.

Para começar a utilizar com o armazenamento padrão do Azure, visite [começar gratuitamente](https://azure.microsoft.com/pricing/free-trial/). 

Para obter informações sobre como criar uma VM com Managed Disks, consulte um dos seguintes artigos.

* [Criar uma VM com o Resource Manager e o PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Criar uma VM do Linux através da CLI do Azure](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Recursos de armazenamento Standard

Vamos dar uma olhada em alguns dos recursos de armazenamento Standard. Para obter mais detalhes, veja [introdução ao armazenamento do Azure](../articles/storage/common/storage-introduction.md).

**Armazenamento Standard**: Armazenamento Standard do Azure suporta discos do Azure, Blobs do Azure, ficheiros do Azure, as tabelas do Azure e filas do Azure. Para utilizar os serviços de armazenamento Standard, comece com [criar uma conta de armazenamento do Azure](../articles/storage/common/storage-quickstart-create-account.md).

**Discos SSD Standard:** Os discos Standard SSD apresentam um desempenho mais fiável do que os discos Standard HDD e estão atualmente disponíveis. Para obter mais informações sobre a disponibilidade de região de discos de Standard SSD, veja [disponibilidade das regiões de discos Standard SSD](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**Discos standard de HDD:** Podem ser anexados os discos standard de HDD para todas as VMs do Azure, incluindo VMs de série de tamanho utilizadas com o armazenamento Premium, como a série DSv2 e GS. Um disco de Standard HDD só pode ser anexado a uma VM. No entanto, pode anexar um ou mais destes discos para uma VM, até a contagem de disco máximo definida para esse tamanho VM. Na seção a seguir em metas de desempenho e escalabilidade do Storage Standard, descrevemos as especificações em mais detalhes.

**BLOBs de páginas Standard**: Blobs de páginas Standard são utilizados para armazenar discos persistentes para VMs e também podem ser acedidos diretamente através do REST, como outros tipos de Blobs do Azure. [Blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) são uma coleção de páginas de 512 bytes otimizados para operações de escrita de leitura aleatória e. 

**Replicação de armazenamento:** Na maioria das regiões, dados de uma conta de armazenamento standard podem ser replicado localmente ou georreplicado centros de dados. Os quatro tipos de replicação disponível são armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS), o armazenamento Georredundante (GRS) e o armazenamento Georredundante com acesso de leitura (RA-GRS). Discos geridos no armazenamento Standard suportam atualmente armazenamento localmente redundante (LRS) apenas. Para obter mais informações, consulte [replicação de armazenamento](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Metas de Escalabilidade e Desempenho

Nesta seção, descrevemos os destinos de escalabilidade e desempenho, que precisa considerar ao utilizar o armazenamento standard.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limites de contas – não se aplica aos discos geridos

| **Recurso** | **Limite Predefinido** |
|--------------|-------------------|
| TB por conta de armazenamento  | 500 TB |
| Entrada de Max<sup>1</sup> por conta de armazenamento (regiões E.u.a.) | 10 Gbps, se estiver ativada o GRS/ZRS, 20 Gbps para LRS |
| Saída de Max<sup>1</sup> por conta de armazenamento (regiões E.u.a.) | 20 Gbps se ativada de RA-GRS/GRS/ZRS, 30 Gbps para LRS |
| Entrada de Max<sup>1</sup> por conta de armazenamento (Europeu e asiático regiões) | 5 Gbps, se estiver ativada o GRS/ZRS, 10 Gbps para LRS |
| Saída de Max<sup>1</sup> por conta de armazenamento (Europeu e asiático regiões) | 10 Gbps, se estiver ativada de RA-GRS/GRS/ZRS, 15 Gbps para LRS |
| Total taxa de pedidos (supondo que o tamanho do objeto de 1 KB) por conta de armazenamento | Até 20 000 IOPS, entidades por segundo ou mensagens por segundo |

<sup>1</sup> entrada refere-se a todos os dados (pedidos) a ser enviados para uma conta de armazenamento. Saída refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.

Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../articles/storage/common/storage-scalability-targets.md).

Se as necessidades da sua aplicação excederem os destinos de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para utilizar várias contas de armazenamento e a partição dos seus dados entre essas contas de armazenamento. Em alternativa, pode Managed Disks do Azure e o Azure gere a criação de partições e o posicionamento dos seus dados para.

### <a name="standard-disks-limits"></a>Limites de discos Standard

Ao contrário dos discos Premium, as operações de entrada/saída por segundo (IOPS) e o débito (largura de banda) de discos Standard não são aprovisionadas. O desempenho dos discos standard varia de acordo com o tamanho da VM para que o disco está anexado e o tamanho do disco.

Se a sua carga de trabalho requer o suporte de disco de elevado desempenho e de baixa latência, considere utilizar o armazenamento Premium. Para saber mais benefícios do armazenamento Premium, visite [High-Performance Premium Storage e discos de VM do Azure](../articles/virtual-machines/windows/premium-storage.md).

## <a name="snapshots-and-copy-blob"></a>Os instantâneos e blob de cópia

Para o serviço de armazenamento, o ficheiro VHD é um blob de página. Pode criar instantâneos de blobs de páginas e copiá-los para outro local, como uma conta de armazenamento diferente.

### <a name="unmanaged-disks"></a>Discos não geridos

Pode criar [instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md) para discos standard não geridos da mesma forma utilizar instantâneos de armazenamento Standard. Recomendamos que criar instantâneos e, em seguida, copie os instantâneos para uma conta de armazenamento standard com redundância geográfica se o disco de origem está numa conta de armazenamento localmente redundante. Para obter mais informações, consulte [opções de redundância de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Se um disco está ligado a uma VM, determinadas operações de API não são permitidas nos discos. Por exemplo, não é possível efetuar uma [Blob de cópia](/rest/api/storageservices/Copy-Blob) operação desse blob, desde que o disco está ligado a uma VM. Em vez disso, primeiro, crie um instantâneo desse blob usando o [Blob de instantâneo](/rest/api/storageservices/Snapshot-Blob) método API de REST e, em seguida, execute o [Blob de cópia](/rest/api/storageservices/Copy-Blob) do instantâneo para copiar o disco anexado. Em alternativa, pode desligar o disco e, em seguida, efetuar quaisquer operações necessárias.

Para manter cópias georredundantes de sua instantâneos, pode copiar instantâneos de uma conta de armazenamento localmente redundante para uma conta de armazenamento standard com redundância geográfica com o AzCopy ou Blob de cópia. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](../articles/storage/common/storage-use-azcopy.md) e [Blob de cópia](/rest/api/storageservices/Copy-Blob).

Para obter informações detalhadas sobre como realizar operações de REST blobs de páginas em contas de armazenamento standard, veja [API de REST de serviços de armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Managed disks

Um instantâneo para um disco gerido é uma cópia só de leitura do disco gerido, o que é armazenado como um disco gerido standard. Instantâneos incrementais não são atualmente suportados para o Managed Disks, mas serão suportados no futuro.

Se um disco gerido está ligado a uma VM, determinadas operações de API não são permitidas nos discos. Por exemplo, não é possível gerar uma assinatura de acesso partilhado (SAS) para efetuar uma operação de cópia, enquanto o disco estiver ligado a uma VM. Em vez disso, crie primeiro um instantâneo do disco e, em seguida, efetuar a cópia do instantâneo. Em alternativa, pode desligar o disco e, em seguida, gerar uma assinatura de acesso partilhado (SAS) para executar a operação de cópia.

## <a name="pricing-and-billing"></a>Preços e Faturação

Ao utilizar o armazenamento Standard, aplicam-se as seguintes considerações de faturas:

* Tamanho de discos/dados de armazenamento Standard não gerido
* Discos geridos Standard
* Instantâneos de armazenamento Standard
* Transferências de dados de saída
* Transações

**Tamanho de dados e o disco de armazenamento não geridos:** Para discos não geridos e outros dados (blobs, tabelas, filas e ficheiros), é cobrado apenas para a quantidade de espaço que está a utilizar. Por exemplo, se tiver uma VM cuja BLOBs de páginas é aprovisionada como 127 GB, mas a VM é realmente apenas através de 10 GB de espaço, serão cobrados os 10 GB de espaço. Damos suporte a armazenamento Standard até 8191 GB e Standard não geridos discos até 4095 GB. 

**Discos geridos:** Faturação para discos geridos standard depende do tamanho aprovisionado do disco. Azure mapeia o tamanho aprovisionado (arredondado) para a opção de Managed Disks mais próxima, conforme especificado nas tabelas abaixo. Cada disco gerido é mapeado para um dos tamanhos suportados aprovisionados e é cobrado em conformidade. Por exemplo, se criar um disco gerido standard e especificar um tamanho aprovisionado de 200 GiB, é-lhe cobrada de acordo com os preços do tipo de disco S15.

Tamanhos marcados com um asterisco estão atualmente em pré-visualização.

| **HDD padrão gerido <br>tipo de disco** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60*** | **S70*** | **S80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Tamanho do Disco        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1.024 GiB (1 TiB) | 2.048 GiB (2 TiB) | 4.095 GiB (4 TiB) | 8.192 GiB (8 TiB) | 16,385 giB (TiB de 16) | 32.767 giB (32 TiB) |


**Instantâneos**: Os instantâneos de discos standard são faturados para uma maior capacidade utilizada pelos instantâneos. Para obter informações sobre instantâneos, consulte [criar um instantâneo de um Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Transferências de dados de saída**: [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) faturação para utilização de largura de banda de incorrer em (dados que saem de datacenters do Azure).

**Transação**: Azure cobra $0.0036 por 100.000 transações de armazenamento standard. As transações incluem as opções de leitura e escrita para armazenamento.

Para obter informações detalhadas sobre os preços de armazenamento Standard, as máquinas virtuais e Managed Disks, consulte:

* [Preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Suporte de serviço de cópia de segurança do Azure

Máquinas virtuais com discos não geridos pode ser feitas com o Azure Backup. [Obter mais detalhes](../articles/backup/backup-azure-vms-first-look-arm.md).

Também pode utilizar o serviço de cópia de segurança do Azure com os Managed Disks para criar uma tarefa de cópia de segurança com cópias de segurança baseados no tempo, fácil restauro de VM e políticas de retenção de cópia de segurança. Pode ler mais sobre isso em [serviço através de cópia de segurança do Azure para VMs com discos geridos](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Armazenamento do Azure](../articles/storage/common/storage-introduction.md)

* [Criar uma conta de armazenamento](../articles/storage/common/storage-quickstart-create-account.md)

* [Descrição Geral do Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Criar uma VM com o Resource Manager e o PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Criar uma VM do Linux através da CLI do Azure](../articles/virtual-machines/linux/quick-create-cli.md)
