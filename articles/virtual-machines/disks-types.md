---
title: Selecione um tipo de disco para Azure IaaS VMs - discos geridos
description: Saiba mais sobre os tipos de discos Azure disponíveis para máquinas virtuais, incluindo discos ultra, SSDs premium, SSDs padrão e HDDs standard.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ef481b73b6dc42bc35252c08ae8d63b9de95b2ba
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325099"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quais são os tipos de disco disponíveis no Azure?

A azure gere discos atualmente oferece quatro tipos de disco, cada tipo é direcionado para cenários específicos do cliente.

## <a name="disk-comparison"></a>Comparação de discos

A tabela seguinte fornece uma comparação de discos ultra, unidades de estado sólido premium (SSD), SSD padrão e drives de disco rígido padrão (HDD) para discos geridos para ajudá-lo a decidir o que usar.

| Detalhe | Disco Ultra | SSD Premium | SSD Standard | HDD Standard |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho intensivas em IO, tais como [SAP HANA,](workloads/sap/hana-vm-operations-storage.md)bases de dados de nível superior (por exemplo, SQL, Oráculo) e outras cargas de trabalho pesadas de transações.   |Cargas de trabalho confidenciais de produção e de desempenho   |Servidores Web, aplicações empresariais pouco utilizadas e dev/test   |Cópia de segurança, acesso pouco frequente e não crítico   |
|Tamanho máximo do disco   |65,536 gibibyte (GiB)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|Débito máximo   |2.000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|IOPS Máximo   |160 000    |20 000   |6000   |2.000   |

## <a name="ultra-disk"></a>Disco Ultra

Os discos Ultra do Azure têm um débito elevado, IOPS elevado e armazenamento no disco com latência baixa consistente para VMs IaaS do Azure. Alguns benefícios adicionais dos discos ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com as suas cargas de trabalho, sem a necessidade de reiniciar as suas máquinas virtuais (VM). Os discos Ultra são indicados para cargas de trabalho com utilização intensa de dados, como o SAP HANA, base de dados de escalão superior e cargas de trabalho com bastantes transações. Os discos Ultra só podem ser utilizados como discos de dados. Recomendamos a utilização de discos SSD premium como discos de SO.

### <a name="performance"></a>Desempenho

Quando forja um disco ultra, pode configurar independentemente a capacidade e o desempenho do disco. Os discos ultra vêm em vários tamanhos fixos, que vão de 4 GiB a 64 TiB, e apresentam um modelo de configuração de desempenho flexível que lhe permite configurar independentemente iOPS e produção.

Algumas capacidades-chave dos discos ultra são:

- Capacidade do disco: A capacidade dos discos ultra varia entre 4 GiB e 64 TiB.
- Discos IOPS: Os discos ultra suportam limites IOPS de 300 IOPS/GiB, até um máximo de 160 K IOPS por disco. Para alcançar o IOPS que a provisionou, certifique-se de que o IOPS de disco selecionado é inferior ao limite de VM IOPS. O IOPS mínimo garantido por disco é de 2 IOPS/GiB, com um mínimo de base global de 100 IOPS. Por exemplo, se tiver um disco ultra 4 GiB, terá um mínimo de 100 IOPS, em vez de oito IOPS.
- Produção de disco: Com discos ultra, o limite de produção de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco (onde MBps = 10^6 Bytes por segundo). A produção mínima garantida por disco é de 4KiB/s para cada IOPS provisível, com um mínimo de base global de 1 MBps.
- Os discos ultra suportam ajustar os atributos de desempenho do disco (IOPS e produção) em tempo de execução sem separar o disco da máquina virtual. Uma vez emitida uma operação de redimensionação de desempenho do disco num disco, pode levar até uma hora para que a alteração produza efetivamente efeitos. Há um limite de quatro operações de redimensionar o desempenho durante uma janela de 24 horas. É possível que uma operação de redimensionação de desempenho falhe devido à falta de capacidade de largura de banda de desempenho.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do disco (GiB)  |Tampa do IOPS  |Tampa de produção (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2,400         |600         |
|16     |4800         |1200         |
|32     |9600         |2.000         |
|64     |19,200         |2.000         |
|128     |38,400         |2.000         |
|256     |76 800         |2.000         |
|512     |153,600         |2.000         |
|1.024-65.536 (tamanhos nesta gama aumentando em incrementos de 1 TiB)     |160 000         |2.000         |

Os discos ultra destinam-se a fornecer latências de sub-milisegundos e iops-alvo e produção descrita no quadro anterior 99,99% das vezes.

### <a name="ga-scope-and-limitations"></a>Âmbito e limitações do GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Se quiser começar a utilizar discos ultra, consulte o nosso artigo sobre o assunto: [Utilizar discos ultra Azure](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>SSD Premium

Os SSDs premium Azure oferecem suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) com cargas de trabalho intensivas de entrada/saída (IO). Para aproveitar a velocidade e desempenho dos discos de armazenamento premium, pode migrar discos VM existentes para SSDs Premium. Os SSDs premium são adequados para aplicações de produção críticas da missão. Os SSDs Premium só podem ser utilizados com séries VM compatíveis com armazenamento premium.

