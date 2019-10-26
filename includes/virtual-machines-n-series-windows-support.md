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
ms.openlocfilehash: 661849d2d5c42db70d366e48e4138008bc18b720
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902268"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>Drivers NVIDIA Tesla (CUDA)

Os drivers NVIDIA Tesla (CUDA) para as VMs NC, NCv2, NCv3, ND e NDv2 (opcional para a série NV) têm suporte apenas nos sistemas operacionais listados na tabela a seguir. Links de download de driver são atuais no momento da publicação. Para os controladores mais recentes, visite o Web site da [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Windows Server, você pode implantar uma imagem de [máquina virtual de ciência de dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Windows Server 2016 pré-instalar drivers NVIDIA CUDA, a biblioteca de rede neural CUDA Deep e outras ferramentas.


| SO | Controlador |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver de grade NVIDIA para VMs de série NVv3 e NV usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade nas VMs da série NV do Azure, somente nos sistemas operacionais listados na tabela a seguir. Esses drivers incluem licenciamento para o software de GPU virtual de grade no Azure. Você não precisa configurar um servidor de licença de software de vGPU NVIDIA.

Observe que a extensão NVIDIA sempre instalará o driver latst. Fornecemos links para a versão anterior aqui para os clientes que têm dependência em uma versão mais antiga.

| SO | Controlador |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Grade 9,1 (431,79)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) <br/><br/> [Grade 9,0 (431, 2)](https://download.microsoft.com/download/8/C/C/8CC88D54-EB07-44D3-8FA9-B797B173ED04/431.02_grid_win10_server2016_server2019_64bit_international.exe) (. exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Grade 9,1 (431,79)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)<br/><br/> [Grade 9,0 (431, 2)](https://download.microsoft.com/download/B/D/6/BD6D1C34-E41F-4654-B5AB-F4DA9C08AA64/431.02_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (. exe)  |
