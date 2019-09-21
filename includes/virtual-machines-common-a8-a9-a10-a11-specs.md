---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 0930fa371500125c15cd969b9e9f4b7a2853612e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174984"
---
## <a name="deployment-considerations"></a>Considerações sobre implementação
* **Assinatura do Azure** – para implantar mais do que algumas instâncias de computação intensiva, considere uma assinatura paga conforme o uso ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

* **Preços e disponibilidade** -esses tamanhos de VM são oferecidos apenas no tipo de preço Standard. Verifique os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade nas regiões do Azure. 
* **Cota de núcleos** – talvez seja necessário aumentar a cota de núcleos na sua assinatura do Azure a partir do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série H. Para solicitar um aumento de cota, [abra uma solicitação](../articles/azure-supportability/how-to-create-azure-support-request.md) de atendimento ao cliente online sem encargos. (Os limites padrão podem variar dependendo da sua categoria de assinatura.)
  
  > [!NOTE]
  > Entre em contato com o suporte do Azure se você tiver necessidades de capacidade em grande escala. As cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente da sua cota, você será cobrado apenas pelos núcleos que usar.
  > 
  > 
* **Rede virtual** – uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure não é necessária para usar as instâncias de computação intensiva. No entanto, para muitas implantações, você precisa de pelo menos uma rede virtual do Azure baseada em nuvem ou uma conexão site a site se precisar acessar recursos locais. Quando necessário, crie uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de VMs com uso intensivo de computação a uma rede virtual em um grupo de afinidade.
* **Redimensionamento** – devido ao seu hardware especializado, você só pode redimensionar instâncias de computação intensiva na mesma família de tamanho (série H ou série a de computação intensiva). Por exemplo, você só pode redimensionar uma VM da série H de um tamanho da série H para outro. Além disso, não há suporte para o redimensionamento de um tamanho que não seja de computação intensa para um tamanho de computação intensiva.  

## <a name="rdma-capable-instances"></a>Instâncias com capacidade de RDMA
Um subconjunto das instâncias de computação intensiva (A8, A9, H16r, H16mr, HB e HC) apresentam um adaptador de rede para conectividade RDMA (acesso remoto direto à memória). Os tamanhos de série N selecionados designados com ' r ', como as configurações de NC24rs (NC24rs_v2 e NC24rs_v3) também são compatíveis com RDMA. Essa interface é além da interface de rede padrão do Azure disponível para outros tamanhos de VM. 
  
Essa interface permite que as instâncias compatíveis com RDMA se comuniquem por meio de uma rede InfiniBand (IB), operando em taxas de EDR para HB, HC, FDR taxas para máquinas virtuais da série N com capacidade para H16r, H16mr e RDMA e taxas de QDR para máquinas virtuais A8 e A9. Esses recursos RDMA podem impulsionar a escalabilidade e o desempenho de determinados aplicativos MPI (interface de transmissão de mensagens). Para obter mais informações sobre velocidade, consulte os detalhes nas tabelas desta página.

> [!NOTE]
> No Azure, o IP sobre IB tem suporte apenas nas VMs habilitadas para SR-IOV (SR-IOV para InfiniBand, atualmente HB e HC). O RDMA sobre IB tem suporte para todas as instâncias compatíveis com RDMA.
>