Para saber mais sobre os tipos e tamanhos VM individuais em Azure para Windows ou Linux, incluindo quais os tamanhos compatíveis com armazenamento premium, consulte [tamanhos para máquinas virtuais em Azure](sizes.md). A partir deste artigo, você precisa verificar cada artigo de tamanho VM individual para determinar se é compatível com armazenamento premium.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Ao providenciar um disco de armazenamento premium, ao contrário do armazenamento padrão, é-lhe garantida a capacidade, iOPS e a produção desse disco. Por exemplo, se criar um disco P50, o Azure prevê capacidade de armazenamento de 4.095 GB, 7.500 IOPS e 250-MB/s para o disco. A sua aplicação pode utilizar a maior ou parte da capacidade e desempenho. Os discos Premium SSD são projetados para fornecer latências de um dígito baixo e IOPS alvo e produção descrita na tabela anterior 99,9% das vezes.

## <a name="bursting"></a>Estourando

Os tamanhos Premium SSD inferiores ao P30 oferecem agora o rebentamento do disco e podem rebentar o seu IOPS por disco até 3.500 e a sua largura de banda até 170 MB/s. A explosão é automatizada e funciona com base num sistema de crédito. Os créditos são automaticamente acumulados num balde de rutura quando o tráfego de disco está abaixo do objetivo de desempenho previsto e os créditos são automaticamente consumidos quando o tráfego rebenta para além do objetivo, até ao limite máximo de rajada. O limite máximo de rutura define o teto do disco IOPS & largura de banda mesmo que tenha créditos de rutura para consumir. A explosão do disco proporciona uma melhor tolerância às mudanças imprevisíveis dos padrões de IO. Pode aproveitar melhor para o arranque do disco OS e aplicações com tráfego espinhoso.    

O suporte de rebentamento de discos será ativado em novas implementações de tamanhos de disco aplicáveis por padrão, sem necessidade de ação do utilizador. Para os discos existentes dos tamanhos aplicáveis, pode ativar a explosão com qualquer uma das opções: desprender e recolocar o disco ou parar e reiniciar o VM anexado. Todos os tamanhos de disco originais rebentados começarão com um balde de crédito de rajada completa quando o disco é ligado a uma Máquina Virtual que suporta uma duração máxima no limite máximo de rajada de 30 minutos. Para saber mais sobre como estourar o trabalho em Discos Azure, veja [o Premium SSD a rebentar.](linux/disk-bursting.md) 

### <a name="transactions"></a>Transações

No caso dos SSDs premium, cada operação de E/S inferior ou igual a 256 KiB de produção é considerada uma operação única de E/O. As operações de I/O maiores do que 256 KiB de produção são consideradas múltiplas I/Os do tamanho 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs standard Azure são uma opção de armazenamento rentável otimizada para cargas de trabalho que precisam de um desempenho consistente em níveis de IOPS mais baixos. O Standard SSD oferece uma boa experiência de nível de entrada para quem deseja mover-se para a nuvem, especialmente se sentir problemas com a variação de cargas de trabalho em execução nas suas soluções HDD nas instalações. Em comparação com os HDDs padrão, os SSDs padrão proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência. Os SSDs standard são adequados para servidores Web, servidores de aplicações IOPS baixos, aplicações empresariais levemente utilizadas e cargas de trabalho de Dev/Test. Tal como os HDDs standard, os SSDs standard estão disponíveis em todos os VMs Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Os SSDs standard são concebidos para fornecer latências milisegundos de um dígito e o IOPS e produção até aos limites descritos na tabela anterior 99% das vezes. O IOPS real e a produção podem variar, por vezes, dependendo dos padrões de tráfego. Os SSDs standard proporcionarão um desempenho mais consistente do que os discos HDD com a latência mais baixa.

### <a name="transactions"></a>Transações

Para os SSDs standard, cada operação de E/S inferior ou igual a 256 KiB de produção é considerada uma única operação de E/S. As operações de I/O maiores do que 256 KiB de produção são consideradas múltiplas I/Os do tamanho 256 KiB. Estas transações têm um impacto na faturação.

## <a name="standard-hdd"></a>HDD Standard

