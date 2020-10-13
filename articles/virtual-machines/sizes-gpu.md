---
title: Tamanhos Azure VM - GPU / Microsoft Docs
description: Lista os diferentes tamanhos otimizados da GPU disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento e largura de banda de rede para tamanhos nesta série.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 87314e38225d6e08f81ce6c3855f70a13db9c6bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653184"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais otimizadas da GPU

Os tamanhos de VM otimizados da GPU são máquinas virtuais especializadas disponíveis com GPUs individuais, múltiplas ou fracionadas. Estes tamanhos são projetados para cargas de trabalho de computação intensiva, gráfica intensiva e visualização. Este artigo fornece informações sobre o número e tipo de GPUs, vCPUs, discos de dados e NICs. A produção de armazenamento e a largura de banda da rede também estão incluídas para cada tamanho neste agrupamento.

- [Série NC](nc-series.md), [série NCv2](ncv2-series.md), [série NCv3](ncv3-series.md) e tamanhos [de séries NCT4_v3](nct4-v3-series.md) são otimizados para aplicações e algoritmos intensivos de computação intensiva e de rede. Alguns exemplos são aplicações e simulações baseadas em CUDA e OpenCL, IA e Deep Learning. A série NCT4v3 está focada em cargas de trabalho de inferência com o processador Tesla T4 GPU da NVIDIA e o processador AMD EPYC2 Rome. A série NCv3 está focada em cargas de trabalho de computação de alto desempenho com o Tesla V100 GPU da NVIDIA. A série NC utiliza o processador Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) e os VMs da série NCv2 e NCv3 utilizam o processador Intel Xeon E5-2690 v4 (Broadwell).

- [As séries ND](nd-series.md)e os tamanhos [da série NDv2](ndv2-series.md) estão focados em cenários de treino e inferência para aprendizagem profunda. A série ND utiliza o GPU NVIDIA Tesla P40 e o processador Intel Xeon E5-2690 v4 (Broadwell). A série NDv2 utiliza o processador Nvidia Volta V100 e o processador Intel Xeon Platinum 8168 (Skylake).

- Os tamanhos [da série NV](nv-series.md) e [NVv3](nvv3-series.md) são otimizados e projetados para visualização remota, streaming, jogos, codificação e cenários VDI usando quadros como OpenGL e DirectX. Estes VMs são apoiados pelo GPU NVIDIA Tesla M60.

- [Série NVv4](nvv4-series.md) Tamanhos VM otimizados e projetados para VDI e visualização remota. Com GPUs divididos, o NVv4 oferece o tamanho certo para cargas de trabalho que requerem recursos de GPU mais pequenos. Estes VMs são apoiados pela GPU AMD Radeon Instinct MI25. Atualmente, os VMS NVv4 suportam apenas o sistema operativo para hóspedes do Windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades da GPU dos condutores da série Azure N VMs, NVIDIA ou AMD GPU devem ser instalados.

- Para os VMs apoiados pela NVIDIA GPUs, a [Extensão do Condutor GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) instala controladores adequados NVIDIA CUDA ou GRID. Instale ou gerencie a extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação da [extensão do condutor do GPU da NVIDIA](./extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implantação. Para obter informações gerais sobre extensões de VM, consulte [extensões e funcionalidades de máquinas virtuais Azure](./extensions/overview.md).

   Em alternativa, pode instalar manualmente os controladores da NVIDIA GPU. Consulte [os controladores GPU da NVIDIA em VMs da série N que executam o Windows](./windows/n-series-driver-setup.md) ou [instale os controladores GU NVIDIA em VMs da série N que executam o Linux](./linux/n-series-driver-setup.md) para sistemas operativos suportados, controladores, instalações e etapas de verificação.

- Para VMs apoiados por GPUs AMD, consulte [os controladores DE GPU da AMD em VMs da série N que executam o Windows](./windows/n-series-amd-driver-setup.md) para sistemas operativos suportados, controladores, instalações e etapas de verificação.

## <a name="deployment-considerations"></a>Considerações sobre implementação

- Para obter disponibilidade de VMs da série N, consulte [produtos disponíveis por região.](https://azure.microsoft.com/regions/services/)

- Os VMs da série N só podem ser implantados no modelo de implementação do Gestor de Recursos.

- Os VMs da série N diferem no tipo de Armazenamento Azure que suportam para os seus discos. Os VMs NC e NV suportam apenas discos VM que são apoiados por Armazenamento Padrão de Disco (HDD). NCv2, NCv3, ND, NDv2 e NVv2 VMs suportam apenas discos VM que são apoiados por Premium Disk Storage (SSD).

- Se quiser implementar mais do que alguns VMs da série N, considere uma subscrição pay-as-you-go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

- Você pode precisar aumentar a quota de núcleos (por região) na sua assinatura Azure, e aumentar a quota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV ou NVv2. Para solicitar um aumento de quota, [abra gratuitamente um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md) ao cliente online. Os limites predefinidos podem variar dependendo da sua categoria de subscrição.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
