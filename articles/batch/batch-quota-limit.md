---
title: Quotas e limites - Azure Batch Service | Documentos da Microsoft
description: Saiba mais sobre quotas do Azure Batch predefinidas, limites e restrições e aumenta a como pedir quota
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 820eddff7da3bb52ca94ea0cb7e2361d89892a4a
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595324"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch

Como com outros serviços do Azure, existem limites em certos recursos associados com o serviço Batch. Muitos destes limites são quotas predefinidas aplicadas pelo Azure no nível de conta ou subscrição. Este artigo aborda os padrões e como pode solicitar quota aumenta.

Tenha presentes estas quotas em mente à medida que, design e aumentar verticalmente as cargas de trabalho do Batch. Por exemplo, se o seu conjunto não alcance o número de destino de nós de computação que especificou, poderá atingiu o limite de quota de núcleos para a sua conta do Batch.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

Se planeja executar cargas de trabalho de produção no Batch, se pretender aumentar um ou mais das quotas acima da predefinição. Se pretender aumentar uma quota, pode abrir um online [pedido de suporte ao cliente](#increase-a-quota) sem encargos.

> [!NOTE]
> Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure.

## <a name="resource-quotas"></a>Quotas de recursos
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de núcleos no modo de subscrição de utilizador

Se tiver criado uma conta do Batch com o modo de alocação de conjuntos definido como **subscrição do utilizador**, as quotas são aplicadas de forma diferente. Neste modo, as VMs do Batch e outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. As quotas de núcleos do Azure Batch não são aplicáveis a uma conta criada neste modo. Em vez disso, as quotas na sua subscrição para regionais de núcleos de computação e outros recursos são aplicados. Saiba mais sobre estas quotas no [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de tamanho do conjunto

| **Recurso** | **Limite Máximo** |
| --- | --- |
| **Nós de computação em [conjunto de comunicação entre nós ativada](batch-mpi.md)**  ||
| Modo de alocação do conjunto de serviço do batch | 100 |
| Modo de alocação de conjuntos de subscrição de batch | 80 |
| **Nós de computação em [conjunto criado com a imagem de VM personalizada](batch-custom-images.md)**<sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> para agrupamentos que não estão a comunicação entre nós ativada.

## <a name="other-limits"></a>Outros limites

| **Recurso** | **Limite Máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 x número de núcleos do nó |
| [Aplicativos](batch-application-packages.md) por conta do Batch | 20 |
| Pacotes de aplicação por aplicação | 40 |
| Pacotes de aplicações por conjunto | 10 |
| Duração de tarefa máximo | 180 dias<sup>1</sup> |

<sup>1</sup> a duração máxima de uma tarefa, quando é adicionado à tarefa para quando for concluída, é de 180 dias. As tarefas concluídas mantêm durante sete dias; dados de tarefas não concluídas dentro da duração máxima não estão acessíveis.

## <a name="view-batch-quotas"></a>Ver quotas de Batch

Ver as suas quotas de conta do Batch no [portal do Azure][portal].

1. Selecione **contas do Batch** no portal, em seguida, selecione a conta do Batch tiver interesse em.
1. Selecione **Quotas** no menu da conta do Batch.
1. Ver as quotas atualmente aplicadas para a conta do Batch

    ![Quotas das contas do batch][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma quota

Siga estas etapas para pedir uma quota de aumentam para sua conta do Batch ou a sua subscrição com o [portal do Azure][portal]. O tipo de aumento de quota depende do modo de alocação de conjuntos da sua conta do Batch. Para pedir um aumento de quota, tem de incluir a série VM para aumentar a quota para. Quando o aumento de quota é aplicado, esta é aplicada a todas as séries de VMs.

### <a name="increase-cores-quota-in-batch"></a>Aumentar a quota de núcleos no Batch 

1. Selecione o **ajuda + suporte** mosaico no dashboard do portal ou o ponto de interrogação (**?**) no canto superior direito do portal.
1. Selecione **novo pedido de suporte** > **Noções básicas**.
1. Na **Noções básicas**:
   
    a. **Tipo de problema** > **limites de serviço e subscrição (cotas)**
   
    b. Selecione a sua subscrição.
   
    c. **Tipo de quota** > **Batch**
      
    Selecione **Seguinte**.
    
1. Na **detalhes**:
      
    a. Na **fornecem detalhes**, especifique a localização, o tipo de quota e conta do Batch.
    
    ![Aumento de quota de lote][quota_increase]

    Os tipos de quota incluem:

    * **Por conta do Batch**  
        Valores específicos para um único lote da conta, incluindo núcleos dedicados e de baixa prioridade e número de tarefas e agrupamentos.
        
    * **Por região**  
        Valores que se aplicam a todas as contas do Batch numa região e incluem o número de contas do Batch por região por subscrição.

    Quota de baixa prioridade é um valor único em todas as séries VM. Se precisar de SKUs restritas, tem de selecionar **núcleos de prioridade baixa** e incluem as famílias VM para pedir.

    b. Selecione um **gravidade** acordo com a sua [impacto comercial][support_sev].

    Selecione **Seguinte**.

1. Na **informações de contacto**:
   
    a. Selecione um **método preferencial de contacto**.
   
    b. Verificar e introduza os detalhes de contactos necessários.
   
    Selecione **criar** para submeter o pedido de suporte.

Depois de ter enviado o pedido de suporte, o suporte do Azure irá contactá-lo. Pedidos de quota podem ser concluídos dentro de alguns minutos ou até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Quotas relacionadas para conjuntos VM

Os conjuntos do batch na configuração da Máquina Virtual implementada numa rede virtual do Azure automaticamente alocar recursos de rede do Azure adicionais. Os recursos a seguir são necessários para cada nós de conjunto de 50 numa rede virtual:

* Um [grupo de segurança de rede](../virtual-network/security-overview.md#network-security-groups)
* Um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Um [Balanceador de carga](../load-balancer/load-balancer-overview.md)

Esses recursos são alocados na subscrição que contém a rede virtual fornecida durante a criação de conjunto do Batch. Estes recursos estão limitados pelas [quotas de recursos](../azure-subscription-service-limits.md) da subscrição. Se planear implementações de grande pool numa rede virtual, verifique as quotas a subscrição para esses recursos. Se for necessário, pedir um aumento no portal do Azure, selecionando **ajuda + suporte**.


## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do Azure Batch no portal do Azure](batch-account-create-portal.md)
* [Descrição geral da funcionalidade do Azure Batch](batch-api-basics.md)
* [Subscrição do Azure e limites, quotas e limitações do serviço (Azure subscription and service limits, quotas, and constraints)](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
