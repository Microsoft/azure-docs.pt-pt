---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f58d08629fcde791186d27ae09e7417453faf8ad
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585798"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>Controladores de NVIDIA Tesla (CUDA)

Controladores de NVIDIA Tesla (CUDA) para NC, NCv2, NCv3, ND e as VMs da série NDv2 (opcionais para a série NV) são suportados apenas em sistemas operativos listados na tabela seguinte. Ligações de transferência do controlador são atualizadas no momento da publicação. Para os controladores mais recentes, visite o Web site da [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação de controlador CUDA manual numa VM do Windows Server, pode implementar do Azure [máquina de Virtual de ciência de dados](../articles/machine-learning/data-science-virtual-machine/overview.md) imagem. As edições DSVM do Windows Server 2016 previamente instalar controladores de NVIDIA CUDA, CUDA Deep Neural biblioteca de rede e outras ferramentas.


| SO | Controlador |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Controladores de GRID da NVIDIA

Microsoft redistribui programas de instalação de controladores de NVIDIA GRID NV e as VMs da série NVv2 utilizadas como estações de trabalho virtuais ou para aplicações virtuais. Instale apenas estes controladores GRID em VMs do Azure NV, apenas em sistemas operativos listados na tabela seguinte. Estes controladores incluem licenças de Software GPU Virtual GRADE no Azure. Não é necessário configurar um servidor de licenças de software de vGPU NVIDIA.

| SO | Controlador |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRELHA 7 (411.81)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [GRELHA 7 (411.81)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
