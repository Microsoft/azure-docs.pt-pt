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
ms.openlocfilehash: 0dda467c3906c217c18e1340f7dc2a464dcbccae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127794"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) drivers

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
| Windows Server 2016<br/><br/>Windows 10 (até versão 1803) | [GRELHA 8.0 (425.31)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [GRELHA 8.0 (425.31)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
