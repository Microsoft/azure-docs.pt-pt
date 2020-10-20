---
title: Gerir quotas de & recursos
titleSuffix: Azure Machine Learning
description: Conheça as quotas sobre recursos para a Azure Machine Learning e como solicitar mais quota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 93cf29651110356023752f2377c9a0fc358982a7
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203058"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gerir & aumentar quotas de recursos com a Azure Machine Learning

O Azure utiliza limites e quotas para evitar excedentes orçamentais devido a fraudes e para honrar as restrições de capacidade do Azure. Considere estes limites à medida que escala para cargas de trabalho de produção. Neste artigo aprende-se sobre:

> [!div class="checklist"]
> + Limites predefinidos aos recursos Azure relacionados com a [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Veja as suas quotas e limites.
> + Criar quotas de nível de espaço de trabalho.
> + O pedido de quota aumenta.
> + Ponto final privado e quotas de DNS.

Juntamente com a gestão de quotas, você também pode aprender a [planear & gerir custos para Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerações especiais

+ Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em larga escala, [contacte o suporte da Azure para aumentar a sua quota.](#request-quota-increases)

+ A quota é partilhada em todos os serviços das suas subscrições, incluindo Azure Machine Learning. Calcular a utilização em todos os serviços ao avaliar a capacidade.
    + O Azure Machine Learning Compute é uma exceção, e tem uma quota separada da quota de cálculo principal. 

+ Os limites predefinidos variam de acordo com a oferta Categoria Tipo, tais como Free Trial, Pay-As-You-Go, e séries de máquinas virtuais (VM), tais como Dv2, F, G, e assim por diante.

## <a name="default-resource-quotas"></a>Quotas de recursos predefinidos

Nesta secção, você aprende sobre o padrão e limites máximos de quota para os seguintes recursos:

+ Máquinas virtuais
+ Computação do Machine Learning
+ Oleodutos Azure Machine Learning
+ Container Instances
+ Armazenamento

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. As últimas podem sempre ser encontradas no [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) de quota de nível de serviço para toda a Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Cada subscrição do Azure tem um limite no número de máquinas virtuais em todos os serviços. Os núcleos de máquinas virtuais têm um limite total regional e um limite regional por série de tamanho (Dv2, F, etc.). Ambos os limites são aplicados separadamente.

Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição seria autorizada a implantar 30 VMs A1, ou 30 D1 VMs, ou uma combinação dos dois que não exceda um total de 30 núcleos.

Os limites para máquinas virtuais não podem ser aumentados acima do valor indicado no quadro seguinte.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Computação do Machine Learning
[O Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) tem um limite de quota padrão tanto no número de núcleos como no número de recursos de computação únicos permitidos por região numa subscrição. Esta quota é separada da quota-base VM da secção anterior.

[Solicito um aumento do contingente](#request-quota-increases) para elevar os limites desta secção até ao limite **máximo** indicado na tabela.

Recursos disponíveis:
+ **Os núcleos dedicados por região** têm um limite padrão de 24 - 300 dependendo do tipo de oferta de subscrição.  O número de núcleos dedicados por subscrição pode ser aumentado para cada família VM. Famílias de VM especializadas como NCv2, NCv3 ou série ND começam com um padrão de núcleos zero.

+ **Os núcleos de baixa prioridade por região** têm um limite padrão de 100 - 3000 dependendo do tipo de oferta de subscrição. O número de núcleos de baixa prioridade por subscrição pode ser aumentado e é um valor único em todas as famílias VM.

+ **Os aglomerados por região** têm um limite de incumprimento de 200. Estes são partilhados entre um cluster de formação e um caso computacional (que é considerado como um único aglomerado de nó para efeitos de quota).

O quadro seguinte mostra limites adicionais que não podem ser ultrapassados.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Espaços de trabalho por grupo de recursos | 800 |
| Nós num único recurso Azure Machine Learning Compute (AmlCompute) | 100 nosdes |
| GpU MPI processos por nó | 1-4 |
| Trabalhadores da GPU por nó | 1-4 |
| Vida de trabalho | 21 dias<sup>1</sup> |
| Vida de trabalho num nó Low-Priority | 7 dias<sup>2</sup> |
| Servidores de parâmetros por nó | 1 |

<sup>1</sup> Duração máxima refere-se à duração entre quando uma corrida começa e termina. As execuções completas persistem indefinidamente. Os dados relativos a execuções não concluídas no prazo máximo de vida não são acessíveis.
<sup>2</sup> Os postos de trabalho num nó Low-Priority podem ser antecipados sempre que houver uma restrição de capacidade. Recomendamos que implemente pontos de verificação no seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Gasodutos de aprendizagem de máquinas Azure
[Os gasodutos de aprendizagem de máquinas Azure](concept-ml-pipelines.md) têm os seguintes limites.

| **Recurso** | **Limite** |
| --- | --- |
| Passos num pipeline | 30,000 |
| Espaços de trabalho por grupo de recursos | 800 |

### <a name="container-instances"></a>Instâncias de contentores

Para obter mais informações, consulte [os limites de instâncias de contentores.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)

### <a name="storage"></a>Armazenamento
As contas de armazenamento Azure têm um limite de 250 contas de armazenamento por região, por subscrição. Isto inclui contas standard e premium storage.

Para aumentar o limite, faça um pedido através [do Suporte Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) A equipa de Armazenamento Azure irá rever o seu caso e poderá aprovar até 250 contas de armazenamento para uma região.


## <a name="workspace-level-quota"></a>Quota de nível de espaço de trabalho

Utilize quotas de nível de espaço de trabalho para gerir a alocação de destino do Azure Machine Learning Compute entre [vários espaços de trabalho](concept-workspace.md) na mesma subscrição.

Por padrão, todos os espaços de trabalho partilham a mesma quota que a quota de nível de subscrição para as famílias VM. No entanto, pode definir uma quota máxima para famílias VM individuais em espaços de trabalho numa subscrição. Isto permite-lhe partilhar a capacidade e evitar problemas de contenção de recursos:

1. Navegue para qualquer espaço de trabalho na sua subscrição.
1. No painel esquerdo, selecione **Usage + quotas**.
1. Selecione o **separador quotas configurar** para visualizar as quotas.
1. Expandir uma família VM.
1. Estabeleça um limite de quota em qualquer espaço de trabalho listado na família VM.

Não é possível definir um valor negativo ou um valor superior ao do contingente de nível de subscrição.

[![Quota de nível de espaço de trabalho de aprendizagem de máquina de Azure](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Precisa de permissões de nível de subscrição para definir a quota ao nível do espaço de trabalho.

## <a name="view-your-usage-and-quotas"></a>Veja o seu uso e quotas

Para ver a sua quota para vários recursos Azure como Máquinas Virtuais, Armazenamento, Rede, utilize o portal Azure:

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Subscrições** na categoria Geral.

2. Na lista de subscrições, selecione a subscrição cuja quota procura.

3. Selecione **Use + quotas** para ver os seus limites de quota e utilização atuais. Utilize os filtros para selecionar o fornecedor e as localizações. 

A quota Azure Machine Learning Compute na sua subscrição é gerida separadamente de outras quotas Azure. 

1. Navegue para o seu espaço de trabalho **Azure Machine Learning** no portal Azure.

2. No painel esquerdo, na **secção Support + resolução de problemas** selecione Usage + **quotas** para ver os seus limites de quota e utilização atuais.

3. Selecione uma subscrição para ver os limites de quota. Lembre-se de filtrar para a região que lhe interessa.

4. Pode alternar entre uma vista de nível de subscrição e uma vista de nível de espaço de trabalho.

## <a name="request-quota-increases"></a>Pedir aumentos de quota

Para elevar o limite ou quota acima do limite de incumprimento, [abra gratuitamente um pedido de apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) ao cliente online.

Os limites não podem ser elevados acima do valor limite máximo indicado nas tabelas acima. Se não houver limite máximo, não é possível ajustar o limite para o recurso.

Ao solicitar um aumento de quota, selecione o serviço que está a solicitar para aumentar a quota contra. Por exemplo, Aprendizagem automática Azure, Casos de Contentores, Armazenamento, etc. Para Azure Machine Learning Compute, pode selecionar o botão **Quota de Pedido** enquanto visualiza a quota seguindo os passos acima.

> [!NOTE]
> [As assinaturas de teste gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limites ou de quotas. Se tiver uma [subscrição de Teste Gratuito,](https://azure.microsoft.com/offers/ms-azr-0044p)pode fazer upgrade para uma subscrição [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Para obter mais informações, consulte [upgrade Azure Free Trial para Pay-As-You-Go](../billing/billing-upgrade-azure-subscription.md) e  [subscrição de teste gratuito FAQ](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Fim privado e quota privada de DNS aumentam

Existem limitações no número de pontos finais privados e zonas privadas de DNS que podem ser criadas numa subscrição.

Embora o Azure Machine Learning crie recursos na sua subscrição (cliente), existem alguns cenários que criam recursos numa subscrição detida pela Microsoft.

 Nos seguintes cenários, poderá ter de solicitar um subsídio de quota na subscrição detida pela Microsoft:

* __Private Link viabilização espaço de trabalho com uma chave gerida pelo cliente (CMK)__
* __Registo de contentores Azure para o espaço de trabalho por trás da sua rede virtual__
* __Anexação de um cluster de serviço Azure Kubernetes ativado ao seu espaço de trabalho.__

Para solicitar um subsídio para estes cenários, utilize as seguintes etapas:

1. [Crie um pedido de suporte Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) e selecione as seguintes opções na secção __Básico:__

    | Campo | Seleção |
    | ----- | ----- |
    | Tipo de problema | Parte Técnica |
    | Serviço | Os meus serviços. Selecione __Machine Learning__ na lista de dropdown. |
    | Tipo de problema | Configuração do espaço de trabalho, SDK e CLI |
    | Subtipo de problema | Pedido de subsídio de subsídio de Endpoint Privado e DNS Zone privado |

2. A partir da secção __Detalhes,__ utilize o campo __Descrição__ para fornecer a região Azure que pretende utilizar e o cenário que pretende utilizar. Se precisar de solicitar aumentos de quota para várias subscrições, liste também os IDs de subscrição neste campo.

3. Selecione __Criar__ para criar o pedido.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Screenshot de um ponto final privado e pedido de aumento de quota de DNS privado":::

## <a name="next-steps"></a>Passos seguintes

+ [Plano & gerir custos para a Azure Machine Learning](concept-plan-manage-cost.md)
