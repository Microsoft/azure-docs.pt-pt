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
ms.openlocfilehash: 22d2ddc65616bbeb0c511afa6f60bc12c6ec67ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022048"
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
- [GRELHA 11.1 (452.39)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRELHA 11.0 (451.48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Para o Windows Server 2012 R2: 
- [GRELHA 11.0 (451.48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 
- [GRELHA 10.1 (442.66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (.exe)  
