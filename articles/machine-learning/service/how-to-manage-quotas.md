---
title: Gerenciar recursos & cotas
titleSuffix: Azure Machine Learning
description: Saiba mais sobre as cotas de recursos para Azure Machine Learning e como solicitar mais cotas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 11/04/2019
ms.openlocfilehash: 2206afc45c7ea35c41f51839b66dca33982939ae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489810"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gerenciar e solicitar cotas para recursos do Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece detalhes sobre os limites pré-configurados nos recursos do Azure para sua assinatura. Também estão incluídas instruções sobre como solicitar aprimoramentos de cota para cada tipo de recurso. Esses limites são colocados em vigor para evitar as execuções de orçamento por causa de fraudes e para honrar as restrições de capacidade do Azure.

Assim como ocorre com outros serviços do Azure, há limites em determinados recursos associados a Azure Machine Learning. Esses limites variam de um limite no número de espaços de trabalho a limites na computação subjacente real que é usada para treinamento de modelo ou inferência/Pontuação. 

Conforme você projeta e dimensiona seus recursos de Azure Machine Learning para cargas de trabalho de produção, considere esses limites. Por exemplo, se o cluster não alcançar o número de nós de destino, você poderá ter atingido um limite de núcleos de computação Azure Machine Learning para sua assinatura. Se você quiser aumentar o limite ou a cota acima do limite padrão, abra uma solicitação de atendimento ao cliente online sem encargos. Os limites não podem ser gerados acima do valor de limite máximo mostrado nas tabelas a seguir devido a restrições de capacidade do Azure. Se não houver nenhuma coluna de limite máximo, o recurso não terá limites ajustáveis.

## <a name="special-considerations"></a>Considerações especiais

+ Uma cota é um limite de crédito, não uma garantia de capacidade. Se você tiver necessidades de capacidade em grande escala, entre em contato com o suporte do Azure.

+ Sua cota é compartilhada entre todos os serviços em suas assinaturas, incluindo Azure Machine Learning. A única exceção é Azure Machine Learning computação que tem uma cota separada da cota de computação principal. Certifique-se de calcular o uso de cotas em todos os serviços ao avaliar suas necessidades de capacidade.

+ Os limites padrão variam por tipo de categoria de oferta, como avaliação gratuita, pago conforme o uso e série de VMs, como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Cotas de recursos padrão

Aqui está uma análise dos limites de cota por vários tipos de recursos em sua assinatura do Azure.