Os HDDs standard Azure oferecem suporte fiável e de baixo custo para VMs que executam cargas de trabalho insensíveis à latência. Com o armazenamento padrão, os dados são armazenados em discos rígidos (HDDs). A latência, o IOPS e o Rendimento dos discos HDD standard podem variar mais em comparação com os discos baseados em SSD. Os Discos HDD padrão são projetados para fornecer latências de escrita abaixo de 10ms e ler latências abaixo de 20ms para a maioria das operações de IO, no entanto o desempenho real pode variar dependendo do tamanho e padrão de carga de trabalho de IO. Ao trabalhar com VMs, pode utilizar discos HDD padrão para cenários dev/teste e cargas de trabalho menos críticas. Os HDDs standard estão disponíveis em todas as regiões do Azure e podem ser usados com todos os VMs Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transações

Para os HDDs standard, cada operação IO é considerada como uma única transação, independentemente do tamanho de E/S. Estas transações têm um impacto na faturação.

## <a name="billing"></a>Faturação

Ao utilizar discos geridos, aplicam-se as seguintes considerações de faturação:

- Tipo de disco
- tamanho do disco gerido
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerido**: os discos geridos são faturados no tamanho previsto. Azure mapeia o tamanho provisionado (arredondado) para o tamanho do disco mais próximo oferecido. Para mais detalhes sobre os tamanhos do disco oferecidos, consulte as tabelas anteriores. Cada disco mapeia para uma oferta de tamanho de disco a provisionada suportada e é faturado em conformidade. Por exemplo, se a provisionou um SSD Standard 200 GiB, ele mapeia a oferta de tamanho do disco de E15 (256 GiB). A faturação de qualquer disco provisionado é prostimada de hora em hora, utilizando o preço mensal da oferta de armazenamento. Por exemplo, se aprovisionou um disco E10 e o apagou após 20 horas, é cobrado pela oferta E10, proserendo 20 horas. Isto é independentemente da quantidade de dados reais escritos no disco.

**Snapshots**: As imagens são faturadas com base no tamanho utilizado. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade a provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo é faturado apenas para o tamanho de dados usado de 10 GiB.

Para obter mais informações sobre instantâneos, consulte a secção de instantâneos na [visão geral](managed-disks-overview.md)do disco gerido .

**Transferências de dados de saída**: [Transferências de dados de saída (dados saídos](https://azure.microsoft.com/pricing/details/bandwidth/) dos centros de dados Azure) incorrem na faturação para utilização da largura de banda.

**Transações**: É cobrado o número de transações que realiza num disco gerido padrão. Para os SSDs standard, cada operação de E/S inferior ou igual a 256 KiB de produção é considerada uma única operação de E/S. As operações de I/O maiores do que 256 KiB de produção são consideradas múltiplas I/Os do tamanho 256 KiB. Para os HDDs standard, cada operação IO é considerada como uma única transação, independentemente do tamanho de E/S.

Para obter informações detalhadas sobre os preços dos discos geridos, incluindo os custos de transação, consulte [a fixação dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva VM de disco ultra

Os VMs Azure têm a capacidade de indicar se são compatíveis com discos ultra. Um VM ultra compatível com disco atribui capacidade de largura de banda dedicada entre a instância VM compute e a unidade de escala de armazenamento de blocos para otimizar o desempenho e reduzir a latência. A adição desta capacidade no VM resulta numa taxa de reserva que só é imposta se ativar a capacidade do disco ultra no VM sem lhe anexar um disco ultra. Quando um disco ultra é ligado ao VM compatível com disco ultra, esta carga não seria aplicada. Esta taxa é por vCPU provisida no VM. 

> [!Note]
> Para [tamanhos de VM de núcleo limitados,](constrained-vcpu.md)a taxa de reserva baseia-se no número real de VCPUs e não nos núcleos constrangidos. Para Standard_E32 8s_v3, a taxa de reserva será baseada em 32 núcleos. 

Consulte a página de preços do [Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes sobre preços ultra-discos.

### <a name="azure-disk-reservation"></a>Reserva de disco Azure

A reserva em disco é a opção de comprar um ano de armazenamento em disco com desconto, reduzindo o seu custo total. Ao comprar uma reserva de disco, selecione um SKU de disco específico numa região alvo, por exemplo, 10 SSDs premium P30 (1TiB) na região leste dos EUA 2 por um período de um ano. A experiência de reserva é semelhante a instâncias reservadas da máquina virtual (VM). Pode agregar reservas de VM e Disk para maximizar as suas poupanças. Para já, a Azure Disks Reservation oferece um plano de compromisso de um ano para SKUs SSD premium de P30 (1TiB) a P80 (32 TiB) em todas as regiões de produção. Para obter mais detalhes sobre os preços dos Discos Reservados, consulte [a página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Passos seguintes

Consulte [os preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/) para começar.
