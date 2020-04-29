---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5dccdb6c357635e78b076b1560bf6c0c62c03753
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77135124"
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e controladores suportados

### <a name="nvidia-cuda-drivers"></a>Condutores da NVIDIA CUDA

Os condutores da NVIDIA CUDA para OS VMs nc, NCv2, NCv3, ND e NDv2 séries (opcional para séries NV) são suportados apenas nas distribuições Linux listadas na tabela seguinte. A informação do condutor da CUDA está atual no momento da publicação. Para os mais recentes condutores da CUDA, visite o site da [NVIDIA.](https://developer.nvidia.com/cuda-zone) Certifique-se de que instala ou atualiza para os mais recentes controladores CUDA para a sua distribuição. 

> [!TIP]
> Como alternativa à instalação manual do controlador CUDA num VM Linux, pode implementar uma imagem [da Máquina Virtual](../articles/machine-learning/data-science-virtual-machine/overview.md) de Ciência de Dados Azure. As edições DSVM para Ubuntu 16.04 LTS ou CentOS 7.4 pré-instalar os controladores NVIDIA CUDA, a Biblioteca da Rede Neural Profunda cuda, e outras ferramentas.

| Distribuição | Controlador |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> CentOS-based 7.3, 7.4, 7.5, 7.6, CentOS-based 7.4 HPC | NVIDIA CUDA 10.1, ramo de condutor R418 |

### <a name="nvidia-grid-drivers"></a>Condutores da NVIDIA GRID

A Microsoft redistribui os instaladores de controladores NVIDIA GRID para VMs da série NV e NVv3 utilizados como estações de trabalho virtuais ou para aplicações virtuais. Instale apenas estes controladores GRID em VMs Azure NV, apenas nos sistemas operativos listados na tabela seguinte. Estes condutores incluem o licenciamento para o SOFTWARE GPU Virtual GRID em Azure. Não precisa de configurar um servidor de licença de software VGPU NVIDIA.

| Distribuição | Controlador |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.0 a 7.6<br/><br/>Baseado em CentOS 7.0 a 7.6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10.1, ramo de condutor R440|

> [!WARNING] 
> A instalação de software de terceiros em produtos Red Hat pode afetar os termos de suporte do Red Hat. Veja o [Red Hat Knowledgebase](https://access.redhat.com/articles/1067) (Base de Dados de Conhecimento do Red Hat).
>
