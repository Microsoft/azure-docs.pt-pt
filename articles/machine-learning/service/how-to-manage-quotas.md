---
title: Gerir e solicitar quotas de recursos
titleSuffix: Azure Machine Learning service
description: Este guia de procedimentos explica as quotas de vários recursos para o Azure Machine Learning e como visualizar e pedir mais quota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: aa425b6dfeb076448d14fc35cbea964516d603b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904648"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Gerir e solicitar as quotas para recursos do Azure

Como com outros serviços do Azure, existem limites em certos recursos associados com o serviço Azure Machine Learning. Estes limites de intervalo de um limite no número de áreas de trabalho, pode criar limites na computação subjacente real que é utilizado para treinamento ou inferência seus modelos. Este artigo fornece mais detalhes sobre os limites previamente configurados em vários recursos do Azure para a sua subscrição e também contém ligações úteis para aprimoramentos de quota de pedido para cada tipo de recurso. Estes limites são colocados em vigor para impedir que o orçamento execuções excessiva devido a fraude e que respeite as restrições de capacidade do Azure.

Tenha presentes estas quotas em mente à medida que, design e aumentar verticalmente os recursos de serviço do Azure Machine Learning para cargas de trabalho de produção. Por exemplo, se o número de destino de nós que especificou não chega ao seu cluster, em seguida, poderá ter atingido um limite de núcleos de computação do Azure Machine Learning para a sua subscrição. Se quiser aumentar o limite ou quota acima do limite predefinido, abra um pedido de suporte do cliente online sem encargos. Os limites não podem ser aumentados acima do valor de limite máximo mostrado nas tabelas seguintes devido a restrições de capacidade do Azure. Se não houver nenhuma coluna de limite máximo, em seguida, o recurso não tem limites ajustável.

## <a name="special-considerations"></a>Considerações especiais

+ Uma quota é um limite de crédito, não uma garantia de capacidade. Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure.

+ A quota é compartilhada entre todos os serviços nas suas subscrições, incluindo o serviço Azure Machine Learning. A única exceção é a computação do Azure Machine Learning, que tem uma quota separada da quota de núcleo de computação. Certifique-se de que calcular a utilização de quota em todos os serviços ao avaliar as suas necessidades de capacidade.

+ Limites predefinidos variam de acordo com a oferta de tipo de categoria, como avaliação gratuita, pay as you go e série, como Dv2, F, G e assim por diante.

## <a name="default-resource-quotas"></a>Quotas de recursos padrão

Aqui está uma divisão os limites de quota por vários tipos de recursos na sua subscrição do Azure.