> [!Important]
> Os limites estão sujeitos a alterações. O mais recente sempre pode ser encontrado no [documento](https://docs.microsoft.com/azure/azure-subscription-service-limits/) de cota de nível de serviço para todo o Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Para cada assinatura do Azure, há um limite no número de máquinas virtuais que você pode ter em seus serviços ou autônomos. Esse limite está no nível de região nos núcleos totais e também em uma base por família.

Os núcleos de máquina virtual têm um limite de total regional e um limite regional por série de tamanho (Dv2, F etc.), os quais são impostos separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região EUA Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição poderá implementar 30 VMs A1, 30 VMs D1 ou uma combinação de ambas que não excedam um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Para obter uma lista mais detalhada e atualizada de limites de cota, verifique o artigo sobre a cota de todo o Azure [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Computação Azure Machine Learning
Para Azure Machine Learning computação, há um limite de cota padrão no número de núcleos e no número de recursos de computação exclusivos permitidos por região em uma assinatura. Essa cota é separada da cota de núcleos de VM acima e os limites principais não são compartilhados entre os dois tipos de recurso, uma vez que AmlCompute é um serviço gerenciado que implanta recursos em um modelo hospedado em nome de.

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 24-300, dependendo de seu tipo de oferta de assinatura com padrões mais altos para tipos de oferta EA e CSP.  O número de núcleos dedicados por assinatura pode ser aumentado e é diferente para cada família de VM. Determinadas famílias de VMs especializadas como NCv2, NCv3 ou ND Series começam com um padrão de zero núcleos. Entre em contato com o suporte do Azure gerando uma solicitação de cota para discutir as opções de aumento.

+ Os núcleos de baixa prioridade por região têm um limite padrão de 100-3000, dependendo do tipo de oferta de assinatura com padrões mais altos para os tipos de oferta EA e CSP. O número de núcleos de baixa prioridade por assinatura pode ser aumentado e é um único valor entre as famílias de VMs. Contate o suporte do Azure para discutir as opções de aumento.

+ Clusters por região têm um limite padrão de 200. Eles são compartilhados entre um cluster de treinamento e uma instância de computação (que é considerada como um cluster de nó único para fins de cota). Entre em contato com o suporte do Azure se desejar solicitar um aumento além desse limite.

+ Há * * outros limites estritos que não podem ser excedidos quando atingido.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Máximo de espaços de trabalho por grupo de recursos | 800 |
| Máximo de nós em um único recurso de AmlCompute (computação de Azure Machine Learning) | nós de 100 |
| Máximo de processos MPI de GPU por nó | 1-4 |
| Máximo de trabalhos de GPU por nó | 1-4 |
| Tempo de vida máximo do trabalho | 90 dias<sup>1</sup> |
| Tempo de vida máximo de trabalho em um nó de baixa prioridade | 7 dias<sup>2</sup> |
| Máximo de servidores de parâmetro por nó | 1 |

<sup>1</sup> o tempo de vida máximo refere-se à hora em que uma execução é iniciada e quando ela é concluída. Execuções concluídas são mantidas indefinidamente; os dados para execuções não concluídas no tempo de vida máximo não estão acessíveis.
<sup>2</sup> os trabalhos em um nó de baixa prioridade poderiam ser admitidos sempre que houver uma restrição de capacidade. Recomendamos que você implemente o ponto de verificação em seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Pipelines de Azure Machine Learning
Para Azure Machine Learning pipelines, há um limite de cota no número de etapas em um pipeline e no número de execuções baseadas em agendamento de pipelines publicados por região em uma assinatura.
- O número máximo de etapas permitidas em um pipeline é de 30.000
- O número máximo da soma de execuções baseadas em agendamento e pulls de BLOB para agendamentos disparados por blog de pipelines publicados por assinatura por mês é de 100.000

> [!NOTE]
> Se você quiser aumentar esse limite, entre em contato com [suporte da Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Instâncias de contêiner

Também há um limite no número de instâncias de contêiner que você pode girar em um determinado período de tempo (com escopo de hora) ou em toda a sua assinatura.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Para obter uma lista mais detalhada e atualizada de limites de cota, verifique o artigo sobre a cota de todo o Azure [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
Há um limite no número de contas de armazenamento por região também em uma determinada assinatura. O limite padrão é 200 e inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento em uma determinada região, faça uma solicitação por meio [do suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipe de armazenamento do Azure analisará seu caso comercial e poderá aprovar até 250 contas de armazenamento para uma determinada região.


## <a name="workspace-level-quota"></a>Cota de nível de espaço de trabalho

Para gerenciar melhor as alocações de recursos para Amlcompute entre vários espaços de trabalho, apresentamos um recurso que permite distribuir cotas de nível de assinatura (por família de VM) e configurá-las no nível do espaço de trabalho. O comportamento padrão é que todos os espaços de trabalho têm a mesma cota que a cota de nível de assinatura para qualquer família de VMs. No entanto, à medida que o número de espaços de trabalho aumenta, e as cargas de trabalho de prioridade variável começam a compartilhar os mesmos recursos, os usuários desejam uma maneira de compartilhar melhor a capacidade e evitar problemas de contenção de recursos. O Azure Machine Learning fornece uma solução com sua oferta de computação gerenciada, permitindo que os usuários definam uma cota máxima para uma família de VMs específica em cada espaço de trabalho. Isso é análogo à distribuição de sua capacidade entre espaços de trabalho, e os usuários também podem optar por superalocar para aumentar a utilização máxima. 

Para definir cotas no nível do espaço de trabalho, acesse qualquer espaço de trabalho em sua assinatura e clique em **usos + cotas** no painel esquerdo. Em seguida, selecione a guia **configurar cotas** para exibir as cotas, expanda qualquer família de VMs e defina um limite de cota em qualquer espaço de trabalho listado nessa família de VMs. Lembre-se de que você não pode definir um valor negativo ou um valor maior que a cota de nível de assinatura. Além disso, como você observaria, por padrão, todos os espaços de trabalho recebem a cota de assinatura inteira para permitir a utilização total da cota alocada.

[![cota de nível de espaço de trabalho Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Esse é apenas um recurso da Enterprise Edition. Se você tiver um espaço de trabalho básico e um Enterprise Edition em sua assinatura, poderá usá-lo para definir apenas cotas em seus espaços de trabalho corporativos. Seus espaços de trabalho básicos continuarão a ter a cota de nível de assinatura, que é o comportamento padrão.
>
> Você precisa de permissões de nível de assinatura para definir a cota no nível do espaço de trabalho. Isso é imposto para que os proprietários de espaços de trabalho individuais não editem ou aumentem suas cotas e iniciem o tranqüilamente em recursos separados para outro espaço de trabalho. Portanto, um administrador de assinatura é mais adequado para alocar e distribuir essas cotas entre espaços de trabalho.



## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas

Exibir sua cota para vários recursos, como máquinas virtuais, armazenamento, rede, é fácil por meio do portal do Azure.

1. No painel esquerdo, selecione **todos os serviços** e, em seguida, **assinaturas** na categoria geral.

1. Na lista de assinaturas, selecione a assinatura cuja cota que você está procurando.

   **Há uma limitação**, especificamente para exibir a cota de computação Azure Machine Learning. Conforme mencionado acima, essa cota é separada da cota de computação em sua assinatura.

1. No painel esquerdo, selecione **Machine Learning serviço** e, em seguida, selecione qualquer espaço de trabalho na lista mostrada

1. Na próxima folha, na **seção suporte + solução de problemas** , selecione **uso + cotas** para exibir os limites e o uso da cota atual.

1. Selecione uma assinatura para exibir os limites de cota. Lembre-se de filtrar para a região em que você está interessado.

1. Agora você pode alternar entre uma exibição de nível de assinatura e uma exibição no nível do espaço de trabalho:
    + **Exibição de assinatura:** Isso permite que você exiba o uso da cota de núcleos por família de VMs, expandindo-a por espaço de trabalho e expandindo-a ainda mais pelos nomes de cluster reais. Essa exibição é ideal para obter rapidamente os detalhes do uso principal de uma família de VMs específica para ver a divisão por espaços de trabalho e além dos clusters subjacentes para cada um desses espaços de trabalho. A Convenção geral nessa exibição é (uso/cota), em que o uso é o número atual de núcleos expandidos e a cota é o número máximo lógico de núcleos para o qual o recurso pode ser dimensionado. Para cada **espaço de trabalho**, a cota seria a cota de nível de espaço de trabalho (conforme explicado acima), que denota o número máximo de núcleos que você pode dimensionar para uma família de VM específica. Para um **cluster** da mesma forma, a cota é, na verdade, os núcleos que correspondem ao número máximo de nós que o cluster pode dimensionar para ser definido pela propriedade max_nodes.

    + **Exibição do espaço de trabalho:** Isso permite que você exiba o uso da cota de núcleos por espaço de trabalho, expandindo-a pela família de VMs e expandindo-a ainda mais pelos nomes de cluster reais. Essa exibição é ideal para obter rapidamente os detalhes do uso principal de um espaço de trabalho específico para ver a divisão por famílias de VMs e ainda mais pelos clusters subjacentes para cada uma dessas famílias.

## <a name="request-quota-increases"></a>Aumento da cota de solicitação

Se você quiser aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) de atendimento ao cliente online sem encargos.

Os limites não podem ser gerados acima do valor de limite máximo mostrado nas tabelas. Se não houver um limite máximo, o recurso não terá limites ajustáveis. [Este](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artigo aborda o processo de aumento de cota em mais detalhes.

Ao solicitar um aumento de cota, você precisa selecionar o serviço ao qual você está solicitando a cota, que poderia ser serviços como Machine Learning cota de serviço, instâncias de contêiner ou cota de armazenamento. Além de Azure Machine Learning computação, você pode clicar no botão **solicitar cota** ao exibir a cota seguindo as etapas acima.

> [!NOTE]
> As [assinaturas de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para aumentos de cota ou limite. Se você tiver uma [assinatura de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), poderá atualizar para uma assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) . Para obter mais informações, consulte [atualizar a avaliação gratuita do Azure para o pagamento conforme o uso](../../billing/billing-upgrade-azure-subscription.md) e as [perguntas frequentes da assinatura de avaliação gratuita](https://azure.microsoft.com/free/free-account-faq).