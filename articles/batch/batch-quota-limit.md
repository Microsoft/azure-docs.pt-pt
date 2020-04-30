---
title: Quotas e limites do serviço
description: Conheça as quotas, limites e constrangimentos do Lote Azure padrão, e como solicitar aumentos de quota
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: a901d54ea584ec67bfa679e9cdc716ab27d45eca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115827"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch

Tal como acontece com outros serviços azure, existem limites em certos recursos associados ao serviço Batch. Muitos destes limites são quotas por defeito aplicadas pelo Azure ao nível da subscrição ou da conta. Este artigo discute esses incumprimentos e como pode solicitar aumentos de quota.

Tenha em mente estas quotas à medida que projeta e amplia as suas cargas de trabalho do Lote. Por exemplo, se a sua piscina não atingir o número-alvo de nós computacionais que especificou, pode ter atingido o limite de quota central da sua conta Batch.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

Se planeia executar cargas de trabalho de produção em Batch, poderá ter de aumentar uma ou mais quotas acima do padrão. Se quiser aumentar uma quota, pode abrir um pedido de apoio ao [cliente](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Quotas de recursos

Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em larga escala, contacte o suporte do Azure.

Note também que as quotas não são valores garantidos. As quotas podem variar em função das alterações do serviço 'Lote' ou de um pedido do utilizador para alterar o valor da quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de cores no modo de subscrição do utilizador

Se criou uma conta Batch com o modo de alocação de piscina definido para **a subscrição do utilizador,** as quotas são aplicadas de forma diferente. Neste modo, os VMs de Lote e outros recursos são criados diretamente na sua subscrição quando uma piscina é criada. As quotas de núcleos do Lote Azure não se aplicam a uma conta criada neste modo. Em vez disso, aplicam-se as quotas na sua subscrição para núcleos de computação regional e outros recursos. Saiba mais sobre estas quotas nos [limites de subscrição e serviço do Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="pool-size-limits"></a>Limites do tamanho da piscina

Os limites do tamanho da piscina são definidos pelo serviço Batch. Ao contrário das quotas de [recursos,](#resource-quotas)estes valores não podem ser alterados. Apenas piscinas com comunicação inter-nó e imagens personalizadas têm restrições diferentes da quota padrão.

| **Recurso** | **Limite máximo** |
| --- | --- |
| **Nóscomputa em piscina ativada por [comunicação inter-nó](batch-mpi.md)**  ||
| Modo de atribuição de piscina de serviço de lote | 100 |
| Modo de atribuição de piscina de subscrição de lote | 80 |
| **Computação de sos em [piscina criada com um recurso](batch-custom-images.md)** de imagem gerido<sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> Para piscinas que não estejam ativadas para a comunicação inter-nó.

## <a name="other-limits"></a>Outros limites

Limites adicionais definidos pelo serviço Batch. Ao contrário das quotas de [recursos,](#resource-quotas)estes valores não podem ser alterados.

| **Recurso** | **Limite máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de cálculo | 4 x número de núcleos de nó |
| [Aplicações](batch-application-packages.md) por conta Lote | 20 |
| Pacotes de aplicação por aplicação | 40 |
| Pacotes de aplicação por piscina | 10 |
| Duração máxima da tarefa | 180 dias<sup>1</sup> |
| [Montes](virtual-file-mount.md) por nó de computação | 10 |

<sup>1</sup> O tempo máximo de vida de uma tarefa, a partir do momento em que é adicionado ao trabalho até ao momento em que completa, é de 180 dias. As tarefas concluídas persistem durante sete dias; Os dados relativos a tarefas não concluídas no prazo máximo de vida não são acessíveis.

## <a name="view-batch-quotas"></a>Ver quotas de lote

Consulte as quotas da sua conta Batch no [portal Azure][portal].

1. Selecione **as contas do Lote** no portal e, em seguida, selecione a conta 'Lote' em que está interessado.
1. Selecione **Quotas** no menu da conta Batch.
1. Ver as quotas atualmente aplicadas à conta Lote

    ![Quotas de conta de lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma quota

Siga estes passos para solicitar um aumento de quota para a sua conta Batch ou a sua subscrição utilizando o [portal Azure][portal]. O tipo de aumento de quota depende do modo de atribuição de piscina da sua conta Batch. Para solicitar um aumento de quota, deve incluir a série VM para a sua necessidade de aumentar a quota. Quando o aumento da quota é aplicado, é aplicado a todas as séries de VMs.

### <a name="increase-cores-quota-in-batch"></a>Aumentar a quota de núcleos em Lote 

1. Selecione o azulejo **de suporte Help +** no seu portal dashboard, ou o ponto de interrogação (**?**) no canto superior direito do portal.
1. Selecione **Novo pedido** > de suporte**Básicos**.
1. No **Básico:**
   
    a. **Serviço de tipo** > de**emissão e limites de subscrição (quotas)**
   
    b. Selecione a sua subscrição.
   
    c. **Lote tipo** > **Batch** quota
      
    Selecione **Seguinte**.
    
1. Em **Detalhes**:
      
    a. Em **fornecer detalhes**, especifique a localização, o tipo de quota e a conta Lote.
    
    ![Aumento da quota de lote][quota_increase]

    Os tipos de quotas incluem:

    * **Conta Por Lote**  
        Valores específicos de uma única conta batch, incluindo núcleos dedicados e de baixa prioridade, e número de empregos e piscinas.
        
    * **Por região**  
        Valores aplicáveis a todas as contas do Lote numa região e inclui o número de contas de Lote por região por subscrição.

    A quota de baixa prioridade é um valor único em todas as séries VM. Se precisar de SKUs constrangidos, deve selecionar **núcleos de baixa prioridade** e incluir as famílias VM a solicitar.

    b. Selecione uma **Severidade** de acordo com o impacto do seu [negócio][support_sev].

    Selecione **Seguinte**.

1. Em informações de **contacto:**
   
    a. Selecione um método de **contacto preferido**.
   
    b. Verifique e introduza os dados de contacto necessários.
   
    Selecione **Criar** para submeter o pedido de apoio.

Assim que tiver apresentado o seu pedido de apoio, o suporte do Azure entrará em contacto consigo. Os pedidos de quota podem ser concluídos dentro de alguns minutos ou até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Quotas relacionadas para piscinas vm

Os conjuntos de lotes na Configuração da Máquina Virtual implantados numa rede virtual Azure atribuem automaticamente recursos adicionais de rede Azure. São necessários os seguintes recursos para cada 50 nós de piscina numa rede virtual:

* Um [grupo de segurança de rede](../virtual-network/security-overview.md#network-security-groups)
* Um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Um [equilibrador de carga](../load-balancer/load-balancer-overview.md)

Estes recursos são atribuídos na subscrição que contém a rede virtual fornecida na criação do pool Batch. Estes recursos estão limitados pelas [quotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Se planeia grandes implantações de piscinas numa rede virtual, verifique as quotas de subscrição para estes recursos. Se necessário, solicite um aumento no portal Azure selecionando **Ajuda + suporte**.


## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do Azure Batch no portal do Azure](batch-account-create-portal.md)
* [Visão geral da funcionalidade do Lote Azure](batch-api-basics.md)
* [Subscrição do Azure e limites, quotas e limitações do serviço (Azure subscription and service limits, quotas, and constraints)](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
