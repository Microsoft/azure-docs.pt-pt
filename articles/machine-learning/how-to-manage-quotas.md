---
title: Gerir recursos e quotas
titleSuffix: Azure Machine Learning
description: Conheça as quotas de recursos para o Azure Machine Learning e como solicitar mais quota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399161"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gerir e solicitar as quotas para recursos do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece detalhes sobre os limites pré-configurados dos recursos do Azure para a sua subscrição. Também estão incluídas instruções sobre como solicitar melhorias de quotas para cada tipo de recurso. Estes limites são colocados em vigor para impedir que o orçamento execuções excessiva devido a fraude e que respeite as restrições de capacidade do Azure.

Tal como acontece com outros serviços azure, existem limites em certos recursos associados à Aprendizagem automática Azure. Estes limites vão desde um limite no número de espaços de trabalho até limites à computação subjacente real que é usada para treino de modelos ou inferência/pontuação. 

À medida que projeta e dimensiona os seus recursos de Aprendizagem automática Azure para cargas de trabalho de produção, considere estes limites. Por exemplo, se o seu cluster não atingir o número de nós alvo, então pode ter atingido um limite de núcleos azure machine learning compute para a sua subscrição. Se quiser aumentar o limite ou quota acima do limite predefinido, abra um pedido de suporte do cliente online sem encargos. Os limites não podem ser aumentados acima do valor de limite máximo mostrado nas tabelas seguintes devido a restrições de capacidade do Azure. Se não houver nenhuma coluna de limite máximo, em seguida, o recurso não tem limites ajustável.

## <a name="special-considerations"></a>Considerações especiais

+ Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure.

+ A sua quota é partilhada em todos os serviços das suas subscrições, incluindo o Azure Machine Learning. A única exceção é o cálculo Azure Machine Learning que tem uma quota separada da quota de computação central. Certifique-se de que calcular a utilização de quota em todos os serviços ao avaliar as suas necessidades de capacidade.

+ Os limites padrão variam consoante a categoria de oferta Tipo, tais como Teste Gratuito, Pay-As-You-Go e séries VM, tais como Dv2, F, G, e assim por diante.

## <a name="default-resource-quotas"></a>Quotas de recursos padrão

Aqui está uma divisão os limites de quota por vários tipos de recursos na sua subscrição do Azure.

> [!IMPORTANT]
> Limites estão sujeitos a alterações. O mais recente pode ser sempre encontrado no [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) de quota de nível de serviço para todo o Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Para cada subscrição do Azure, existe um limite no número de máquinas virtuais que pode ter em todos os seus serviços ou autónomas. Este limite é no nível de região no total de núcleos e também numa base por família.

Os núcleos de máquinas virtuais têm um limite total regional e uma série regional por tamanho (Dv2, F, etc.), ambas executadas separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição poderá implementar 30 VMs A1, 30 VMs D1 ou uma combinação de ambas que não excedam um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Para obter uma lista mais pormenorizada e atualizada dos limites das quotas, consulte [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)o artigo de quotas em todo o Azure.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Para Azure computação do Machine Learning, existe um limite de cota padrão sobre o número de núcleos e o número exclusivo de recursos de computação permitidas por região numa subscrição. Esta quota é separada da quota central vm acima e os limites de base não são partilhados entre os dois tipos de recursos, uma vez que a AmlCompute é um serviço gerido que utiliza recursos num modelo hospedado em nome de um modelo.

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 24 - 300 dependendo do seu tipo de oferta de subscrição com incumprimentos mais elevados para tipos de oferta EA e CSP.  O número de núcleos dedicados por subscrição pode ser aumentado e é diferente para cada família VM. Algumas famílias de VM especializadas como nCv2, NCv3 ou série ND começam com um padrão de zero núcleos. Contacte o apoio do Azure levantando um pedido de quota para discutir o aumento de opções.

+ Os núcleos de baixa prioridade por região têm um limite de padrão de 100 - 3000 dependendo do seu tipo de oferta de subscrição com incumprimentos mais elevados para tipos de oferta saem eA e CSP. O número de núcleos de baixa prioridade por subscrição pode ser aumentado e é um valor único entre as famílias VM. Contacte o suporte do Azure para discutir as opções de aumento.

