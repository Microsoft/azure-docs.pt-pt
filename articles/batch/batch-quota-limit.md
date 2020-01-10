---
title: Cotas e limites de serviço-lote do Azure | Microsoft Docs
description: Saiba mais sobre as cotas, os limites e as restrições do lote do Azure padrão e como solicitar aumentos de cota
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: b63032baa60d18f3d9d98fc403bdc1087c6c9b6b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660686"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch

Assim como ocorre com outros serviços do Azure, há limites em determinados recursos associados ao serviço de lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível de assinatura ou conta. Este artigo discute esses padrões e como você pode solicitar aumentos de cota.

Lembre-se dessas cotas ao projetar e escalar verticalmente suas cargas de trabalho do lote. Por exemplo, se o pool não alcançar o número de destino de nós de computação que você especificou, você pode ter atingido o limite de cota de núcleo para sua conta do lote.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

Se você planeja executar cargas de trabalho de produção no lote, talvez seja necessário aumentar uma ou mais das cotas acima do padrão. Se você quiser gerar uma cota, poderá abrir uma solicitação de atendimento ao [cliente](#increase-a-quota) online sem encargos.

## <a name="resource-quotas"></a>Quotas de recursos

Uma quota é um limite de crédito, não uma garantia de capacidade. Se você tiver necessidades de capacidade em grande escala, entre em contato com o suporte do Azure.

Observe também que as cotas não são valores garantidos. As cotas podem variar com base nas alterações do serviço de lote ou de uma solicitação de usuário para alterar um valor de cota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Cotas de núcleos no modo de assinatura do usuário

Se você criou uma conta do lote com o modo de alocação do pool definido como **assinatura do usuário**, as cotas são aplicadas de forma diferente. Nesse modo, as VMs do lote e outros recursos são criados diretamente em sua assinatura quando um pool é criado. As cotas de núcleos do lote do Azure não se aplicam a uma conta criada neste modo. Em vez disso, as cotas em sua assinatura para núcleos de computação regionais e outros recursos são aplicadas. Saiba mais sobre essas cotas na [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de tamanho do pool

Os limites de tamanho do pool são definidos pelo serviço de lote. Ao contrário das [cotas de recursos](#resource-quotas), esses valores não podem ser alterados. Somente os pools com comunicação entre nós e imagens personalizadas têm restrições diferentes da cota padrão.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| **Nós de computação no [pool habilitado para comunicação entre nós](batch-mpi.md)**  ||
| Modo de alocação do pool do serviço de lote | 100 |
| Modo de alocação do pool de assinaturas do lote | 80 |
| **Nós de computação no [pool criado com um recurso de imagem gerenciada](batch-custom-images.md)** <sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> para pools que não são habilitados para comunicação entre nós.

## <a name="other-limits"></a>Outros limites

Limites adicionais definidos pelo serviço de lote. Ao contrário das [cotas de recursos](#resource-quotas), esses valores não podem ser alterados.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 x número de núcleos de nó |
| [Aplicativos](batch-application-packages.md) por conta do lote | 20 |
| Pacotes de aplicativos por aplicativo | 40 |
| Pacotes de aplicativos por pool | 10 |
| Tempo de vida máximo da tarefa | 180 dias<sup>1</sup> |
| [Montagens](virtual-file-mount.md) por nó de computação | 10 |

<sup>1</sup> o tempo de vida máximo de uma tarefa, desde quando ela é adicionada ao trabalho até ser concluída, é de 180 dias. As tarefas concluídas persistem por sete dias; os dados para tarefas não concluídas no tempo de vida máximo não estão acessíveis.

## <a name="view-batch-quotas"></a>Exibir cotas do lote

Exiba suas cotas de conta do lote no [portal do Azure][portal].

1. Selecione **contas do lote** no portal e, em seguida, selecione a conta do lote em que você está interessado.
1. Selecione **cotas** no menu da conta do lote.
1. Exibir as cotas atualmente aplicadas à conta do lote

    ![Cotas da conta do lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma cota

Siga estas etapas para solicitar um aumento de cota para sua conta do lote ou sua assinatura usando o [portal do Azure][portal]. O tipo de aumento de cota depende do modo de alocação de pool da sua conta do lote. Para solicitar um aumento de cota, você deve incluir a série de VMs para a qual deseja aumentar a cota. Quando o aumento da cota é aplicado, ele é aplicado a todas as séries de VMs.

### <a name="increase-cores-quota-in-batch"></a>Aumentar a cota de núcleos no lote 

1. Selecione o bloco **ajuda + suporte** no painel do portal ou o ponto de interrogação ( **?** ) no canto superior direito do Portal.
1. Selecione **nova solicitação de suporte** > **noções básicas**.
1. Em **noções básicas**:
   
    a. **Tipo de problema** > **limites de serviço e assinatura (cotas)**
   
    b. Selecione a sua subscrição.
   
    c. **Tipo de cota** > **lote**
      
    Selecione **Seguinte**.
    
1. Na **detalhes**:
      
    a. Em **fornecer detalhes**, especifique o local, o tipo de cota e a conta do lote.
    
    ![Aumento da cota de lote][quota_increase]

    Os tipos de cota incluem:

    * **Por conta do lote**  
        Valores específicos de uma única conta do lote, incluindo núcleos dedicados e de baixa prioridade, e o número de trabalhos e pools.
        
    * **Por região**  
        Valores que se aplicam a todas as contas do lote em uma região e incluem o número de contas do lote por região por assinatura.

    A cota de baixa prioridade é um único valor em todas as séries da VM. Se você precisar de SKUs restritos, deverá selecionar **núcleos de baixa prioridade** e incluir as famílias de VMs a serem solicitadas.

    b. Selecione uma **severidade** de acordo com o impacto do seu [negócio][support_sev].

    Selecione **Seguinte**.

1. Em **informações de contato**:
   
    a. Selecione um **método de contato preferencial**.
   
    b. Verifique e insira os detalhes de contato necessários.
   
    Selecione **criar** para enviar a solicitação de suporte.

Depois de enviar sua solicitação de suporte, o suporte do Azure entrará em contato com você. As solicitações de cota podem ser concluídas em alguns minutos ou até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Cotas relacionadas para pools de VMs

Os pools do lote na configuração de máquina virtual implantados em uma rede virtual do Azure alocam automaticamente recursos de rede do Azure adicionais. Os seguintes recursos são necessários para cada nó de pool de 50 em uma rede virtual:

* Um [grupo de segurança de rede](../virtual-network/security-overview.md#network-security-groups)
* Um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Um [balanceador de carga](../load-balancer/load-balancer-overview.md)

Esses recursos são alocados na assinatura que contém a rede virtual fornecida ao criar o pool do lote. Estes recursos estão limitados pelas [quotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Se você planejar implantações de pool grande em uma rede virtual, verifique as cotas da assinatura para esses recursos. Se necessário, solicite um aumento no portal do Azure selecionando **ajuda + suporte**.


## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do lote do Azure usando o portal do Azure](batch-account-create-portal.md)
* [Visão geral do recurso de lote do Azure](batch-api-basics.md)
* [Subscrição do Azure e limites, quotas e limitações do serviço (Azure subscription and service limits, quotas, and constraints)](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
