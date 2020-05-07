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
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77135164"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>Pilotos da NVIDIA Tesla (CUDA)

Os condutores da NVIDIA Tesla (CUDA) para os VMs nc, NCv2, NCv3, ND e NDv2 séries (opcional para séries NV) são suportados apenas nos sistemas operativos listados na tabela seguinte. Os links de descarregamento do condutor estão atuais no momento da publicação. Para os controladores mais recentes, visite o Web site da [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do controlador CUDA num VM do Windows Server, pode implementar uma imagem da Máquina Virtual de [Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) Do Azure. As edições DSVM para windows Server 2016 pré-instalar os controladores NVIDIA CUDA, a Biblioteca de Rede Neural Profunda CUDA e outras ferramentas.


| SO | Controlador |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Condutores da NVIDIA GRID

A Microsoft redistribui os instaladores de controladores NVIDIA GRID para VMs da série NV e NVv3 utilizados como estações de trabalho virtuais ou para aplicações virtuais. Instale apenas estes controladores GRID em VMs da série Azure NV, apenas nos sistemas operativos listados na tabela seguinte. Estes condutores incluem o licenciamento para o SOFTWARE GPU Virtual GRID em Azure. Não precisa de configurar um servidor de licença de software VGPU NVIDIA.

Por favor, note que a extensão Nvidia instalará sempre o mais recente controlador. Fornecemos links para a versão anterior aqui para os clientes, que têm dependência de uma versão mais antiga.

Para windows Server 2019, Windows Server 2016 e Windows 10 (até à construção de 1909):
- [GRELHA 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRELHA 10.0 (441.66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Para windows Server 2012 R2, Windows Server 2008 R2, Windows 8 e Windows 7: 
- [GRELHA 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [GRELHA 10.0 (441.66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
