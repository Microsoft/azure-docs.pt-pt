---
title: Quotas e limites do serviço
description: Saiba mais sobre as quotas, limites e restrições do Lote Azure padrão e como solicitar aumentos de quota
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553554"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch

Tal como acontece com outros serviços Azure, existem limites em certos recursos associados ao serviço Batch. Muitos destes limites são quotas de incumprimento aplicadas pela Azure ao nível da subscrição ou da conta.

Tenha estas quotas em mente à medida que projeta e amplia as cargas de trabalho do Batch. Por exemplo, se a sua piscina não atingir o número alvo de nós de computação especificados, poderá ter atingido o limite de quota de base para a sua conta Batch.

Pode executar várias cargas de trabalho do Batch numa única conta batch, ou distribuir as suas cargas de trabalho entre contas batch que estão na mesma subscrição, mas em diferentes regiões de Azure.

Se planeia executar cargas de trabalho de produção em Batch, poderá ter de aumentar uma ou mais das quotas acima do padrão. Para aumentar uma quota, pode [solicitar um aumento de quota](#increase-a-quota) sem custos.

## <a name="resource-quotas"></a>Quotas de recursos

Uma quota é um limite, não uma garantia de capacidade. Se tiver necessidades de grande capacidade, contacte o suporte da Azure.

Note-se também que as quotas não são valores garantidos. As quotas podem variar com base em alterações do serviço Batch ou de um pedido de utilizador para alterar um valor de quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Quotas nucleares

### <a name="cores-quotas-in-batch-service-mode"></a>Quotas de núcleos no modo de serviço Batch

Existem quotas de base para cada série VM suportada pelo Batch. Estas quotas principais são apresentadas na página **Quotas** no portal Azure. Os limites de quota da série VM podem ser atualizados com um pedido de apoio, conforme descrito abaixo. Para os nós dedicados, o Batch impõe um limite de quota de base para cada série VM, bem como um limite total de quota de base para toda a conta Batch. Para os nós de baixa prioridade, o Batch aplica apenas uma quota de base total para a conta Batch sem qualquer distinção entre diferentes séries de VM.

### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de núcleos no modo de subscrição do utilizador

Se criou uma [conta Batch](accounts.md) com modo de atribuição de piscina definida para **a subscrição do utilizador,** os VMs do lote e outros recursos são criados diretamente na sua subscrição quando um pool é criado ou redimensionado. As quotas centrais do Azure Batch não se aplicam e as quotas na sua subscrição para núcleos de computação regional, núcleos computacional por série, e outros recursos são utilizados e aplicados.

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

Estes limites adicionais são definidos pelo serviço Batch. Ao contrário das [quotas de recursos,](#resource-quotas)estes valores não podem ser alterados.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 x número de núcleos de nó |
| [Candidaturas](batch-application-packages.md) por conta Batch | 20 |
| Pacotes de aplicação por aplicação | 40 |
| Pacotes de aplicação por piscina | 10 |
| Duração máxima da tarefa | 180 dias<sup>1</sup> |
| [Montes](virtual-file-mount.md) por nó de computação | 10 |
| Certificados por piscina | 12 |

<sup>1</sup> A duração máxima de uma tarefa, a partir do momento em que é adicionada ao trabalho até ao momento em que termina, é de 180 dias. As tarefas concluídas persistem durante sete dias; os dados relativos a tarefas não concluídas no prazo máximo de vida não são acessíveis.

## <a name="view-batch-quotas"></a>Ver quotas de lote

Para ver as suas quotas de conta Batch no [portal Azure:](https://portal.azure.com)

1. Selecione **as contas De Lote** e, em seguida, selecione a conta Batch em que está interessado.
1. Selecione **Quotas** no menu da conta Batch.
1. Ver as quotas atualmente aplicadas na conta Batch.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Screenshot mostrando quotas de conta batch no portal Azure.":::

## <a name="increase-a-quota"></a>Aumentar uma quota

Pode solicitar um aumento de quota para a sua conta Batch ou para a sua subscrição utilizando o [portal Azure](https://portal.azure.com) ou utilizando a [API AZure Quota REST](#azure-quota-rest-api).

O tipo de aumento de quota depende do modo de atribuição de piscinas da sua conta Batch. Para solicitar um aumento de quota, deve incluir a série VM para a quais gostaria de aumentar a quota. Quando o aumento da quota é aplicado, é aplicado a todas as séries de VMs.

Assim que submeter o seu pedido de apoio, o suporte da Azure entrará em contacto consigo. Os pedidos de quota podem ser preenchidos dentro de poucos minutos ou até dois dias úteis.

### <a name="azure-portal"></a>Portal do Azure

1. Na página **Quotas,** selecione **Aumentar a quota de pedido.** Em alternativa, pode selecionar o azulejo **de suporte Help +** no seu painel de instrumentos do portal (ou a partir do ponto de interrogação **(?**) no canto superior direito do portal e, em seguida, selecionar novo pedido de **suporte.**

1. No **Básico:**

    1. Para **o Tipo de Emissão**, selecione **limites de serviço e de subscrição (quotas)**.
    1. Selecione a sua subscrição.
    1. Para **o tipo quota**, selecione **Batch**.
    1. Selecione **Seguinte** para continuar.

1. Em **Detalhes**:

    1. Na secção **Fornecer detalhes,** especifique a localização, o tipo de quota e a conta Batch (se aplicável), selecione então as quotas para aumentar.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Screenshot do ecrã de detalhes da Quota ao solicitar um aumento de quota.":::

       Os tipos de quotas incluem:

       - **Por conta de lote**  
         Utilize esta opção para solicitar aumentos de quota específicos para uma única conta Batch, incluindo núcleos dedicados e de baixa prioridade, e o número de empregos e piscinas.

         Se selecionar esta opção, especifique a conta Batch a que este pedido deve ser aplicado e, em seguida, selecione as quotas que pretende atualizar. Forneça o novo limite que está a solicitar para cada recurso.

         A quota de baixa prioridade é um valor único em todas as séries VM. Se precisar de SKUs limitados, deve selecionar **núcleos de baixa prioridade** e incluir as famílias VM a solicitar.

       - **Todas as contas desta região**  
         Utilize esta opção para solicitar aumentos de quota que se aplicam a todas as contas do Batch numa região, tais como o número de contas batch por região por subscrição.

    1. No **método De Suporte,** selecione uma **Severidade** de acordo com o impacto do seu [negócio](https://aka.ms/supportseverity) e o seu método de contacto preferido e linguagem de suporte.

    1. Nas **informações de contacto,** verifique e introduza os dados de contacto necessários.

1. Selecione **Rever + criar** e, em seguida, selecione **Criar** para submeter o pedido de suporte.

### <a name="azure-quota-rest-api"></a>Azure Quota REST API

Pode utilizar a Azure Quota REST API para solicitar um aumento de quota ao nível da subscrição ou ao nível da conta Batch.

Para mais detalhes e exemplos, consulte [Solicite um aumento de quota utilizando a API de Apoio ao Azure](/rest/api/support/quota-payload#azure-batch)Support .

## <a name="related-quotas-for-vm-pools"></a>Quotas relacionadas para piscinas VM

[Os lotes na Configuração de Máquina Virtual implantados numa rede virtual Azure](batch-virtual-network.md) alocam automaticamente recursos adicionais de rede Azure. Estes recursos são criados na subscrição que contém a rede virtual fornecida ao criar o pool Batch.

São criados os seguintes recursos para cada 100 nós de piscina numa rede virtual:

- Um [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Um [endereço IP público](../virtual-network/public-ip-addresses.md)
- Um [equilibrador de carga](../load-balancer/load-balancer-overview.md)

Estes recursos estão limitados pelas [quotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Se planeia grandes implantações de piscinas numa rede virtual, poderá ter de solicitar um aumento de quota para um ou mais destes recursos.

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Conheça os [limites de subscrição e serviço da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md)