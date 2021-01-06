---
title: Gerir recursos e quotas
titleSuffix: Azure Machine Learning
description: Conheça as quotas e limites de recursos para a Azure Machine Learning e como solicitar aumentos de quota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 1b43a1ceb1f9e098e4932b405553ae8f33e27262
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937353"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Gerir e aumentar quotas para recursos com Azure Machine Learning

O Azure utiliza limites e quotas para evitar excedentes orçamentais devido a fraudes e para honrar as restrições de capacidade do Azure. Considere estes limites à medida que escala para cargas de trabalho de produção. Neste artigo, aprende-se:

> [!div class="checklist"]
> + Limites predefinidos aos recursos Azure relacionados com a [Azure Machine Learning](overview-what-is-azure-ml.md).
> + Criar quotas ao nível do espaço de trabalho.
> + Ver as suas quotas e limites.
> + Solicitando aumentos de quota.
> + Ponto final privado e quotas de DNS.

Juntamente com a gestão de quotas, pode aprender a [planear e gerir custos para a Azure Machine Learning](concept-plan-manage-cost.md) ou aprender sobre os limites de serviço na [Azure Machine Learning.](resource-limits-quotas-capacity.md)

## <a name="special-considerations"></a>Considerações especiais

+ Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em larga escala, [contacte o suporte da Azure para aumentar a sua quota.](#request-quota-increases)

+ Uma quota é partilhada em todos os serviços nas suas subscrições, incluindo Azure Machine Learning. Calcular o uso em todos os serviços quando estiver a avaliar a capacidade.
 
  O cálculo de Aprendizagem automática Azure é uma exceção. Tem uma quota separada da quota de cálculo principal. 

+ Os limites predefinidos variam por tipo de categoria de oferta, tais como teste gratuito, pay-as-you-go e séries de máquina virtual (como Dv2, F e G).

## <a name="default-resource-quotas"></a>Quotas de recursos predefinidos

Nesta secção, você aprende sobre o padrão e limites máximos de quota para os seguintes recursos:

+ Ativos de aprendizagem automática Azure
  + Azure Machine Learning compute
  + Oleodutos Azure Machine Learning
+ Máquinas virtuais
+ Azure Container Instances
+ Storage do Azure

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. Para obter as informações mais recentes, consulte  [os limites de serviço na Azure Machine Learning](resource-limits-quotas-capacity.md).



### <a name="azure-machine-learning-assets"></a>Ativos de aprendizagem automática Azure
Os seguintes limites para os ativos aplicam-se numa base por espaço de trabalho. 

| **Recurso** | **Limite máximo** |
| --- | --- |
| Conjuntos de dados | 10 milhões |
| Execuções | 10 milhões |
| Modelos | 10 milhões|
| Artefactos | 10 milhões |

Além disso, o tempo máximo **de execução** é de 30 dias e o número máximo de **métricas registadas por corrida** é de 1 milhão.

### <a name="azure-machine-learning-compute"></a>Computação do Machine Learning
[O Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) tem um limite de quota padrão tanto no número de núcleos (dividido por cada VM Family e núcleos totais cumulativos) como no número de recursos de computação únicos permitidos por região numa subscrição. Esta quota é separada da quota-base VM enumerada na secção anterior, uma vez que se aplica apenas aos recursos de cálculo geridos da Azure Machine Learning.

[Solicitar um aumento de quota](#request-quota-increases) para aumentar os limites para várias quotas de base familiar vM, quotas e recursos essenciais de subscrição totais nesta secção.

Recursos disponíveis:
+ **Os núcleos dedicados por região** têm um limite padrão de 24 a 300, dependendo do tipo de oferta de subscrição. Pode aumentar o número de núcleos dedicados por subscrição para cada família VM. Famílias de VM especializadas como NCv2, NCv3 ou série ND começam com um padrão de núcleos zero.

+ **Os núcleos de baixa prioridade por região** têm um limite padrão de 100 a 3.000, dependendo do tipo de oferta de subscrição. O número de núcleos de baixa prioridade por subscrição pode ser aumentado e é um valor único em todas as famílias VM.

+ **Os aglomerados por região** têm um limite de incumprimento de 200. Estes são partilhados entre um cluster de treino e um caso computacional. (Uma instância computacional é considerada um agrupamento de nó único para efeitos de quota.)

> [!TIP]
> Para saber mais sobre qual a família VM a solicitar um aumento de quota, confira os [tamanhos das máquinas virtuais em Azure.](https://docs.microsoft.com/azure/virtual-machines/sizes) Por exemplo, as famílias de VM gpu começam com um "N" em seu nome de família (por exemplo. Série NCv3)

A tabela seguinte mostra limites adicionais na plataforma. Contacte a equipa de produtos da AzureML através de um bilhete de apoio **técnico** para solicitar uma exceção.

| **Recurso ou Ação** | **Limite máximo** |
| --- | --- |
| Espaços de trabalho por grupo de recursos | 800 |
| Nós num único cluster Azure Machine Learning Compute (AmlCompute) como um **pool** não ativado por comunicação (ou seja, não pode executar trabalhos de MPI) | 100 nódoas mas configurável até 65000 nosdes |
| Nós em um único passo de corrida paralelo **executado** em um cluster Azure Machine Learning Compute (AmlCompute) | 100 nóns mas configuráveis até 65000 nosdes se o seu cluster estiver configurado para escalar por cima |
| Nós em uma única configuração de **cluster** Azure Machine Learning Compute (AmlCompute) como uma piscina ativada por comunicação | 300 nódoas mas configurável até 4000 nosdes |
| Nós em uma única configuração de **cluster** Azure Machine Learning Compute (AmlCompute) como uma piscina ativada por comunicação em uma família VM habilitada a VM | 100 nosdes |
| Nós em um único MPI **executado** em um cluster Azure Machine Learning Compute (AmlCompute) | 100 nódoas mas pode ser aumentado para 300 nosdes |
| GpU MPI processos por nó | 1-4 |
| Trabalhadores da GPU por nó | 1-4 |
| Vida de trabalho | 21 dias<sup>1</sup> |
| Vida de trabalho num nó de baixa prioridade | 7 dias<sup>2</sup> |
| Servidores de parâmetros por nó | 1 |

<sup>1</sup> Duração máxima é a duração entre quando uma corrida começa e quando termina. As execuções completas persistem indefinidamente. Os dados relativos a execuções não concluídas no prazo máximo de vida não são acessíveis.
<sup>2</sup> Os empregos num nó de baixa prioridade podem ser antecipados sempre que há uma restrição de capacidade. Recomendamos que implemente postos de controlo no seu trabalho.

#### <a name="azure-machine-learning-pipelines"></a>Oleodutos Azure Machine Learning
[Os oleodutos Azure Machine Learning](concept-ml-pipelines.md) têm os seguintes limites.

| **Recurso** | **Limite** |
| --- | --- |
| Passos num pipeline | 30,000 |
| Espaços de trabalho por grupo de recursos | 800 |

### <a name="virtual-machines"></a>Máquinas virtuais
Cada subscrição do Azure tem um limite no número de máquinas virtuais em todos os serviços. Os núcleos de máquinas virtuais têm um limite total regional e um limite regional por série de tamanho. Ambos os limites são aplicados separadamente.

Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição seria autorizada a implantar 30 VMs A1, ou 30 D1 VMs, ou uma combinação dos dois que não exceda um total de 30 núcleos.

Não é possível elevar limites para máquinas virtuais acima dos valores indicados na tabela seguinte.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

Para obter mais informações, consulte [os limites de instâncias de contentores.](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)

### <a name="storage"></a>Armazenamento
O Azure Storage tem um limite de 250 contas de armazenamento por região, por subscrição. Este limite inclui contas de armazenamento Standard e Premium.

Para aumentar o limite, faça um pedido através [do Suporte Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) A equipa de Armazenamento Azure irá rever o seu caso e pode aprovar até 250 contas de armazenamento para uma região.


## <a name="workspace-level-quotas"></a>Quotas ao nível do espaço de trabalho

Utilize quotas de nível de trabalho para gerir a alocação de destino de cálculo Azure Machine Learning entre [vários espaços de trabalho](concept-workspace.md) na mesma subscrição.

Por padrão, todos os espaços de trabalho partilham a mesma quota que a quota de nível de subscrição para as famílias VM. No entanto, pode definir uma quota máxima para famílias VM individuais em espaços de trabalho numa subscrição. Isto permite-lhe partilhar a capacidade e evitar problemas de contenção de recursos.

1. Vá a qualquer espaço de trabalho na sua subscrição.
1. No painel esquerdo, selecione **Usage + quotas**.
1. Selecione o **separador quotas configurar** para visualizar as quotas.
1. Expandir uma família VM.
1. Estabeleça um limite de quota em qualquer espaço de trabalho listado na família VM.

Não é possível definir um valor negativo ou um valor superior ao do nível de subscrição.

[![Screenshot que mostra uma quota de nível de espaço de trabalho Azure Machine Learning.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Precisa de permissões de nível de subscrição para definir uma quota ao nível do espaço de trabalho.

## <a name="view-your-usage-and-quotas"></a>Veja o seu uso e quotas

Para ver a sua quota para vários recursos Azure, como máquinas virtuais, armazenamento ou rede, utilize o portal Azure:

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Subscrições** na categoria **Geral.**

2. Na lista de subscrições, selecione a subscrição cuja quota procura.

3. Selecione **Use + quotas** para ver os seus limites de quota e utilização atuais. Utilize os filtros para selecionar o fornecedor e as localizações. 

Gere a quota de cálculo Azure Machine Learning na sua subscrição separadamente de outras quotas Azure: 

1. Vá ao seu espaço de trabalho **Azure Machine Learning** no portal Azure.

2. No painel esquerdo, na secção **Suporte + resolução de problemas,** selecione **Usage + quotas** para ver os seus limites de quota e utilização atuais.

3. Selecione uma subscrição para ver os limites de quota. Filtre para a região que lhe interessa.

4. Pode alternar entre uma vista de nível de subscrição e uma vista de nível de espaço de trabalho.

## <a name="request-quota-increases"></a>Pedir aumentos de quota

Para elevar o limite ou quota acima do limite de incumprimento, [abra gratuitamente um pedido de apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) ao cliente online.

Não é possível elevar os limites acima dos valores máximos indicados nas tabelas anteriores. Se não houver limite máximo, não pode ajustar o limite para o recurso.

Quando pedir um aumento de quota, selecione o serviço que tem em mente. Por exemplo, selecione Azure Machine Learning, Container Instances ou Storage. Para o cálculo Azure Machine Learning, pode selecionar o botão **Quota de Pedido** enquanto visualiza a quota nos passos anteriores.

> [!NOTE]
> [As assinaturas de ensaios gratuitos](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limites ou de quotas. Se tiver uma subscrição de teste gratuita, pode fazer upgrade para uma subscrição [pay-as-you-go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Para mais informações, consulte [o upgrade Azure free trial para pay-as-you-go](../cost-management-billing/manage/upgrade-azure-subscription.md) e [Azure free account FAQ](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Aumentos de quota de ponto final privado e DNS privado

Existem limites no número de pontos finais privados e zonas privadas de DNS que pode criar numa subscrição.

O Azure Machine Learning cria recursos na sua subscrição (cliente), mas alguns cenários criam recursos numa subscrição detida pela Microsoft.

 Nos seguintes cenários, poderá ter de solicitar um subsídio de quota na subscrição detida pela Microsoft:

* Azure Private Link viabilizou espaço de trabalho com uma chave gerida pelo cliente (CMK)
* Azure Container Registry para a área de trabalho por detrás da rede virtual
* Anexar um cluster do Azure Kubernetes Service preparado para o Private Link à área de trabalho

Para solicitar um subsídio para estes cenários, utilize as seguintes etapas:

1. [Crie um pedido de suporte Azure](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) e selecione as seguintes opções na secção __Básico:__

    | Campo | Seleção |
    | ----- | ----- |
    | Tipo de problema | **Parte Técnica** |
    | Serviço | **Os meus serviços.** Em seguida, selecione __Machine Learning__ na lista de drop-down. |
    | Tipo de problema | **Configuração e Segurança do Espaço de Trabalho** |
    | Subtipo de problema | **Pedido de subsídio de subsídio de Endpoint Privado e DNS Zone privado** |

2. Na secção __Detalhes,__ utilize o campo __Descrição__ para fornecer a região de Azure e o cenário que pretende utilizar. Se precisar de solicitar aumentos de quota para várias subscrições, liste os IDs de subscrição neste campo.

3. Selecione __Criar__ para criar o pedido.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Screenshot de um ponto final privado e pedido de aumento de quota de DNS privado.":::

## <a name="next-steps"></a>Passos seguintes

+ [Planear e gerir os custos para a Azure Machine Learning](concept-plan-manage-cost.md)
+ [Limites de serviço na Aprendizagem automática Azure](resource-limits-quotas-capacity.md)
