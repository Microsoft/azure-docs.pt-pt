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
ms.openlocfilehash: 0ed7fbf5e2b4f7f9b554f718d88c62e7a266f4ad
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903880"
---
## <a name="supported-distributions-and-drivers"></a>Distribuições e controladores suportados

### <a name="nvidia-cuda-drivers"></a>Drivers NVIDIA CUDA

Os drivers NVIDIA CUDA para VMs NC, NCv2, NCv3, ND e NDv2 (opcional para a série NV) têm suporte apenas nas distribuições do Linux listadas na tabela a seguir. As informações do driver CUDA são atuais no momento da publicação. Para obter os drivers CUDA mais recentes, visite o site do [NVIDIA](https://developer.nvidia.com/cuda-zone) . Certifique-se de instalar ou atualizar para os drivers CUDA mais recentes para sua distribuição. 

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Linux, você pode implantar uma imagem de [máquina virtual de ciência de dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM para Ubuntu 16, 4 LTS ou CentOS 7,4 pré-instale os drivers NVIDIA CUDA, a biblioteca de rede neural profunda CUDA e outras ferramentas.

| Distribuição | Controlador |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> Baseado em CentOS 7,3, 7,4, 7,5, 7,6, HPC baseado em CentOS 7,4 | NVIDIA CUDA 10,1, Branch de driver R418 |

### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver de grade NVIDIA para VMs de série NVv3 e NV usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade em VMs NV do Azure, somente nos sistemas operacionais listados na tabela a seguir. Esses drivers incluem licenciamento para o software de GPU virtual de grade no Azure. Você não precisa configurar um servidor de licença de software de vGPU NVIDIA.

| Distribuição | Controlador |
| --- | -- |
|Ubuntu 18, 4 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 a 7,6<br/><br/>Baseado em CentOS 7,0 a 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 9,0, ramificação do driver R430|

> [!WARNING] 
> A instalação de software de terceiros em produtos Red Hat pode afetar os termos de suporte do Red Hat. Veja o [Red Hat Knowledgebase](https://access.redhat.com/articles/1067) (Base de Dados de Conhecimento do Red Hat).
>
