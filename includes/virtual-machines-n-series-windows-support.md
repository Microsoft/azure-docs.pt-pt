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
ms.openlocfilehash: 04b085d2e990a580ddc99acb3b83ac8bd8ac2db3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86998987"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>Condutores da NVIDIA Tesla (CUDA)

Os controladores NVIDIA Tesla (CUDA) para NC, NCv2, NCv3, ND e NDv2 série VMs (opcional para série NV) são suportados apenas nos sistemas operativos listados na tabela seguinte. Os links de descarregamento do condutor estão em vigor no momento da publicação. Para os controladores mais recentes, visite o Web site da [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do controlador CUDA num VM do Servidor do Windows, pode implementar uma imagem [da Máquina Virtual Azure Data Science.](../articles/machine-learning/data-science-virtual-machine/overview.md) As edições DSVM para controladores NVIDIA CUDA pré-instalarem o Windows Server 2016, a Biblioteca da Rede Neural Profunda CUDA e outras ferramentas.


| SO | Controlador |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Condutores da NVIDIA GRID

A Microsoft redistribui os instaladores de controladores NVIDIA GRID para VMs da série NV e NVv3 utilizados como estações de trabalho virtuais ou para aplicações virtuais. Instale apenas estes controladores GRID em VMs da série Azure NV, apenas nos sistemas operativos listados na tabela seguinte. Estes controladores incluem licenciamento para GRID Virtual GPU Software em Azure. Não precisa de configurar um servidor de licença de software NVIDIA vGPU.

Os controladores GRID redistribuídos pela Azure não funcionam em VMs não-NV séries como NC, NCv2, NCv3, ND e NDv2 série VMs.

Por favor, note que a extensão da Nvidia irá sempre instalar o mais recente controlador. Fornecemos links para a versão anterior aqui para clientes, que têm dependência de uma versão mais antiga.

Para o Windows Server 2019, Windows Server 2016 e Windows 10 (até 2004):
- [GRELHA 11 (451.48)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRELHA 10.1 (442.06)](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) (.exe) 

Para o Windows Server 2012 R2: 
- [GRELHA 11 (451.48)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [GRELHA 10.1 (442.66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (.exe)  
