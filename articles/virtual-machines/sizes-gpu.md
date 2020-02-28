---
title: Tamanhos Azure VM - GPU / Microsoft Docs
description: Lista os diferentes tamanhos otimizados da GPU disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada de armazenamento e largura de banda da rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: e7e9ff795f7f639686b9f629dacb6f0752c20732
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660211"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU otimizado tamanhos de máquina virtual

Os tamanhos de VM otimizados pela GPU são máquinas virtuais especializadas disponíveis com GPUs únicaou múltipla seleções da NVIDIA. Estes tamanhos são projetados para cargas de trabalho intensivas em cálculo, gráficos-intensivos e visualização. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A produção de armazenamento e a largura de banda da rede também estão incluídas para cada tamanho neste agrupamento.

- [Série NC](nc-series.md), [série NCv2,](ncv2-series.md)tamanhos da [série NCv3](ncv3-series.md) são otimizados para aplicações e algoritmos intensivos de computação e de rede. Alguns exemplos são aplicações e simulações baseadas em CUDA e OpenCL, IA e Deep Learning. A série NCv3 está focada em cargas de trabalho de computação de alto desempenho com o GPU Tesla V100 da NVIDIA. A série NC utiliza o processador Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) e o processador NCv2 série e NCv3 da série VMs usam o processador Intel Xeon E5-2690 v4 (Broadwell).

- [As séries ND](nd-series.md)e os tamanhos das [séries NDv2](ndv2-series.md) estão focados em cenários de treino e inferência para uma aprendizagem profunda. Utilizam o GPU NVIDIA Tesla P40 e o processador Intel Xeon E5-2690 v4 (Broadwell). A série NDv2 utiliza o processador Intel Xeon Platinum 8168 (Skylake).

- Os tamanhos das [séries NV](nv-series.md) e [NVv3](nvv3-series.md) são otimizados e projetados para visualização remota, streaming, jogos, codificação e cenários vDI usando estruturas como OpenGL e DirectX. Estes VMs são apoiados pela NVIDIA Tesla M60 GPU.

- [Série NVv4](nvv4-series.md) Tamanhos VM otimizados e projetados para VDI e visualização remota. Com GPUs divididos, o NVv4 oferece o tamanho certo para cargas de trabalho que requerem recursos de GPU menores. Estes VMs são apoiados pela AMD Radeon Instinct MI25 GPU. Atualmente, os VMs NVv4 suportam apenas o sistema operativo windows.

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades de GPU das VMs da série N Azure, os condutores de GPU da NVIDIA devem ser instalados.

A Extensão do [Condutor GPU da NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para sistemas operativos suportados e etapas de implementação. Para obter informações gerais sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual Azure.](/azure/virtual-machines/extensions/overview)

Se optar por instalar manualmente os controladores GPU da NVIDIA, consulte a configuração do controlador GPU da série N para a configuração do controlador [GPU da série N](/azure/virtual-machines/windows/n-series-driver-setup) para o [Linux](/azure/virtual-machines/linux/n-series-driver-setup) para sistemas operativos suportados, controladores, instalação e verificação.

## <a name="deployment-considerations"></a>Considerações sobre implementação

- Para disponibilidade de VMs da série N, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

- Os VMs da série N só podem ser implantados no modelo de implementação do Gestor de Recursos.

- Os VMs da série N diferem no tipo de Armazenamento Azure que suportam para os seus discos. Os VMs NC e NV suportam apenas discos VM que são apoiados pelo Armazenamento de Discos Standard (HDD). NCv2, NCv3, ND, NDv2 e NVv2 VMs suportam apenas discos VM que são apoiados por Armazenamento de Discos Premium (SSD).

- Se quiser implementar mais do que alguns VMs da série N, considere uma subscrição pay-as-you-go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

- Poderá ser necessário aumentar a quota de núcleos (por região) na sua subscrição Azure e aumentar a quota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV ou NVv2. Para solicitar um aumento de quota, [abra um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md) ao cliente online gratuitamente. Os limites de predefinição podem variar dependendo da sua categoria de subscrição.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
