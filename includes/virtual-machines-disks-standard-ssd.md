---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: da46687517dbfe189571286087d4ef29d50d1246
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564282"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>SSD Managed Disks Standard para cargas de trabalho de Máquina Virtual do Azure

Unidades de estado sólido padrão (SSD) Managed Disks do Azure são uma opção de armazenamento económico, otimizada para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS. Standard SSD oferece uma experiência de bom nível de entrada para aqueles que pretende mover para a cloud, especialmente se ocorrerem problemas com a variância de cargas de trabalho em execução em suas soluções HDD no local. SSDs padrão proporcionar melhor disponibilidade, consistência, fiabilidade e latência em comparação comparada discos de HDD e são adequadas para servidores Web, servidores de aplicações de IOPS reduzidos, aplicações empresariais pouco usada e cargas de trabalho de programador/teste.

## <a name="standard-ssd-features"></a>Funcionalidades Standard SSD

**Discos geridos**: Standard SSDs só estão disponíveis como Managed Disks. Blobs de páginas e discos não geridos não são suportados em Standard SSD. Ao criar o disco gerido, especifique o tipo de disco como Standard SSD e indicar o tamanho do disco é necessário, e o Azure cria e gere o disco por si.
Standard SSDs suportam todas as operações de modelo de implementação clássica oferecidas pelo Managed Disks. Por exemplo, pode criar, copiar ou instantâneo SSD Managed Disks Standard da mesma forma que fazer com o Managed Disks.

**Máquinas virtuais**: SSDs padrão podem ser utilizados com todas as VMs do Azure, incluindo os tipos VM que não suporta discos Premium. Por exemplo, se estiver a utilizar uma VM de série, ou VMS de série N ou série DS, ou qualquer outra série de VM do Azure, pode utilizar SSDs padrão com essa VM. Com a introdução do padrão de SSD, estamos a ativar uma vasta gama de cargas de trabalho que tenha utilizado os discos baseados em HDD para fazer a transição para discos baseado em SSD e experimentar o desempenho consistente, maior disponibilidade, latência melhor e um melhor geral experiência que acompanham o SSDs.

**Elevada disponibilidade e durabilidade**: SSDs padrão baseiam-se na mesma plataforma de discos do Azure, que forneceu consistentemente elevada disponibilidade e durabilidade para discos. Discos do Azure foram concebidos para disponibilidade de 99,999%. Como todos os discos geridos, SSDs padrão também oferece armazenamento com redundância Local (LRS). Com o LRS, a plataforma mantém várias réplicas de dados para cada disco e forneceu consistentemente a nível empresarial durabilidade para IaaS discos, com um líder do setor ZERO taxa de falhas anuais a percentagem.

**Instantâneos**: Como todos os discos geridos, Standard SSDs suporta também a criação de instantâneos. Tipo de instantâneo pode ser Standard (HDD) ou Premium (SSD). Para economia de custo, recomendamos que o tipo de instantâneo do Standard (HDD) para todos os tipos de disco do Azure. Isto acontece porque quando cria um disco gerido a partir de um instantâneo, sempre pode escolher um escalão mais elevado, como Standard SSD ou Premium SSD.

## <a name="scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho

A tabela seguinte contém os tamanhos de disco, que são oferecidos atualmente para Standard SSD. Tamanhos marcados com um asterisco estão atualmente em pré-visualização.

