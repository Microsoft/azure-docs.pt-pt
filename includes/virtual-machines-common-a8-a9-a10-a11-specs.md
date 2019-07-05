---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 88d9491bae1fcc6acb7ab631514aa4f6244f593d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538033"
---
## <a name="deployment-considerations"></a>Considerações sobre implementação
* **Subscrição do Azure** – para implementar mais do que algumas instâncias de computação intensiva, considere uma subscrição pay as you go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

* **Preços e disponibilidade** -tamanhos de VM estes são oferecidos apenas no padrão de escalão de preço. Verifique [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade em regiões do Azure. 
* **Quota de núcleos** – poderá ter de aumentar a quota de núcleos na sua subscrição do Azure do valor predefinido. A sua subscrição também pode limitar o número de núcleos que pode implementar em determinadas famílias de tamanhos VM, incluindo a série H. Para pedir um aumento de quota [abra um pedido de suporte do cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) sem encargos. (Os limites predefinidos podem variar consoante a categoria de subscrição.)
  
  > [!NOTE]
  > Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure. Quotas do Azure são o crédito limita, não as garantias de capacidade. Independentemente da sua quota, é cobrado apenas por núcleos que utilize.
  > 
  > 
* **Rede virtual** -uma do Azure [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) não é necessária para utilizar as instâncias de computação intensiva. No entanto, para muitas implementações precisa de, pelo menos, uma rede de virtual do Azure com base na cloud, ou uma ligação site a site se precisar de aceder a recursos no local. Quando for necessário, crie uma nova rede virtual para implementar as instâncias. Não é suportada a adição de VMs de computação intensiva para uma rede virtual de um grupo de afinidades.
* **Redimensionamento** – devido a hardware especializado, apenas pode redimensionar as instâncias de computação intensiva dentro da mesma família de tamanho (série H ou de computação intensiva de série). Por exemplo, apenas pode redimensionar uma VM de série H a partir de um tamanho de série H para outro. Além disso, a partir de um tamanho de não-computação intensiva de redimensionamento para um tamanho de computação intensiva não é suportado.  

## <a name="rdma-capable-instances"></a>Instâncias com capacidade de RDMA
Um subconjunto das instâncias de computação intensiva (A8, A9, H16r, H16mr, HB e HC) apresentam uma interface de rede para a conectividade de acesso (RDMA) de memória direta remoto. Tamanhos de série N selecionados designados com 'r', tais como as configurações de NC24rs (NC24rs_v2 e NC24rs_v3) também são compatíveis com RDMA. Essa interface é, além da interface de rede do Azure standard disponível para outros tamanhos de VM. 
  
Essa interface permite que as instâncias com capacidade RDMA comunicar através de uma rede InfiniBand (IB), operacional às tarifas EDR para HB, HC, FDR taxas para máquinas virtuais H16r, H16mr e série N com capacidade RDMA e taxas de QDR para máquinas virtuais A8 e A9. Estas capacidades RDMA podem aumentar a escalabilidade e desempenho de determinados aplicativos de Interface de passagem de mensagens (MPI). Para obter mais informações sobre a velocidade, consulte os detalhes das tabelas nesta página.

> [!NOTE]
> No Azure, o IP sobre IB só é suportada no SR-IOV ativado VMs (SR-IOV InfiniBand, atualmente HB e HC). RDMA sobre IB é suportada para todas as instâncias com capacidade RDMA.
>