> [!Important]
> Limites estão sujeitos a alterações. A versão mais recente sempre pode ser encontrado na quota do nível de serviço [documento](https://docs.microsoft.com/azure/azure-subscription-service-limits/) para todas as do Azure.

### <a name="virtual-machines"></a>Máquinas virtuais
Existe um limite no número de máquinas virtuais, que pode aprovisionar uma subscrição do Azure em todos os seus serviços ou de forma autónoma. Este limite é no nível de região no total de núcleos e também numa base por família.

É importante enfatizar que núcleos de máquina virtual tem um limite total regional e um regional por limite de série (Dv2, F, etc.) de tamanho são impostos em separado. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região EUA Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição poderá implementar 30 VMs A1, 30 VMs D1 ou uma combinação de ambas que não excedam um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Para obter uma lista mais detalhada e atualizada de limites de quota, consulte o artigo de quota do Azure em todo [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Para Azure computação do Machine Learning, existe um limite de cota padrão sobre o número de núcleos e o número exclusivo de recursos de computação permitidas por região numa subscrição. Esta quota é separada da quota de núcleos VM acima e os limites de principal não são atualmente partilhados entre os tipos de dois recursos.

Recursos disponíveis:
+ Núcleos dedicados por região tem um limite predefinido de 10-24.  O número de núcleos dedicados por subscrição pode ser aumentado. Contacte o suporte do Azure para discutir as opções de aumento.

+ Núcleos de prioridade baixa por região tem um limite predefinido de 10-24.  O número de núcleos de prioridade baixa por subscrição pode ser aumentado. Contacte o suporte do Azure para discutir as opções de aumento.

+ Os clusters por região têm um limite predefinido de 100 e um limite máximo de 200. Se pretender pedir um aumento que ultrapassam este limite, contacte o suporte do Azure.

+ Existem **outros limites strict** que não pode ser excedido uma vez atingido.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Áreas de trabalho máximas por grupo de recursos | 800 |
| Número máximo de nós num único recurso de computação do Azure Machine Learning (AmlCompute) | 100 nós |
| Máximo de GPU de MPI processa por nó | 1 a 4 |
| Operadores de GPU máximos por nó | 1 a 4 |
| Duração de tarefas máxima | 7 dias<sup>1</sup> |
| Servidores de parâmetro máxima por nó | 1 |

<sup>1</sup> a duração máxima refere-se até ao momento em que iniciar uma execução e quando ele for concluída. Execuções concluídas mantêm-se indefinidamente; dados de execuções não concluídas dentro da duração máxima não estão acessíveis.

### <a name="container-instances"></a>Instâncias de contentor

Também existe um limite no número de instâncias de contentor que pode acelerar num determinado período de tempo (hora a hora de âmbito) ou em sua assinatura inteira.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Para obter uma lista mais detalhada e atualizada de limites de quota, consulte o artigo de quota do Azure em todo [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Armazenamento
Existe um limite no número de contas de armazenamento por região também numa determinada subscrição. O limite predefinido é 200 e inclui contas padrão e o armazenamento Premium. Se necessitar de mais de 200 contas de armazenamento numa determinada região, efetue um pedido através de [suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). A equipa do armazenamento do Azure analisará o seu caso comercial e pode aprovar até 250 contas de armazenamento para uma determinada região.


## <a name="find-your-quotas"></a>Encontrar as suas quotas

Ver a sua quota para vários recursos, como máquinas virtuais, armazenamento, rede, é fácil através do portal do Azure.

1. No painel esquerdo, selecione **todos os serviços** e, em seguida, selecione **subscrições** sob a categoria geral.

1. Na lista de subscrições, selecione a subscrição cujo quota que procura.

   **Há uma limitação:**, especificamente para ver a quota de computação do Azure Machine Learning. Conforme mencionado acima, esse quota é separada da quota de computação na sua subscrição.

1. No painel esquerdo, selecione **serviço Machine Learning** e, em seguida, selecione qualquer área de trabalho na lista apresentada

1. No painel seguinte, sob o **suporte + resolução de problemas de seção** selecionar **utilização + quotas** para ver os limites de quota atual e o uso.

1. Selecione uma subscrição para ver os limites de quota. Lembre-se filtrar para a região que está interessado.


## <a name="request-quota-increases"></a>Aumentos de quota de pedido

Se deseja elevar o limite ou quota acima do limite de predefinição [abra um pedido de suporte do cliente online](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) sem encargos.

Os limites não podem ser aumentados acima do valor de limite máximo mostrado nas tabelas. Se não houver nenhum limite máximo, em seguida, o recurso não tem limites ajustável. [Isso](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artigo aborda o processo de aumento de quota em mais detalhes.

Quando pedir um aumento de quota, tem de selecionar o serviço que está a pedir para gerar a quota contra, que pode ser serviços, como a quota de serviço de Machine Learning, instâncias de contentor ou quota de armazenamento. Além para o Azure computação do Machine Learning, pode simplesmente clicar no **pedir Quota** botão enquanto vê a quota ao seguir os passos acima.

> [!NOTE]
> [As subscrições de avaliação de gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limite ou quota. Se tiver um [subscrição de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), pode atualizar para uma [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) subscrição. Para obter mais informações, consulte [atualizar avaliação gratuita do Azure para pay as you go](../../billing/billing-upgrade-azure-subscription.md) e [perguntas Freqüentes da assinatura de avaliação gratuita](https://azure.microsoft.com/free/free-account-faq).
