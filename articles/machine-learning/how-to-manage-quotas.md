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
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: 4e5634633aacb56e7c7744b3afdb3aec0fe2b70f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629595"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Gerir & aumentar quotas de recursos com a Azure Machine Learning


Neste artigo, você vai aprender sobre os limites pré-configurados sobre os recursos Azure para a sua subscrição [Azure Machine Learning](overview-what-is-azure-ml.md) e quais as quotas que você pode gerir. Estes limites são insíbidos para evitar excedentes orçamentais devido a fraudes e para honrar as restrições de capacidade do Azure. 

Tal como acontece com outros serviços Azure, existem limites em certos recursos associados à Azure Machine Learning. Estes limites vão desde uma tampa no número de espaços de [trabalho](concept-workspace.md) até limites ao cálculo subjacente real que é usado para treino de modelo ou inferência/pontuação. 

À medida que concebe e escala os seus recursos de Aprendizagem automática Azure para cargas de trabalho de produção, considere estes limites. Por exemplo, se o seu cluster não atingir o número alvo de nós, então pode ter atingido um limite de núcleos Azure Machine Learning Compute para a sua subscrição. Se pretender aumentar o limite ou quota acima do Limite Padrão, abra gratuitamente um pedido de apoio ao cliente online. Os limites não podem ser elevados acima do valor limite máximo indicado nas tabelas seguintes devido a restrições de capacidade do Azure. Se não houver uma coluna Limite Máximo, então o recurso não tem limites ajustáveis.


