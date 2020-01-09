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
ms.openlocfilehash: 443fcad17e5832a014dc463f0dd8a44d90667b75
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646460"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gerir e solicitar as quotas para recursos do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece detalhes sobre os limites pré-configurados nos recursos do Azure para sua assinatura. Também estão incluídas instruções sobre como solicitar aprimoramentos de cota para cada tipo de recurso. Estes limites são colocados em vigor para impedir que o orçamento execuções excessiva devido a fraude e que respeite as restrições de capacidade do Azure.

Assim como ocorre com outros serviços do Azure, há limites em determinados recursos associados a Azure Machine Learning. Esses limites variam de um limite no número de espaços de trabalho a limites na computação subjacente real que é usada para treinamento de modelo ou inferência/Pontuação. 

Conforme você projeta e dimensiona seus recursos de Azure Machine Learning para cargas de trabalho de produção, considere esses limites. Por exemplo, se o cluster não alcançar o número de nós de destino, você poderá ter atingido um limite de núcleos de computação Azure Machine Learning para sua assinatura. Se quiser aumentar o limite ou quota acima do limite predefinido, abra um pedido de suporte do cliente online sem encargos. Os limites não podem ser aumentados acima do valor de limite máximo mostrado nas tabelas seguintes devido a restrições de capacidade do Azure. Se não houver nenhuma coluna de limite máximo, em seguida, o recurso não tem limites ajustável.

## <a name="special-considerations"></a>Considerações especiais

+ Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure.

+ Sua cota é compartilhada entre todos os serviços em suas assinaturas, incluindo Azure Machine Learning. A única exceção é Azure Machine Learning computação que tem uma cota separada da cota de computação principal. Certifique-se de que calcular a utilização de quota em todos os serviços ao avaliar as suas necessidades de capacidade.

+ Os limites padrão variam por tipo de categoria de oferta, como avaliação gratuita, pago conforme o uso e série de VMs, como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Quotas de recursos padrão

Aqui está uma divisão os limites de quota por vários tipos de recursos na sua subscrição do Azure.

> [!Important]
> Limites estão sujeitos a alterações. A versão mais recente sempre pode ser encontrado na quota do nível de serviço [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) para todas as do Azure.

### <a name="virtual-machines"></a>Virtual Machines
Para cada assinatura do Azure, há um limite no número de máquinas virtuais que você pode ter em seus serviços ou autônomos. Este limite é no nível de região no total de núcleos e também numa base por família.

