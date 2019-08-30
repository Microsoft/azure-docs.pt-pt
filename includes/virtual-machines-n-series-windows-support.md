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
ms.openlocfilehash: bab282fded7e4b30d2eca6ed51ceaecf22206869
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166766"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>Drivers NVIDIA Tesla (CUDA)

Os drivers NVIDIA Tesla (CUDA) para as VMs NC, NCv2, NCv3, ND e NDv2 (opcional para a série NV) têm suporte apenas nos sistemas operacionais listados na tabela a seguir. Links de download de driver são atuais no momento da publicação. Para os controladores mais recentes, visite o Web site da [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Windows Server, você pode implantar uma imagem de [máquina virtual de ciência de dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Windows Server 2016 pré-instalar drivers NVIDIA CUDA, a biblioteca de rede neural CUDA Deep e outras ferramentas.


| OS | Controlador |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver de grade NVIDIA para VMs de série NVv3 e NV usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade nas VMs da série NV do Azure, somente nos sistemas operacionais listados na tabela a seguir. Esses drivers incluem licenciamento para o software de GPU virtual de grade no Azure. Você não precisa configurar um servidor de licença de software de vGPU NVIDIA.

Observe que a extensão NVIDIA sempre instalará o driver latst. Fornecemos links para a versão anterior aqui para os clientes que têm dependência em uma versão mais antiga.

| OS | Controlador |
| -------- |------------- |
| Windows Server de 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Grade 9,0 (431, 2)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) <br/><br/> [Grade 8,0 (425,31)](https://download.microsoft.com/download/4/8/C/48C2D46E-EB64-460E-A8D9-0F55737D0D68/425.31_grid_win10_server2016_64bit_international.exe) (. exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Grade 9,0 (431, 2)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)<br/><br/> [Grade 8,0 (425,31)](https://download.microsoft.com/download/6/D/7/6D73C628-B5FB-4243-9520-DAEF363223CB/425.31_grid_win8_win7_server2012R2_server2008R2_64bit_international.exe) (. exe)  |