|Tipo de disco Standard SSD  |Tamanho do Disco  |IOPS por disco  |Débito por disco  |
|---------|---------|---------|---------|
|E4     |32 GiB         |Até 120         |Até 25 MiB por segundo         |
|E6     |64 GiB         |Até 240         |Máximo de 50 MiB por segundo         |
|E10     |128 GiB         |Até 500         |MiB até 60 por segundo         |
|E15     |256 GiB         |Até 500         |MiB até 60 por segundo         |
|E20     |512 GiB         |Até 500         |MiB até 60 por segundo         |
|E30     |1.024 giB       |Até 500         |MiB até 60 por segundo         |
|E40     |2048 giB       |Até 500         |MiB até 60 por segundo         |
|E50     |4095 giB       |Até 500         |MiB até 60 por segundo         |
|E60 *     |8.192 giB       |Até 1.300       |Até 300 MiB por segundo        |
|E70 *    |16.384 giB      |Até 2000       |Até 500 MiB por segundo        |
|E80 *    |32.767 giB      |Até 2000       |Até 500 MiB por segundo        |

SSDs padrão foram concebidos para fornecer latências de milissegundo de dígito na maioria das operações de e/s e para disponibilizar o IOPS e débito até aos limites descrito na tabela acima 99% do tempo. IOPS e o débito real podem variar, às vezes, consoante os padrões de tráfego. SSDs padrão irão proporcionar um desempenho mais consistente que os discos HDD com menor latência.

Premium SSDs por outro lado, melhor desempenho do que SSDs padrão, com latências baixas, IOPS/débito alto e consistência ainda melhor com o desempenho de disco aprovisionado. Premium SSD é o tipo de disco recomendado para cargas de trabalho de produção crítico. Se a sua carga de trabalho requer o suporte de disco de elevado desempenho e de baixa latência, considere utilizar o armazenamento Premium.

Como SSD Premium, o SSDs padrão também utilizar tamanho da unidade de e/s de 256 KiB. Se os dados que está a ser transferidos forem inferior a 256 KiB, ele é considerado 1 unidade de e/s. Tamanhos de e/s maiores são contabilizados como várias e/SS de tamanho 256 KiB. Por exemplo, uma e/s de KiB 1,100 é contabilizado como cinco unidades de e/s.

## <a name="pricing-and-billing"></a>Preços e faturação

Ao utilizar o padrão do SSDs, aplicam-se as seguintes considerações de faturas:

- Tamanho do disco de geridos
- Instantâneos
- Transferências de dados de saída
- Transações

**Geridos pelo tamanho do disco**: Discos geridos são cobrados no tamanho aprovisionado. O Azure mapeia o tamanho aprovisionado (arredondado para cima) para a oferta de tamanho de disco mais próxima. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte a tabela na secção de metas de desempenho e escalabilidade acima. Cada disco é mapeado para um tamanho de disco de aprovisionamento suportados e são faturadas em conformidade. Por exemplo, se tiver aprovisionado um 200 GiB Standard SSD, vai mapear para a oferta de tamanho de disco de E15 (256gib). Para qualquer disco aprovisionado a faturação é contabilizada à hora, utilizando o preço mensal para a oferta de armazenamento Premium. Por exemplo, se aprovisionar um disco de E10 e eliminado depois de 20 horas, é cobrado para a oferta de E10 Rateado para 20 horas. Isto é, independentemente da quantidade de dados reais, escritos no disco.

**Instantâneos**: Os instantâneos de discos geridos são cobrados a capacidade utilizada pelos instantâneos, o destino e na origem, se houver. Para obter mais informações sobre instantâneos, consulte [instantâneos de disco gerido](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Transferências de dados de saída**: [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) faturação para utilização de largura de banda de incorrer em (dados que saem de datacenters do Azure).

**Transações**: Semelhante ao padrão HDD, transações SSDs padrão incorrem a faturação. Transações incluem as opções de leitura e escrita no disco. Tamanho da unidade de e/s utilizado para as transações de gestão de contas no padrão de SSD é 256 KiB. Tamanhos de e/s maiores são contabilizados como várias e/SS de tamanho 256 KiB.

Para obter mais informações sobre os preços para máquinas virtuais e Managed Disks, consulte:

- [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a criação de SSDs padrão, consulte o exemplo que mostra como criar uma VM com vários SSDs padrão.

> [!div class="nextstepaction"]
> [Criar uma VM com vários SSDs padrão](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)