Os núcleos de máquina virtual têm um limite de total regional e um limite regional por série de tamanho (Dv2, F etc.), os quais são impostos separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região EUA Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição poderá implementar 30 VMs A1, 30 VMs D1 ou uma combinação de ambas que não excedam um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Para obter uma lista mais detalhada e atualizada de limites de quota, consulte o artigo de quota do Azure em todo [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Para Azure computação do Machine Learning, existe um limite de cota padrão sobre o número de núcleos e o número exclusivo de recursos de computação permitidas por região numa subscrição. Essa cota é separada da cota de núcleos de VM acima e os limites principais não são compartilhados entre os dois tipos de recurso, uma vez que AmlCompute é um serviço gerenciado que implanta recursos em um modelo hospedado em nome de.

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 24-300, dependendo de seu tipo de oferta de assinatura com padrões mais altos para tipos de oferta EA e CSP.  O número de núcleos dedicados por assinatura pode ser aumentado e é diferente para cada família de VM. Determinadas famílias de VMs especializadas como NCv2, NCv3 ou ND Series começam com um padrão de zero núcleos. Entre em contato com o suporte do Azure gerando uma solicitação de cota para discutir as opções de aumento.

+ Os núcleos de baixa prioridade por região têm um limite padrão de 100-3000, dependendo do tipo de oferta de assinatura com padrões mais altos para os tipos de oferta EA e CSP. O número de núcleos de baixa prioridade por assinatura pode ser aumentado e é um único valor entre as famílias de VMs. Contacte o suporte do Azure para discutir as opções de aumento.

+ Clusters por região têm um limite padrão de 200. Eles são compartilhados entre um cluster de treinamento e uma instância de computação (que é considerada como um cluster de nó único para fins de cota). Se pretender pedir um aumento que ultrapassam este limite, contacte o suporte do Azure.

+ Há * * outros limites estritos que não podem ser excedidos quando atingido.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Áreas de trabalho máximas por grupo de recursos | 800 |
| Número máximo de nós num único recurso de computação do Azure Machine Learning (AmlCompute) | 100 nós |
| Máximo de GPU de MPI processa por nó | 1 a 4 |
| Operadores de GPU máximos por nó | 1 a 4 |
| Duração de tarefas máxima | 90 dias<sup>1</sup> |
| Tempo de vida máximo de trabalho em um nó de baixa prioridade | 7 dias<sup>2</sup> |
| Servidores de parâmetro máxima por nó | 1 |

<sup>1</sup> a duração máxima refere-se até ao momento em que iniciar uma execução e quando ele for concluída. Execuções concluídas mantêm-se indefinidamente; dados de execuções não concluídas dentro da duração máxima não estão acessíveis.
<sup>2</sup> os trabalhos em um nó de baixa prioridade poderiam ser admitidos sempre que houver uma restrição de capacidade. Recomendamos que você implemente o ponto de verificação em seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Pipelines de Azure Machine Learning
Para Azure Machine Learning pipelines, há um limite de cota no número de etapas em um pipeline e no número de execuções baseadas em agendamento de pipelines publicados por região em uma assinatura.
- O número máximo de etapas permitidas em um pipeline é de 30.000
- O número máximo da soma de execuções baseadas em agendamento e pulls de BLOB para agendamentos disparados por blog de pipelines publicados por assinatura por mês é de 100.000

> [!NOTE]
> Se você quiser aumentar esse limite, entre em contato com [suporte da Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Instâncias de contentor

Também existe um limite no número de instâncias de contentor que pode acelerar num determinado período de tempo (hora a hora de âmbito) ou em sua assinatura inteira.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Para obter uma lista mais detalhada e atualizada de limites de quota, consulte o artigo de quota do Azure em todo [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
Existe um limite no número de contas de armazenamento por região também numa determinada subscrição. O limite padrão é 250 e inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 250 contas de armazenamento em uma determinada região, faça uma solicitação por meio [do suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipa do armazenamento do Azure analisará o seu caso comercial e pode aprovar até 250 contas de armazenamento para uma determinada região.


## <a name="workspace-level-quota"></a>Cota de nível de espaço de trabalho

Para gerenciar melhor as alocações de recursos para Amlcompute entre vários espaços de trabalho, apresentamos um recurso que permite distribuir cotas de nível de assinatura (por família de VM) e configurá-las no nível do espaço de trabalho. O comportamento padrão é que todos os espaços de trabalho têm a mesma cota que a cota de nível de assinatura para qualquer família de VMs. No entanto, à medida que o número de espaços de trabalho aumenta, e as cargas de trabalho de prioridade variável começam a compartilhar os mesmos recursos, os usuários desejam uma maneira de compartilhar melhor a capacidade e evitar problemas de contenção de recursos. O Azure Machine Learning fornece uma solução com sua oferta de computação gerenciada, permitindo que os usuários definam uma cota máxima para uma família de VMs específica em cada espaço de trabalho. Isso é análogo à distribuição de sua capacidade entre espaços de trabalho, e os usuários também podem optar por superalocar para aumentar a utilização máxima. 

Para definir cotas no nível do espaço de trabalho, acesse qualquer espaço de trabalho em sua assinatura e clique em **usos + cotas** no painel esquerdo. Em seguida, selecione a guia **configurar cotas** para exibir as cotas, expanda qualquer família de VMs e defina um limite de cota em qualquer espaço de trabalho listado nessa família de VMs. Lembre-se de que você não pode definir um valor negativo ou um valor maior que a cota de nível de assinatura. Além disso, como você observaria, por padrão, todos os espaços de trabalho recebem a cota de assinatura inteira para permitir a utilização total da cota alocada.

[![cota de nível de espaço de trabalho Azure Machine Learning](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Esse é apenas um recurso da Enterprise Edition. Se você tiver um espaço de trabalho básico e um Enterprise Edition em sua assinatura, poderá usá-lo para definir apenas cotas em seus espaços de trabalho corporativos. Seus espaços de trabalho básicos continuarão a ter a cota de nível de assinatura, que é o comportamento padrão.
>
> Você precisa de permissões de nível de assinatura para definir a cota no nível do espaço de trabalho. Isso é imposto para que os proprietários de espaços de trabalho individuais não editem ou aumentem suas cotas e iniciem o tranqüilamente em recursos separados para outro espaço de trabalho. Portanto, um administrador de assinatura é mais adequado para alocar e distribuir essas cotas entre espaços de trabalho.



## <a name="view-your-usage-and-quotas"></a>Exibir seu uso e cotas

Ver a sua quota para vários recursos, como máquinas virtuais, armazenamento, rede, é fácil através do portal do Azure.

1. No painel esquerdo, selecione **todos os serviços** e, em seguida, **assinaturas** na categoria geral.

1. Na lista de subscrições, selecione a subscrição cujo quota que procura.

   **Há uma limitação:** , especificamente para ver a quota de computação do Azure Machine Learning. Conforme mencionado acima, esse quota é separada da quota de computação na sua subscrição.

1. No painel esquerdo, selecione **Machine Learning serviço** e, em seguida, selecione qualquer espaço de trabalho na lista mostrada

1. No painel seguinte, sob o **suporte + resolução de problemas de seção** selecionar **utilização + quotas** para ver os limites de quota atual e o uso.

1. Selecione uma subscrição para ver os limites de quota. Lembre-se filtrar para a região que está interessado.

1. Agora você pode alternar entre uma exibição de nível de assinatura e uma exibição no nível do espaço de trabalho:
    + **Exibição de assinatura:** Isso permite que você exiba o uso da cota de núcleos por família de VMs, expandindo-a por espaço de trabalho e expandindo-a ainda mais pelos nomes de cluster reais. Essa exibição é ideal para obter rapidamente os detalhes do uso principal de uma família de VMs específica para ver a divisão por espaços de trabalho e além dos clusters subjacentes para cada um desses espaços de trabalho. A Convenção geral nessa exibição é (uso/cota), em que o uso é o número atual de núcleos expandidos e a cota é o número máximo lógico de núcleos para o qual o recurso pode ser dimensionado. Para cada **espaço de trabalho**, a cota seria a cota de nível de espaço de trabalho (conforme explicado acima), que denota o número máximo de núcleos que você pode dimensionar para uma família de VM específica. Para um **cluster** da mesma forma, a cota é, na verdade, os núcleos que correspondem ao número máximo de nós que o cluster pode dimensionar para ser definido pela propriedade max_nodes.

    + **Exibição do espaço de trabalho:** Isso permite que você exiba o uso da cota de núcleos por espaço de trabalho, expandindo-a pela família de VMs e expandindo-a ainda mais pelos nomes de cluster reais. Essa exibição é ideal para obter rapidamente os detalhes do uso principal de um espaço de trabalho específico para ver a divisão por famílias de VMs e ainda mais pelos clusters subjacentes para cada uma dessas famílias.

## <a name="request-quota-increases"></a>Aumentos de quota de pedido

Se você quiser aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) de atendimento ao cliente online sem encargos.

Os limites não podem ser gerados acima do valor de limite máximo mostrado nas tabelas. Se não houver um limite máximo, o recurso não terá limites ajustáveis. [Isso](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artigo aborda o processo de aumento de quota em mais detalhes.

Quando pedir um aumento de quota, tem de selecionar o serviço que está a pedir para gerar a quota contra, que pode ser serviços, como a quota de serviço de Machine Learning, instâncias de contentor ou quota de armazenamento. Além de Azure Machine Learning computação, você pode clicar no botão **solicitar cota** ao exibir a cota seguindo as etapas acima.

> [!NOTE]
> [As subscrições de avaliação de gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limite ou quota. Se tiver um [subscrição de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), pode atualizar para uma [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) subscrição. Para obter mais informações, consulte [atualizar avaliação gratuita do Azure para pay as you go](../billing/billing-upgrade-azure-subscription.md) e [perguntas Freqüentes da assinatura de avaliação gratuita](https://azure.microsoft.com/free/free-account-faq).
