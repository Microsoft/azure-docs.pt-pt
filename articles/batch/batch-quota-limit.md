---
title: Quotas e limites do serviço
description: Saiba mais sobre as quotas, limites e restrições do Lote Azure padrão e como solicitar aumentos de quota
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 4c13df8b537d701400a22cd2871e7f8362f02455
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417281"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch

Tal como acontece com outros serviços Azure, existem limites em certos recursos associados ao serviço Batch. Muitos destes limites são quotas de incumprimento aplicadas pela Azure ao nível da subscrição ou da conta.

Tenha estas quotas em mente à medida que projeta e amplia as cargas de trabalho do Batch. Por exemplo, se a sua piscina não atingir o número alvo de nós de computação especificados, poderá ter atingido o limite de quota de base para a sua conta Batch.

Pode executar várias cargas de trabalho do Batch numa única conta batch, ou distribuir as suas cargas de trabalho entre contas batch que estão na mesma subscrição, mas em diferentes regiões de Azure.

Se planeia executar cargas de trabalho de produção em Batch, poderá ter de aumentar uma ou mais das quotas acima do padrão. Se quiser levantar uma quota, pode abrir gratuitamente um pedido de apoio ao [cliente](#increase-a-quota) online.

## <a name="resource-quotas"></a>Quotas de recursos

Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de grande capacidade, contacte o suporte da Azure.

Note-se também que as quotas não são valores garantidos. As quotas podem variar com base em alterações do serviço Batch ou de um pedido de utilizador para alterar um valor de quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de núcleos no modo de subscrição do utilizador

Se criou uma [conta Batch](accounts.md) com modo de atribuição de piscina definido para **a subscrição do utilizador,** as quotas são aplicadas de forma diferente. Neste modo, os VMs do Lote e outros recursos são criados diretamente na sua subscrição quando um pool é criado. As quotas de núcleos Azure Batch não se aplicam a uma conta criada neste modo. Em vez disso, são aplicadas as quotas na sua subscrição para núcleos de computação regional e outros recursos.

Para saber mais sobre estas quotas, consulte [os limites de subscrição e serviços da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="pool-size-limits"></a>Limites de tamanho da piscina

Os limites do tamanho da piscina são definidos pelo serviço Batch. Ao contrário das [quotas de recursos,](#resource-quotas)estes valores não podem ser alterados. Apenas piscinas com comunicação inter-nó e imagens personalizadas têm restrições diferentes da quota padrão.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| **Nó de computação na [comunicação inter-nosd ativada pool](batch-mpi.md)**  ||
| Modo de atribuição de piscina de serviço de lote | 100 |
| Modo de atribuição de piscina de assinatura de lote | 80 |
| **Nó de computação em [piscina criada com um recurso de imagem gerido](batch-custom-images.md)**<sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> Para piscinas que não estejam ativadas pela comunicação entre nós.

## <a name="other-limits"></a>Outros limites

Limites adicionais definidos pelo serviço Batch. Ao contrário das [quotas de recursos,](#resource-quotas)estes valores não podem ser alterados.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 x número de núcleos de nó |
| [Candidaturas](batch-application-packages.md) por conta Batch | 20 |
| Pacotes de aplicação por aplicação | 40 |
| Pacotes de aplicação por piscina | 10 |
| Duração máxima da tarefa | 180 dias<sup>1</sup> |
| [Montes](virtual-file-mount.md) por nó de computação | 10 |

<sup>1</sup> A duração máxima de uma tarefa, a partir do momento em que é adicionada ao trabalho até ao momento em que termina, é de 180 dias. As tarefas concluídas persistem durante sete dias; os dados relativos a tarefas não concluídas no prazo máximo de vida não são acessíveis.

## <a name="view-batch-quotas"></a>Ver quotas de lote

Para ver as suas quotas de conta Batch no [portal Azure:](https://portal.azure.com)

1. Selecione **as contas De Lote**e, em seguida, selecione a conta Batch em que está interessado.
1. Selecione **Quotas** no menu da conta Batch.
1. Ver as quotas atualmente aplicadas na conta Batch.

    ![Quotas de conta de lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma quota

Pode solicitar um aumento de quota para a sua conta Batch ou para a sua subscrição utilizando o [portal Azure.](https://portal.azure.com) O tipo de aumento de quota depende do modo de atribuição de piscinas da sua conta Batch. Para solicitar um aumento de quota, deve incluir a série VM para a quais gostaria de aumentar a quota. Quando o aumento da quota é aplicado, é aplicado a todas as séries de VMs.

1. Selecione o azulejo **de suporte Help +** no seu painel de instrumentos do portal ou o ponto de interrogação **(?**
1. Selecione **Novo pedido de suporte**  >  **Básicos**.
1. No **Básico:**
   
    1. Tipo de **Emissão**  >  **Limites de serviço e subscrição (quotas)**
   
    1. Selecione a sua subscrição.
   
    1. **Tipo de quota**  >  **Lote**
      
       Selecione **Seguinte**.
    
1. Em **Detalhes**:
      
    1. Em **Fornecer detalhes**, especifique a localização, o tipo de quota e a conta Batch.
    
       ![Aumento da quota de lote][quota_increase]

       Os tipos de quotas incluem:

       * **Por conta de lote**  
         Valores específicos de uma única conta batch, incluindo núcleos dedicados e de baixa prioridade, e número de empregos e piscinas.
        
       * **Por região**  
         Valores aplicáveis a todas as contas do Batch numa região e que incluem o número de contas batch por região por subscrição.

       A quota de baixa prioridade é um valor único em todas as séries VM. Se precisar de SKUs limitados, deve selecionar **núcleos de baixa prioridade** e incluir as famílias VM a solicitar.

    1. Selecione uma **Severidade** de acordo com o impacto do seu [negócio](https://aka.ms/supportseverity).

       Selecione **Seguinte**.

1. Nas **informações de contacto:**
   
    1. Selecione um **método de contacto preferido.**
   
    1. Verifique e introduza os dados de contacto necessários.
   
       **Selecione Criar** para submeter o pedido de suporte.

Assim que submeter o seu pedido de apoio, o suporte da Azure entrará em contacto consigo. Os pedidos de quota podem ser preenchidos dentro de poucos minutos ou até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Quotas relacionadas para piscinas VM

Os lotes na Configuração de Máquina Virtual implantados numa rede virtual Azure alocam automaticamente recursos adicionais de rede Azure. São necessários os seguintes recursos para cada 50 nós de piscina numa rede virtual:

- Um [grupo de segurança de rede](../virtual-network/security-overview.md#network-security-groups)
- Um [endereço IP público](../virtual-network/public-ip-addresses.md)
- Um [equilibrador de carga](../load-balancer/load-balancer-overview.md)

Estes recursos são atribuídos na subscrição que contém a rede virtual fornecida ao criar o pool batch. Estes recursos estão limitados pelas [quotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Se planeia grandes implantações de piscinas numa rede virtual, verifique as quotas da subscrição para estes recursos. Se necessário, solicite um aumento no portal Azure selecionando **o suporte Help +**.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta Azure Batch utilizando o portal Azure](batch-account-create-portal.md).
* Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
* Conheça os [limites de subscrição e serviço da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
