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
ms.openlocfilehash: caad292f06710fca8d6f64476eead5dfcb164e9d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96536953"
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

Os controladores GRID redistribuídos pela Azure não funcionam em VMs não-NV séries como NC, NCv2, NCv3, ND e NDv2 série VMs. A única exceção é a série VM NCas_T4_V3 onde os controladores GRID permitirão as funcionalidades gráficas semelhantes às séries NV.

Por favor, note que a extensão da Nvidia irá sempre instalar o mais recente controlador. Fornecemos links para a versão anterior aqui para clientes, que têm dependência de uma versão mais antiga.

Para o Windows Server 2019, Windows Server 2016 e Windows 10 (até 2004):
- [GRELHA 11.2 (452.57)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRELHA 11.1 (452.39)](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) (.exe) 

Para o Windows Server 2012 R2: 
- [GRELHA 11.0 (451.48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (.exe) 
- [GRELHA 10.1 (442.66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (.exe)  

Para obter a lista completa de todas as ligações anteriores do controlador Nvidia GRID visite [o GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