Juntamente com a gestão de quotas, você também pode aprender a [planear & gerir custos para Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Considerações especiais

+ Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de grande capacidade, contacte o suporte da Azure. Também pode [aumentar as suas quotas.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)

+ A sua quota é partilhada em todos os serviços das suas subscrições, incluindo Azure Machine Learning. A única exceção é o cálculo Azure Machine Learning que tem uma quota separada da quota de cálculo principal. Certifique-se de calcular o uso da quota em todos os serviços ao avaliar as suas necessidades de capacidade.

+ Os limites predefinidos variam de acordo com a oferta Categoria Tipo, tais como Free Trial, Pay-As-You-Go e séries VM, tais como Dv2, F, G, e assim por diante.

## <a name="default-resource-quotas"></a>Quotas de recursos predefinidos

Aqui está uma repartição dos limites de quota por vários tipos de recursos dentro da sua assinatura Azure.

> [!IMPORTANT]
> Os limites estão sujeitos a alterações. As últimas podem sempre ser encontradas no [documento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) de quota de nível de serviço para toda a Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Para cada subscrição do Azure, existe um limite para o número de máquinas virtuais em todos os seus serviços ou autónomos. Os núcleos de máquinas virtuais têm um limite total regional e um limite regional por série de tamanho (Dv2, F, etc.), ambos são aplicados separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição poderá implementar 30 VMs A1, 30 VMs D1 ou uma combinação de ambas que não excedam um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Computação do Machine Learning
Para [o Azure Machine Learning Compute,](concept-compute-target.md#azure-machine-learning-compute-managed)existe um limite de quota padrão tanto no número de núcleos como no número de recursos computacional únicos permitidos por região numa subscrição. Esta quota é separada da quota-base VM acima e os limites de base não são partilhados entre os dois tipos de recursos, uma vez que a AmlCompute é um serviço gerido que utiliza recursos num modelo alojado em nome do modelo.

Recursos disponíveis:
+ Os núcleos dedicados por região têm um limite padrão de 24 - 300 dependendo do tipo de oferta de subscrição com padrão mais elevado para os tipos de oferta EA e CSP.  O número de núcleos dedicados por subscrição pode ser aumentado e é diferente para cada família VM. Algumas famílias de VM especializadas como NCv2, NCv3 ou série ND começam com um padrão de núcleos zero. Contacte o apoio da Azure levantando um pedido de quota para discutir opções de aumento.

+ Os núcleos de baixa prioridade por região têm um limite padrão de 100 - 3000 dependendo do tipo de oferta de subscrição com padrão mais elevado para os tipos de oferta EA e CSP. O número de núcleos de baixa prioridade por subscrição pode ser aumentado e é um valor único em todas as famílias VM. Contacte o suporte da Azure para discutir opções de aumento.

+ Os aglomerados por região têm um limite de incumprimento de 200. Estes são partilhados entre um cluster de formação e um caso computacional (que é considerado como um único aglomerado de nó para efeitos de quota). Contacte o suporte da Azure se pretender solicitar um aumento para além deste limite.

+ Há outros limites estritos que não podem ser ultrapassados uma vez atingidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Espaços de trabalho máximos por grupo de recursos | 800 |
| Nós máximos num único recurso Azure Machine Learning Compute (AmlCompute) | 100 nosdes |
| Processos máximos de MPI da GPU por nó | 1-4 |
| Trabalhadores máximos da GPU por nó | 1-4 |
| Vida útil máxima | 21 dias<sup>1</sup> |
| Vida útil máxima num nó Low-Priority | 7 dias<sup>2</sup> |
| Servidores de parâmetros máximos por nó | 1 |

<sup>1</sup> A duração máxima refere-se ao tempo que uma corrida começa e quando termina. As execuções concluídas persistem indefinidamente; os dados relativos a execuções não concluídas no prazo máximo de vida não são acessíveis.
<sup>2</sup> Os postos de trabalho num nó Low-Priority podem ser antecipados sempre que houver uma restrição de capacidade. Recomendamos que implemente o checkpointing no seu trabalho.

### <a name="azure-machine-learning-pipelines"></a>Gasodutos de aprendizagem de máquinas Azure
Para [os gasodutos de aprendizagem automática Azure,](concept-ml-pipelines.md)existe um limite de quota no número de etapas de um oleoduto e no número de percursos de gasodutos publicados por região por cada região numa subscrição.
- O número máximo de passos permitidos num oleoduto é de 30.000
- O número máximo de execuções baseadas em horários e de "blob" para horários desencadeados por blogs de gasodutos publicados por subscrição por mês é de 100.000

### <a name="container-instances"></a>Instâncias de contentores

Existe também um limite para o número de casos de contentores que pode girar num determinado período de tempo (anoiteça) ou em toda a subscrição.
Para os limites, consulte os [limites de instâncias de contentores](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
Existe um limite para o número de contas de armazenamento por região, bem como numa determinada subscrição. O limite de incumprimento é de 250 e inclui contas Standard e Premium Storage. Se necessitar de mais de 250 contas de armazenamento numa determinada região, faça um pedido através [do Apoio Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) A equipa de Armazenamento Azure irá rever o seu caso de negócio e poderá aprovar até 250 contas de armazenamento para uma determinada região.


## <a name="workspace-level-quota"></a>Quota de nível de espaço de trabalho

Para gerir melhor as alocações de recursos para o objetivo Azure Machine Learning Compute (Amlcompute) entre [vários espaços de trabalho,](concept-workspace.md)introduzimos uma funcionalidade que lhe permite distribuir quotas de nível de subscrição (pela família VM) e configurá-las ao nível do espaço de trabalho. O comportamento padrão é que todos os espaços de trabalho têm a mesma quota que a quota de nível de subscrição de qualquer família VM. No entanto, à medida que o número de espaços de trabalho aumenta, e as cargas de trabalho de prioridade variada começam a partilhar os mesmos recursos, os utilizadores querem uma forma de partilhar melhor a capacidade e evitar problemas de contenção de recursos. O Azure Machine Learning fornece uma solução com a sua oferta de computação gerida, permitindo aos utilizadores definir uma quota máxima para uma determinada família VM em cada espaço de trabalho. Isto é análogo à distribuição da sua capacidade entre espaços de trabalho, e os utilizadores podem optar por também alocar em excesso para impulsionar a máxima utilização. 

Para definir quotas ao nível do espaço de trabalho, vá a qualquer espaço de trabalho na sua subscrição e clique em **Uses + quotas** no painel esquerdo. Em seguida, selecione o **separador quotas Configure** para visualizar as quotas, expandir qualquer família VM, e definir um limite de quota em qualquer espaço de trabalho listado sob essa família VM. Lembre-se que não pode definir um valor negativo ou um valor superior ao do contingente de nível de subscrição. Além disso, como observaria, por padrão, todos os espaços de trabalho são atribuídos a toda a quota de subscrição para permitir a utilização integral da quota atribuída.

[![Quota de nível de espaço de trabalho de aprendizagem de máquina de Azure](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Precisa de permissões de nível de subscrição para definir a quota ao nível do espaço de trabalho. Isto é aplicado para que os proprietários individuais do espaço de trabalho não editem ou aumentem as suas quotas e comecem a invadir os recursos reservados para outro espaço de trabalho. Assim, um administrador de subscrição é o mais adequado para alocar e distribuir estas quotas por espaços de trabalho.



## <a name="view-your-usage-and-quotas"></a>Veja o seu uso e quotas

A quota de Cálculo de Aprendizagem de Máquinas Azure na sua subscrição é gerida separadamente de outra quota de recursos Azure. Para ver esta quota, você precisa aprofundar os serviços de Machine Learning.  

1. No painel esquerdo, selecione o **serviço machine learning** e, em seguida, selecione qualquer espaço de trabalho da lista mostrada.

2. Na lâmina seguinte, sob a **secção De Suporte + Resolução de Problemas** selecione Usage + **quotas** para ver os seus limites de quota e utilização atuais.

3. Selecione uma subscrição para ver os limites de quota. Lembre-se de filtrar para a região que lhe interessa.

4. Agora pode alternar entre uma vista de nível de subscrição e uma vista de nível de espaço de trabalho:
    + **Vista de assinatura:** Isto permite-lhe visualizar o seu uso de quota principal pela família VM, expandindo-a por espaço de trabalho, e expandindo-a ainda mais pelos nomes reais do cluster. Esta vista é ideal para entrar rapidamente nos detalhes do uso do núcleo para uma determinada família VM ver a separação por espaços de trabalho e ainda mais pelos clusters subjacentes para cada um desses espaços de trabalho. A convenção geral neste ponto é (utilização/quota), onde a utilização é o número atual de núcleos dimensionado, e a quota é o número máximo lógico de núcleos a que o recurso pode escalar. Para cada **espaço de trabalho,** a quota seria a quota de nível de trabalho (como explicado acima) que denota o número máximo de núcleos a que se pode escalar para uma determinada família de VM. Para um **cluster** da mesma forma, a quota é na verdade os núcleos correspondentes ao número máximo de nós que o cluster pode escalar para definido pela propriedade max_nodes.
    
    + **Vista do espaço de trabalho:** Isto permite-lhe visualizar o seu uso de quota principal pelo Workspace, expandindo-o pela família VM, e expandindo-o ainda mais pelos nomes reais do cluster. Esta visão é ideal para entrar rapidamente nos detalhes do uso do núcleo para um determinado espaço de trabalho para ver a separação por parte das famílias VM e ainda mais pelos clusters subjacentes para cada uma dessas famílias.

Ver a sua quota para vários outros recursos Azure, como Máquinas Virtuais, Armazenamento, Rede, é fácil através do portal Azure.

1. No painel esquerdo, selecione **Todos os serviços** e, em seguida, selecione **Subscrições** na categoria Geral.

2. Na lista de subscrições, selecione a subscrição cuja quota procura.

3. Selecione **Use + quotas** para ver os seus limites de quota e utilização atuais. Utilize os filtros para selecionar o fornecedor e as localizações. 

## <a name="request-quota-increases"></a>Pedir aumentos de quota

Se pretender aumentar o limite ou quota acima do limite de incumprimento, abra gratuitamente um pedido de apoio ao [cliente online.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)

Os limites não podem ser elevados acima do valor limite máximo indicado nas tabelas. Se não houver limite máximo, então o recurso não tem limites ajustáveis. [Consulte instruções passo a passo sobre como aumentar a sua quota](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Ao solicitar um aumento de quota, precisa de selecionar o serviço que está a solicitar para aumentar a quota contra, que podem ser serviços como quota de serviço de Machine Learning, instâncias de contentores ou quota de armazenamento. Além de Azure Machine Learning Compute, pode clicar no botão **Quota de Pedido** enquanto visualiza a quota seguindo os passos acima.

> [!NOTE]
> [As assinaturas de teste gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limites ou de quotas. Se tiver uma [subscrição de Teste Gratuito,](https://azure.microsoft.com/offers/ms-azr-0044p)pode fazer upgrade para uma subscrição [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Para obter mais informações, consulte [upgrade Azure Free Trial para Pay-As-You-Go](../billing/billing-upgrade-azure-subscription.md) e  [subscrição de teste gratuito FAQ](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Fim privado e quota privada de DNS aumentam

Existem limitações no número de pontos finais privados e zonas privadas de DNS que podem ser criadas numa subscrição. Embora o Azure Machine Learning crie recursos na sua subscrição (cliente), existem alguns cenários que criam recursos numa subscrição detida pela Microsoft. Nos seguintes cenários, poderá ter de solicitar um subsídio de quota na subscrição detida pela Microsoft:

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
    | Subtipo de problema | Problema com o aprovisionamento ou a gestão da área de trabalho |

2. A partir da secção __Detalhes,__ utilize o campo __Descrição__ para fornecer a região Azure que pretende utilizar e o cenário que pretende utilizar. Se precisar de solicitar aumentos de quota para várias subscrições, liste também os IDs de subscrição neste campo.

3. Utilizar __criar__ para criar o pedido.

## <a name="next-steps"></a>Passos seguintes

+ [Plano & gerir custos para a Azure Machine Learning](concept-plan-manage-cost.md)
