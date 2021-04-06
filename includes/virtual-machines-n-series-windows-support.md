---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5413a6acafa0ea54f98383fc8140a34aff0cf840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98860181"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>Condutores da NVIDIA Tesla (CUDA)

Os controladores NVIDIA Tesla (CUDA) para NC, NCv2, NCv3, NCasT4_v3, ND e NDv2 série VMs (opcional para série NV) são suportados apenas nos sistemas operativos listados na tabela seguinte. Os links de descarregamento do condutor estão em vigor no momento da publicação. Para os controladores mais recentes, visite o Web site da [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do controlador CUDA num VM do Servidor do Windows, pode implementar uma imagem [da Máquina Virtual Azure Data Science.](../articles/machine-learning/data-science-virtual-machine/overview.md) As edições DSVM para controladores NVIDIA CUDA pré-instalarem o Windows Server 2016, a Biblioteca da Rede Neural Profunda CUDA e outras ferramentas.


| SO | Controlador |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Condutores da NVIDIA GRID

A Microsoft redistribui os instaladores de controladores NVIDIA GRID para VMs da série NV e NVv3 utilizados como estações de trabalho virtuais ou para aplicações virtuais. Instale apenas estes controladores GRID em VMs da série Azure NV, apenas nos sistemas operativos listados na tabela seguinte. Estes controladores incluem licenciamento para GRID Virtual GPU Software em Azure. Não precisa de configurar um servidor de licença de software NVIDIA vGPU.

Os controladores GRID redistribuídos pela Azure não funcionam em VMs não-NV séries como NCv2, NCv3, ND e NDv2 série VMs. A única exceção é a série VM NCas_T4_V3 onde os controladores GRID permitirão as funcionalidades gráficas semelhantes às séries NV.

Os NC-Series com GPUs Nvidia K80 não suportam aplicações GRID/graphics.  

Por favor, note que a extensão da Nvidia irá sempre instalar o mais recente controlador. Fornecemos links para a versão anterior aqui para clientes, que têm dependência de uma versão mais antiga.

Para o Windows Server 2019, Windows Server 2016 1607, 1709 e Windows 10(até para construir 20H2):
- [GRELHA 12.0 (461.09)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRELHA 11.3 (452.77)](https://download.microsoft.com/download/f/d/5/fd5ad39b-89cb-4990-ae85-a6fd30475584/452.77_grid_win10_server2016_server2019_64bit_azure_swl.exe) (.exe) 

Para o Windows Server 2012 R2: 
- [GRELHA 12.0 (461.09)](https://download.microsoft.com/download/c/5/e/c5e7df99-364d-45f5-bff7-c253d59121f1/461.09_grid_server2012R2_64bit_azure_swl.exe) (.exe)
- [GRELHA 11.3 (452.77)](https://download.microsoft.com/download/5/4/3/54323644-3c84-4aa1-97ec-35491f94c866/452.77_grid_server2012R2_64bit_azure_swl.exe) (.exe) 


Para obter a lista completa de todas as ligações anteriores do controlador Nvidia GRID visite [o GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
