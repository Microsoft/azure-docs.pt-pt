---
title: Recuperação de desastres e geo-distribuição Funções Duradouras Azure
description: Saiba mais sobre a recuperação de desastres e geo-distribuição em Funções Duráveis.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75433335"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Recuperação de desastres e geo-distribuição em Funções Duráveis Azure

Em Funções Duradouras, todo o estado é persistido no Armazenamento Azure. Um [centro de tarefas](durable-functions-task-hubs.md) é um recipiente lógico para os recursos de armazenamento Azure que são usados para orquestrações. Orquestradores e funções de atividade só podem interagir uns com os outros quando pertencem ao mesmo centro de tarefas.
Os cenários descritos propõem opções de implantação para aumentar a disponibilidade e minimizar o tempo de inatividade durante as atividades de recuperação de desastres.

É importante notar que estes cenários são baseados em configurações Active-Passive, uma vez que são guiados pelo uso do Azure Storage. Este padrão consiste em implementar uma aplicação de função de backup (passiva) para uma região diferente. O Gestor de Tráfego monitorizará a aplicação de função primária (ativa) para disponibilidade. Falhará na aplicação de função de backup se a primária falhar. Para obter mais informações, consulte o [Método de Encaminhamento Prioritário de Tráfego](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) do Gestor de [Tráfego.](https://azure.microsoft.com/services/traffic-manager/)

>[!NOTE]
>
> - A configuração activa-passiva proposta garante que um cliente é sempre capaz de desencadear novas orquestrações através de HTTP. No entanto, como consequência de ter duas aplicações de função que partilham o mesmo armazenamento, o processamento de fundo será distribuído entre ambos, concorrendo por mensagens nas mesmas filas. Esta configuração incorre em custos adicionais de saída para a aplicação de função secundária.
> - A conta de armazenamento subjacente e o centro de tarefas são criados na região primária, e são partilhados por ambas as aplicações de função.
> - Todas as aplicações de função que são implementadas de forma redundante devem partilhar as mesmas teclas de acesso de função no caso de serem ativadas através de HTTP. O Tempo de Execução de Funções expõe uma [API de gestão](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite aos consumidores adicionar, eliminar e atualizar as teclas de função.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1 - Carregar o cálculo equilibrado com armazenamento partilhado

Se a infraestrutura de computação em Azure falhar, a aplicação de função poderá ficar indisponível. Para minimizar a possibilidade de tal tempo de inatividade, este cenário utiliza duas aplicações de função implementadas em diferentes regiões.
O Traffic Manager está configurado para detetar problemas na aplicação de função primária e redirecionar automaticamente o tráfego para a aplicação de função na região secundária. Esta aplicação de função partilha a mesma conta de Armazenamento Azure e Task Hub. Portanto, o estado das aplicações de função não se perde e o trabalho pode ser retomado normalmente. Uma vez que a saúde seja restaurada na região primária, o Azure Traffic Manager começará automaticamente a encaminhar os pedidos para essa aplicação de função.

![Diagrama mostrando o cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Existem vários benefícios ao utilizar este cenário de implantação:

- Se a infraestrutura computacional falhar, os trabalhos podem ser retomados na região de falha sem perda do Estado.
- O Traffic Manager cuida automaticamente da falha automática na aplicação de função saudável.
- O Gestor de Tráfego restabelece automaticamente o tráfego para a aplicação de função primária depois de a paralisação ter sido corrigida.

No entanto, utilizando este cenário, considere:

- Se a aplicação de função for implementada utilizando um plano de Serviço de Aplicações dedicado, replicar a infraestrutura de computação na falha sobre datacenter aumenta os custos.
- Este cenário cobre falhas na infraestrutura de computação, mas a conta de armazenamento continua a ser o único ponto de falha para a app de função. Se houver uma paragem de armazenamento, a aplicação sofre uma inatividade.
- Se a aplicação de função for chumbada, haverá um aumento da latência, uma vez que irá aceder à sua conta de armazenamento em todas as regiões.
- O acesso ao serviço de armazenamento de uma região diferente onde está localizado incorre em custos mais elevados devido ao tráfego de saídas de rede.
- Este cenário depende do Gestor de Tráfego. Tendo em conta [o funcionamento do Traffic Manager,](../../traffic-manager/traffic-manager-how-it-works.md)pode demorar algum tempo até que uma aplicação do cliente que consuma uma Função Durável precise de consultar novamente o endereço da aplicação de função do Gestor de Tráfego.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2 - Carregar o cálculo equilibrado com o armazenamento regional

O cenário anterior abrange apenas o caso de falha na infraestrutura computacional. Se o serviço de armazenamento falhar, resultará numa paragem da aplicação de função.
Para garantir o funcionamento contínuo das funções duráveis, este cenário utiliza uma conta de armazenamento local em cada região para onde as aplicações de função são implementadas.

![Diagrama mostrando cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Esta abordagem adiciona melhorias no cenário anterior:

- Se a aplicação de função falhar, o Traffic Manager cuida de falhar na região secundária. No entanto, uma vez que a aplicação de função se baseia na sua própria conta de armazenamento, as funções duráveis continuam a funcionar.
- Durante uma falha, não há latência adicional na região de falha, uma vez que a app de função e a conta de armazenamento são co-localizadas.
- A falha da camada de armazenamento causará falhas nas funções duráveis, o que, por sua vez, irá desencadear uma reorientação para a região de falha. Mais uma vez, uma vez que a aplicação de função e o armazenamento estão isolados por região, as funções duráveis continuarão a funcionar.

Considerações importantes para este cenário:

- Se a aplicação de função for implementada utilizando um plano de AppService dedicado, replicar a infraestrutura de computação na falha sobre datacenter aumenta os custos.
- O estado atual não é falhado, o que implica que as funções de execução e de verificação falharão. Cabe ao cliente tentar novamente/reiniciar o trabalho.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3 - Carregue o cálculo equilibrado com armazenamento partilhado GRS

Este cenário é uma modificação sobre o primeiro cenário, implementando uma conta de armazenamento partilhada. A principal diferença que a conta de armazenamento é criada com geo-replicação ativada.
Funcionalmente, este cenário proporciona as mesmas vantagens que o Cenário 1, mas permite vantagens adicionais de recuperação de dados:

- O armazenamento geo-redundante (GRS) e o GRS de acesso à leitura (RA-GRS) maximizam a disponibilidade para a sua conta de armazenamento.
- Se houver uma paragem regional do serviço de armazenamento, uma das possibilidades é que as operações do datacenter determinem que o armazenamento deve ser falhado na região secundária. Neste caso, o acesso à conta de armazenamento será redirecionado de forma transparente para a cópia geo-replicada da conta de armazenamento, sem intervenção do utilizador.
- Neste caso, o estado das funções duráveis será preservado até à última replicação da conta de armazenamento, que ocorre a cada poucos minutos.

Tal como acontece com os outros cenários, há considerações importantes:

- A falha na réplica é feita pelos operadores do datacenter e pode demorar algum tempo. Até lá, a aplicação de função sofrerá uma paragem.
- Há um custo acrescido para a utilização de contas de armazenamento geo-replicadas.
- GRS ocorre assíncroticamente. Algumas das transações mais recentes podem perder-se devido à latência do processo de replicação.

![Diagrama mostrando cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Próximos passos

Você pode ler mais sobre [design de aplicações altamente disponíveis usando RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
