---
title: Tamanhos de máquinavirtual para serviços Azure Cloud [ Microsoft Docs
description: Lista os diferentes tamanhos de máquinavirtual (e IDs) para funções web de serviço em nuvem Azure e funções de trabalhador.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 34cb4282f64544e67b3724699380d1d54fd9b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247478"
---
# <a name="sizes-for-cloud-services"></a>Tamanhos para serviços em nuvem
Este tópico descreve os tamanhos e opções disponíveis para as instâncias de função do Serviço cloud (funções web e funções de trabalhador). Também fornece considerações de implantação para estar ciente quando planeia utilizar estes recursos. Cada tamanho tem um ID que coloca no seu ficheiro de [definição](cloud-services-model-and-package.md#csdef)de serviço . Os preços de cada tamanho estão disponíveis na página de Preços dos [Serviços](https://azure.microsoft.com/pricing/details/cloud-services/) cloud.

> [!NOTE]
> Para ver limites de Azure relacionados, consulte [limites de subscrição e serviço do Azure, quotas e constrangimentos](../azure-resource-manager/management/azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamanhos para exemplos de funções web e trabalhador
Existem vários tamanhos standard à escolha no Azure. As considerações para alguns destes tamanhos incluem:

* As VMs da série D foram concebidos para executar aplicações que exigem um maior desempenho de poder de computação e disco temporário. As VMs da série D fornecem processadores mais rápidos, um rácio de memória para núcleo superior e uma unidade de estado sólida (SSD) para o disco temporário. Para obter detalhes, consulte o anúncio no blogue do Azure, [Novos Tamanhos de Máquinas Virtuais da Série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* A série Dv3, série Dv2, uma continuação da série D original, apresenta um CPU mais poderoso. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se no processador de última geração de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell), podendo chegar aos 3,1 GHz com o Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.
* As VMs da série G oferecem a maior capacidade de memória e são executadas em anfitriões com processadores da família Intel Xeon E5 V3.
* Os VMs da série A podem ser implantados em vários tipos e processadores de hardware. O tamanho é acelerado, com base no hardware, para oferecer um desempenho consistente do processador para a instância de execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual.
* O tamanho A0 está sobre-subscrito no hardware físico. Apenas para este tamanho específico, outras implementações de cliente podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é indicado abaixo como a linha de base esperada, sujeito a uma variabilidade aproximada de 15%.

O tamanho da máquina virtual afeta o preço. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados em separado com base nas páginas utilizadas na conta de armazenamento. Para mais detalhes, consulte os detalhes dos [preços dos serviços](https://azure.microsoft.com/pricing/details/cloud-services/) da Cloud e [os preços de armazenamento do Azure.](https://azure.microsoft.com/pricing/details/storage/)

As considerações seguintes podem ajudar a decidir um tamanho:

* Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obter informações detalhadas e considerações sobre a utilização destes tamanhos, consulte [tamanhos vM](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de computação de alto desempenho .
* As séries Dv3, séries Dv2, série D, série G, são ideais para aplicações que exigem CPUs mais rápido, melhor desempenho do disco local ou têm maiores exigências de memória. Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.
* Alguns dos anfitriões físicos nos centros de dados do Azure podem não suportar tamanhos de máquina virtual grandes, tais como A5 – A11. Como resultado, pode ver a mensagem de erro **Não configurar a máquina virtual {nome** da máquina} ou **falhou em criar a máquina virtual {machine name}** ao redimensionar uma máquina virtual existente para um novo tamanho; criação de uma nova máquina virtual numa rede virtual criada antes de 16 de abril de 2013; ou adicionar uma nova máquina virtual a um serviço de nuvem existente. Ver [Erro: "Falhou em configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte para sobras para cada cenário de implementação.
* A sua subscrição também pode limitar o número de núcleos que pode implementar em determinadas famílias de tamanhos. Para aumentar uma quota, contacte o Suporte do Azure.

## <a name="performance-considerations"></a>Considerações de desempenho
Criámos o conceito da Unidade de Computação Azure (ACU) para fornecer uma forma de comparar o desempenho da computação (CPU) através do Azure SKUs e identificar quais os SKU mais propensos a satisfazer as suas necessidades de desempenho.  A ACU está atualmente normalizada numa VM Pequena (Standard_A1) de 100 e todos os outros SKUs representam aproximadamente a velocidade máxima a que esse SKU consegue executar um teste de desempenhopadrão.

> [!IMPORTANT]
> A ACU é apenas uma orientação. Os resultados da sua carga de trabalho podem variar.
>
>

<br>

| Família de SKU | ACU/Núcleo |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Pequena Extragrande](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [Um v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

As ACUs marcadas com um * utilizam a tecnologia Intel® Turbo para aumentar a frequência de CPU e proporcionar um aumento do desempenho. A quantidade do aumento pode variar consoante o tamanho da VM, a carga de trabalho e outras cargas de trabalho em execução no mesmo anfitrião.

## <a name="size-tables"></a>Tabelas de tamanhos
As tabelas seguintes mostram os tamanhos e as capacidades que fornecem.

* A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3), não se esqueça de que os números de capacidade especificados em GiB podem aparecer mais pequenos. Por exemplo, 1023 GiB = 1098,4 GB
* O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
* Os discos de dados podem operar nos modos em cache ou não colocado em cache. Para uma operação do disco de dados em cache, o modo de cache do anfitrião está definido como **ReadOnly** ou **ReadWrite**. Para uma operação do disco de dados não colocada em cache, o modo de cache do anfitrião está definido como **None**.
* A largura de banda máxima da rede é a largura de banda máxima agregada atribuída por tipo de VM. A largura de banda máxima fornece orientações para selecionar o tipo de VM certo para assegurar que está disponível a capacidade de rede adequada. Ao mover-se entre Baixo, Moderado, Alto e Muito Alto, a entrada aumenta em conformidade. O desempenho de rede real irá depender de vários fatores, incluindo cargas da rede e das aplicações, bem como das definições de rede das aplicações.

## <a name="a-series"></a>Série A
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0.768        | 20                   | 1/baixo |
| Pequeno           | 1         | 1.75         | 225                  | 1 / moderado |
| Médio          | 2         | 3.5          | 490                  | 1 / moderado |
| Grande           | 4         | 7            | 1000                 | 2 / alto |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / alto |
| A5              | 2         | 14           | 490                  | 1 / moderado |
| A6              | 4         | 28           | 1000                 | 2 / alto |
| A7              | 8         | 56           | 2040                 | 4 / alto |

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva
Para obter informações e considerações sobre a utilização destes tamanhos, consulte [tamanhos vM](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de computação de alto desempenho .

| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário: GiB       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2 / alto |
| A9*             |16         | 112          | 1817                 | 4 / muito alto |
| A10             |8          | 56           | 1817                 | 2 / alto |
| A11             |16         | 112          | 1817                 | 4 / muito alto |

\*RDMA capaz

## <a name="av2-series"></a>Série Av2

| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / moderado                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/moderado                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / alto                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / alto                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/moderado                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / alto                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / alto                     |


## <a name="d-series"></a>Série D
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1 / moderado |
| Standard_D2     | 2         | 7            | 100                  | 2 / alto |
| Standard_D3     | 4         | 14           | 200                  | 4 / alto |
| Standard_D4     | 8         | 28           | 400                  | 8 / alto |
| Standard_D11    | 2         | 14           | 100                  | 2 / alto |
| Standard_D12    | 4         | 28           | 200                  | 4 / alto |
| Standard_D13    | 8         | 56           | 400                  | 8 / alto |
| Standard_D14    | 16        | 112          | 800                  | 8 / muito alto |

## <a name="dv2-series"></a>Série Dv2
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1 / moderado |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / alto |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / alto |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / alto |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / extremamente alto |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / alto |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / alto |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / alto |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / extremamente alto |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8 / extremamente alto |

## <a name="dv3-series"></a>Série Dv3

| Tamanho            | Núcleos de CPU | Memória: GiB   | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2/moderado |
| Standard_D4_v3  | 4         | 16            | 100                  | 2 / alto |
| Standard_D8_v3  | 8         | 32            | 200                  | 4 / alto |
| Standard_D16_v3 | 16        | 64            | 400                  | 8 / extremamente alto |
| Standard_D32_v3 | 32        | 128           | 800                  | 8 / extremamente alto |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8 / extremamente alto |

## <a name="ev3-series"></a>Série Ev3

| Tamanho            | Núcleos de CPU | Memória: GiB   | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2/moderado |
| Standard_E4_v3  | 4         | 32            | 100                  | 2 / alto |
| Standard_E8_v3  | 8         | 64            | 200                  | 4 / alto |
| Standard_E16_v3 | 16        | 128           | 400                  | 8 / extremamente alto |
| Standard_E32_v3 | 32        | 256           | 800                  | 8 / extremamente alto |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8 / extremamente alto |


## <a name="g-series"></a>Série G
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/alto |
| Standard_G2     | 4         | 56           | 768                  |2 / alto |
| Standard_G3     | 8         | 112          | 1,536                |4 / muito alto |
| Standard_G4     | 16        | 224          | 3,072                |8 / extremamente alto |
| Standard_G5     | 32        | 448          | 6,144                |8 / extremamente alto |

## <a name="h-series"></a>Série H
As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho destinadas a necessidades computacionais avançadas, como modelagem molecular e dinâmicas de fluídos computacionais. Estes VMs de 8 e 16 núcleos são construídos na tecnologia de processador Intel Haswell E5-2667 V3 com memória DDR4 e armazenamento local baseado em SSD.

Além do poder de CPU substancial, a série H oferece diversas opções para funcionamento em rede RDMA de baixa latência através de FDR InfiniBand e várias configurações de memória para suportar requisitos computacionais de memória intensiva.

| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento Temporário (SSD): gib       | NICs/Largura de banda da rede máximos |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / alto |
| Standard_H16    | 16        | 112          | 2000                 | 8 / muito alto |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / alto |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / muito alto |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / muito alto |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / muito alto |

\*RDMA capaz

## <a name="configure-sizes-for-cloud-services"></a>Configurar tamanhos para serviços de nuvem
Pode especificar o tamanho da Máquina Virtual de uma instância de função como parte do modelo de serviço descrito pelo ficheiro de [definição](cloud-services-model-and-package.md#csdef)de serviço . O tamanho da função determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de ficheiros local que é atribuído a uma instância de execução. Escolha o tamanho da função com base no requisito de recursos da sua aplicação.

Aqui está um exemplo para definir o tamanho da função a ser Standard_D2 para uma instância de Papel Web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Alterar o tamanho de um papel existente

À medida que a natureza da sua carga de trabalho muda ou novos tamanhos de VM ficam disponíveis, pode querer alterar o tamanho do seu papel. Para tal, tem de alterar o tamanho vM no ficheiro de definição de serviço (como mostrado acima), reembalar o seu Serviço cloud e implementá-lo.

>[!TIP]
> Você pode querer usar diferentes tamanhos vM para o seu papel em diferentes ambientes (por exemplo. teste vs produção). Uma maneira de fazer isso é criar ficheiros de definição de serviço múltiplo (.csdef) no seu projeto e, em seguida, criar diferentes pacotes de serviço sinuoso por ambiente durante a sua construção automatizada usando a ferramenta CSPack. Para saber mais sobre os elementos de um pacote de serviços na nuvem e como criá-los, veja [qual é o modelo de serviços na nuvem e como o embala?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Obtenha uma lista de tamanhos
Pode utilizar o PowerShell ou a Rest API para obter uma lista de tamanhos. A API REST está documentada [aqui.](/previous-versions/azure/reference/dn469422(v=azure.100)) O seguinte código é um comando PowerShell que listará todos os tamanhos disponíveis para serviços de nuvem. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
* Saiba mais [sobre tamanhos vM](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) de computação de alto desempenho para cargas de trabalho hpc.