+ Os aglomerados por região têm um limite de incumprimento de 200. Estes são partilhados entre um cluster de formação e um exemplo de cálculo (que é considerado como um único cluster de nó para fins de quota). Se pretender pedir um aumento que ultrapassam este limite, contacte o suporte do Azure.

+ Existem outros limites estritos que não podem ser ultrapassados uma vez atingidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Áreas de trabalho máximas por grupo de recursos | 800 |
| Número máximo de nós num único recurso de computação do Azure Machine Learning (AmlCompute) | 100 nós |
| Máximo de GPU de MPI processa por nó | 1 a 4 |
| Operadores de GPU máximos por nó | 1 a 4 |
| Duração de tarefas máxima | 90 dias<sup>1</sup> |
| Vida útil máxima em nó de baixa prioridade | 7 dias<sup>2</sup> |
| Servidores de parâmetro máxima por nó | 1 |

<sup>1</sup> A duração máxima refere-se ao tempo que uma corrida começa e quando termina. Execuções concluídas mantêm-se indefinidamente; dados de execuções não concluídas dentro da duração máxima não estão acessíveis.
<sup>2</sup> Os postos de trabalho num nó de baixa prioridade podem ser antecipados sempre que houver uma restrição de capacidade. Recomendamos que implemente o checkpoint ingcheckpoint no seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Gasodutos de Aprendizagem automática Azure
No caso dos gasodutos de aprendizagem automática Azure, existe um limite de quota para o número de etapas num gasoduto e sobre o número de operações programadas de gasodutos publicados por região numa subscrição.
- Número máximo de etapas permitidas num oleoduto é de 30.000
- O número máximo da soma de execuções baseadas em horários e pulls blob para os horários desencadeados por blog de oleodutos publicados por subscrição por mês é de 100.000

