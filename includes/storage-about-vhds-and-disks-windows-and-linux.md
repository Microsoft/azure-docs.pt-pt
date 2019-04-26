---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa740cfb203f50dc97a06359774dae367a20252b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406498"
---
## <a name="about-vhds"></a>Sobre VHDs

Os VHDs utilizados no Azure são ficheiros .vhd armazenados como blobs de páginas numa conta de armazenamento standard ou premium no Azure. Para obter detalhes sobre blobs de páginas, consulte [Understanding block blobs and page blobs (Noções sobre blobs de blocos e blobs de páginas)](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Para obter detalhes sobre o armazenamento premium, consulte [High-performance premium storage and Azure VMs (VMs do Azure e armazenamento premium de elevado desempenho)](../articles/virtual-machines/windows/premium-storage.md).

O Azure suporta o formato VHD de disco fixo. O formato fixo dispõe do disco lógico de forma linear no ficheiro, para que o deslocamento do disco X seja armazenado no deslocamento do blob X. Um rodapé pequeno no final do blob descreve as propriedades do VHD. Muitas vezes, a formato fixo desperdiça espaço porque a maioria dos discos tem grandes intervalos não utilizados nos mesmos. No entanto, o Azure armazena ficheiros .vhd num formato disperso, para receber os benefícios dos discos fixos e dinâmicos ao mesmo tempo. Para obter mais informações, consulte [guia de introdução discos rígidos virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os ficheiros VHD no Azure que pretende utilizar como uma origem para criar discos ou imagens são só de leitura, exceto os ficheiros. vhd carregado ou copiados para o armazenamento do Azure pelo utilizador (o que pode ser de leitura-escrita ou só de leitura). Quando cria um disco ou imagem, o Azure faz cópias da origem de ficheiros. vhd. Estas cópias podem ser só de leitura ou de leitura e escrita, dependendo de como utiliza o VHD.

Quando cria uma máquina virtual a partir de uma imagem, o Azure cria um disco para a máquina virtual que é uma cópia do ficheiro .vhd de origem. Para proteger contra a eliminação acidental, o Azure coloca uma concessão em qualquer ficheiro .vhd de origem que é utilizado para criar uma imagem, um disco do sistema operativo ou um disco de dados.

Antes de poder eliminar um ficheiro .vhd de origem, terá de remover a concessão, ao eliminar o disco ou a imagem. Para eliminar um ficheiro .vhd que esteja a ser utilizado por uma máquina virtual como um disco do sistema operativo, pode eliminar a máquina virtual, o disco do sistema operativo e o ficheiro .vhd de origem em simultâneo, ao eliminar a máquina virtual e todos os discos associados. No entanto, a eliminação de um ficheiro .vhd que é uma origem para um disco exige vários passos numa determinada ordem. Primeiro desanexe o disco da máquina virtual e, em seguida, elimine o disco e depois o ficheiro .vhd.

> [!WARNING]
> Se eliminar um ficheiro .vhd de origem do armazenamento ou eliminar a sua conta de armazenamento, a Microsoft não pode recuperar os dados por si.

## <a name="types-of-disks"></a>Tipos de discos

Os Discos do Azure foram concebidos para garantir uma disponibilidade de 99,999%. Discos do Azure consistentemente propiciaram durabilidade de nível empresarial, com um líder do setor ZERO % Annualized taxa de falhas.

Existem três escalões de desempenho para o armazenamento que pode escolher ao criar os discos - discos de SSD Premium, o padrão de SSD e o armazenamento de HDD padrão. Além disso, existem dois tipos de discos - geridos e não geridos.

### <a name="standard-hdd-disks"></a>Discos Standard HDD

Os discos HDD Standard estão protegidos por HDDs e fornecem armazenamento económico. Armazenamento HDD Standard pode ser replicado localmente num único datacenter ou ser georredundante com datacenters primários e secundários. Para obter mais informações sobre a replicação de armazenamento, consulte [replicação de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Para obter mais informações sobre a utilização de discos Standard HDD, consulte [armazenamento Standard e discos](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Discos SSD Standard

Os discos Standard SSD foram concebidos para resolver o mesmo tipo de cargas de trabalho como discos Standard HDD, mas oferecem mais consistente desempenho e fiabilidade do que HDD. Os discos Standard SSD combinam elementos de discos Premium SSD e de discos Standard HDD para formar uma solução económica melhor adequados para aplicativos, como servidores web que não é necessário o IOPS elevado em discos. Se estiver disponível, os discos de Standard SSD são a opção de implementação recomendada para a maioria das cargas de trabalho. Os discos SSD Standard estão disponíveis como Managed Disks em todas as regiões, mas atualmente só estão disponíveis com o tipo de resiliência de armazenamento localmente redundante (LRS).

### <a name="premium-ssd-disks"></a>Discos SSD Premium

Os discos SSD Premium são apoiados por SSDs e fornece suporte de discos de elevado desempenho e de baixa latência para VMs com cargas de trabalho de e/S intensivas. Normalmente, pode utilizar discos Premium SSD com tamanhos que incluem um "s" no nome da série. Por exemplo, existe a série Dv3 e a série Dsv3, a série Dsv3 podem ser utilizadas com discos de Premium SSD.  Para mais informações, consulte [Premium Storage (Armazenamento Premium)](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Discos não geridos

Os discos não geridos são o tipo de discos tradicionais que foram utilizados por VMs. Com estes discos, crie a sua própria conta de armazenamento e especificar essa conta de armazenamento ao criar o disco. Certifique-se de que não inclui demasiados discos na mesma conta de armazenamento, porque poderá exceder os [destinos de escalabilidade](../articles/storage/common/storage-scalability-targets.md) da conta de armazenamento (20.000 IOPS, por exemplo), resultando na limitação das VMs. Com discos não geridos, deve maximizar a utilização de uma ou mais contas de armazenamento para obter o melhor desempenho das suas VMs.

### <a name="managed-disks"></a>Managed disks

Os Managed Disks suportam a criação/gestão da conta de armazenamento em segundo plano para si, e asseguram que não tem de se preocupar com os limites de escalabilidade da conta de armazenamento. Basta especificar o tamanho do disco e o escalão de desempenho (Standard/Premium), e o Azure cria e gere o disco por si. Conforme adiciona discos ou aumente e reduza verticalmente a VM, não terá de se preocupar se o armazenamento está a ser utilizado.

Também pode gerir as imagens personalizadas numa conta de armazenamento por região do Azure e utilizá-las para criar centenas de VMs na mesma subscrição. Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../articles/virtual-machines/windows/managed-disks-overview.md).

Recomendamos que utilize os Managed Disks do Azure para novas VMs e que converta os discos não geridos anteriores para discos geridos, para tirar partido das diversas funcionalidades disponíveis nos Managed Disks.

### <a name="disk-comparison"></a>Comparação de discos

A tabela seguinte fornece uma comparação de Standard HDD, Standard SSD e Premium SSD para discos geridos e não para o ajudar a decidir o que utilizar. Tamanhos marcados com um asterisco estão atualmente em pré-visualização.

|    | Disco Premium do Azure |Azure Standard SSD Disk | Disco HDD padrão do Azure
|--- | ------------------ | ------------------------------- | -----------------------
| Tipo de Disco | Unidades de Estado Sólido (SSD) | Unidades de Estado Sólido (SSD) | Unidades de Disco Rígido (HDD)  
| Descrição geral  | Suporte de disco de baixa latência, alto desempenho baseado em SSD para VMs com cargas de trabalho intensivas em E/S ou ambiente de produção crítico de missão de alojamento |Desempenho e fiabilidade do que HDD mais consistente. Otimizado para cargas de trabalho de IOPS de baixo| Disco económico baseado em HDD para acesso pouco frequente
| Cenário  | Cargas de trabalho confidenciais de produção e de desempenho |Servidores Web, aplicações empresariais pouco usada e Dev/Test| Cópia de segurança, não críticas, acesso pouco frequente
| Tamanho do Disco | P4: 32 giB (apenas os discos geridos)<br>P6: 64 giB (apenas os discos geridos)<br>P10: 128 GiB<br>P15: 256gib (apenas os discos geridos)<br>P20: 512 GiB<br>P30: 1024 GiB<br>P40: 2048 GiB<br>P50: 4095 giB<br>P60: GiB 8.192 * (8 TiB)<br>P70: GiB 16.384 * (TiB de 16)<br>P80: GiB 32.767 * (32 TiB) |Discos geridos apenas:<br>E4: 32 GiB<br>E6: 64 GiB<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB<br>E60: GiB 8.192 * (8 TiB)<br>E70: GiB 16.384 * (TiB de 16)<br> E80: GiB 32.767 * (32 TiB) | Discos não geridos: 1 GiB – 4 TiB (4095 GiB) <br><br>Managed Disks:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB<br>S60: GiB 8.192 * (8 TiB)<br>S70: GiB 16.384 * (TiB de 16)<br>S80: GiB 32.767 * (32 TiB)
| Débito Máx por Disco | P4: 25 MiB/s<br> P6: 50 MiB/s<br> P10: 100 MiB/s<br> P15: 125 MiB/s<br> P20: 150 MiB/s<br> P30: 200 MiB/s<br> P40-P50: 250 MiB/s<br> P60: 480 MiB/s *<br> P70-P80: 750 MiB/s * | E10-E50: Até 60 MiB/s<br> E60: Até 300 MiB/s *<br> E70-E80: 500 MiB/s *| S4 - S50: Upt s 60 MiB/s<br> S60: Até 300 MiB/s *<br> S70-S80: Até 500 MiB/s *
| IOPs Máx por disco | P4: 120 IOPS<br> P6: 240 IOPS<br> P10: 500 IOPS<br> P15: 1100 IOPS<br> P20: 2300 IOPS<br> P30: 5000 IOPS<br> P40-P50: 7500 IOPS<br> P60: 12,500 IOPS *<br> P70: 15,000 IOPS *<br> P80: 20,000 IOPS * | E10-E50: Até 500 IOPS<br> E60: Até o IOPS de 1300 *<br> E70-E80: Até 2000 IOPS * | S4-S50: Até 500 IOPS<br> S60: Até o IOPS de 1300 *<br> S70-S80: Até 2000 IOPS *