> [!NOTE]
> Se quiser aumentar este limite, contacte o Suporte da [Microsoft](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Instâncias de contentor

Também existe um limite no número de instâncias de contentor que pode acelerar num determinado período de tempo (hora a hora de âmbito) ou em sua assinatura inteira.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Para obter uma lista mais pormenorizada e atualizada dos limites das quotas, consulte [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)o artigo de quotas em todo o Azure.

### <a name="storage"></a>Armazenamento
Existe um limite no número de contas de armazenamento por região também numa determinada subscrição. O limite de predefinição é de 250 e inclui contas standard e premium de armazenamento. Se necessitar de mais de 250 contas de armazenamento numa determinada região, faça um pedido através do [Suporte Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) A equipa do armazenamento do Azure analisará o seu caso comercial e pode aprovar até 250 contas de armazenamento para uma determinada região.


## <a name="workspace-level-quota"></a>Quota de nível de espaço de trabalho

Para gerir melhor as alocações de recursos para a Amlcompute entre vários espaços de trabalho, introduzimos uma funcionalidade que lhe permite distribuir quotas de nível de subscrição (por família VM) e configurá-las ao nível do espaço de trabalho. O comportamento padrão é que todos os espaços de trabalho têm a mesma quota que a quota de nível de subscrição para qualquer família VM. No entanto, à medida que o número de espaços de trabalho aumenta, e as cargas de trabalho de diferentes prioridades começam a partilhar os mesmos recursos, os utilizadores querem uma forma de partilhar melhor a capacidade e evitar problemas de contenção de recursos. O Azure Machine Learning fornece uma solução com a sua oferta de cálculo gerida, permitindo que os utilizadores estabeleçam uma quota máxima para uma determinada família VM em cada espaço de trabalho. Isto é análogo à distribuição da sua capacidade entre espaços de trabalho, e os utilizadores podem optar por alocar demasiado para impulsionar a máxima utilização. 

Para definir quotas ao nível do espaço de trabalho, vá a qualquer espaço de trabalho na sua subscrição e clique em **Usages + quotas** no painel esquerdo. Em seguida, selecione o separador **de quotas Configure** para visualizar as quotas, expandir qualquer família VM, e definir um limite de quota em qualquer espaço de trabalho listado sob a família VM. Lembre-se de que não pode definir um valor negativo ou um valor superior ao contingente de nível de subscrição. Além disso, como observaria, por padrão, todos os espaços de trabalho são atribuídos a toda a quota de subscrição para permitir a utilização total da quota atribuída.

[quota de nível de nível de espaço de trabalho de aprendizagem automática de ![](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Esta é apenas uma funcionalidade de edição da Enterprise. Se tiver um espaço de trabalho básico e uma edição da Enterprise na sua subscrição, pode usá-lo apenas para definir quotas nos seus espaços de trabalho da Enterprise. Os seus espaços de trabalho Básicos continuarão a ter a quota de nível de subscrição que é o comportamento padrão.
>
> Precisa de permissões de nível de subscrição para definir quota ao nível do espaço de trabalho. Isto é aplicado para que os proprietários individuais de espaço de trabalho não editem ou aumentem as suas quotas e comecem a invadir os recursos reservados para outro espaço de trabalho. Assim, um administrador de subscrição é mais adequado para alocar e distribuir estas quotas por espaços de trabalho.



## <a name="view-your-usage-and-quotas"></a>Veja o seu uso e quotas

Ver a sua quota para vários recursos, como máquinas virtuais, armazenamento, rede, é fácil através do portal do Azure.

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Subscrições** na categoria Geral.

1. Na lista de subscrições, selecione a subscrição cujo quota que procura.

   **Há uma ressalva,** especificamente para visualizar a quota Azure Machine Learning Compute. Conforme mencionado acima, esse quota é separada da quota de computação na sua subscrição.

1. No painel esquerdo, selecione **o serviço de Aprendizagem automática** e, em seguida, selecione qualquer espaço de trabalho da lista mostrada

1. Na lâmina seguinte, sob a **secção suporte + resolução** de problemas selecione **Utilização + quotas** para ver os seus atuais limites de quota e utilização.

1. Selecione uma subscrição para ver os limites de quota. Lembre-se filtrar para a região que está interessado.

1. Agora pode alternar entre uma vista de nível de subscrição e uma vista de nível de espaço de trabalho:
    + **Vista de subscrição:** Isto permite-lhe visualizar o seu uso da quota principal pela família VM, expandindo-a por espaço de trabalho, e expandindo-a ainda mais pelos nomes reais do cluster. Esta vista é ideal para entrar rapidamente nos detalhes do uso do núcleo para uma determinada família VM para ver a separação por espaços de trabalho e ainda mais pelos clusters subjacentes para cada um desses espaços de trabalho. A convenção geral, neste ponto de vista, é (utilização/quota), onde a utilização é o número atual de núcleos escalados, e a quota é o número máximo lógico de núcleos a que o recurso pode escalar. Para cada **espaço de trabalho,** a quota seria a quota de nível de espaço de trabalho (como explicado acima) que denota o número máximo de núcleos a que se pode escalar para uma determinada família VM. Para um **cluster** da mesma forma, a quota é, na verdade, os núcleos correspondentes ao número máximo de nós que o cluster pode escalar para definido pela propriedade max_nodes.

    + **Vista do espaço de trabalho:** Isto permite-lhe visualizar o seu uso da quota principal pelo Workspace, expandindo-a pela família VM, e expandindo-a ainda mais pelos nomes reais do cluster. Esta vista é ideal para entrar rapidamente nos detalhes da utilização do núcleo para um determinado espaço de trabalho para ver a separação das famílias VM e ainda mais pelos aglomerados subjacentes a cada uma dessas famílias.

## <a name="request-quota-increases"></a>Pedir aumentos de quota

Se pretender elevar o limite ou quota acima do limite de incumprimento, [abra um pedido](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) de apoio ao cliente online gratuitamente.

Os limites não podem ser elevados acima do valor-limite máximo indicado nas tabelas. Se não houver limite máximo, então o recurso não tem limites ajustáveis. [Este](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artigo cobre mais pormenorizadamente o processo de aumento de quotas.

Quando pedir um aumento de quota, tem de selecionar o serviço que está a pedir para gerar a quota contra, que pode ser serviços, como a quota de serviço de Machine Learning, instâncias de contentor ou quota de armazenamento. Além da Computação de Aprendizagem automática Azure, pode clicar no botão **'Quota solicitação'** enquanto visualiza a quota seguindo os passos acima.

> [!NOTE]
> [As assinaturas de Ensaio Gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limites ou quotas. Se tiver uma [subscrição de Teste Gratuito,](https://azure.microsoft.com/offers/ms-azr-0044p)pode fazer upgrade para uma subscrição [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Para mais informações, consulte [upgrade Azure Free Trial para pay-As-You-Go](../billing/billing-upgrade-azure-subscription.md) e [free trial subscrição FAQ](https://azure.microsoft.com/free/free-account-faq).
