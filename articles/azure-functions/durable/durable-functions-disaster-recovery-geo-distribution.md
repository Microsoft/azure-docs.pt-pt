---
title: Recuperação de desastres e geo-distribuição Funções Duradouras Azure
description: Saiba mais sobre a recuperação de desastres e geo-distribuição em Funções Duráveis.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071215"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Recuperação de desastres e geo-distribuição em Funções Duráveis Azure

A Microsoft esforça-se por garantir que os serviços Azure estão sempre disponíveis. No entanto, podem ocorrer interrupções de serviço não planeadas. Se a sua aplicação necessitar de resiliência, a Microsoft recomenda configurar a sua aplicação para geo-redundância. Além disso, os clientes devem ter um plano de recuperação de desastres em vigor para lidar com uma interrupção do serviço regional. Uma parte importante de um plano de recuperação de desastres está a preparar-se para falhar na réplica secundária da sua app e armazenamento no caso de a réplica primária ficar indisponível.

Em Funções Duradouras, todo o estado é persistido no Azure Storage por padrão. Um [centro de tarefas](durable-functions-task-hubs.md) é um recipiente lógico para os recursos de armazenamento Azure que são [utilizados](durable-functions-types-features-overview.md#orchestrator-functions) para orquestrações e [entidades.](durable-functions-types-features-overview.md#entity-functions) As funções de orquestrador, atividade e entidade só podem interagir entre si quando pertencem ao mesmo centro de tarefas. Este documento refere-se aos centros de tarefas ao descrever cenários para manter estes recursos de Armazenamento Azure altamente disponíveis.

Orquestrações e entidades podem ser desencadeadas usando funções de cliente que são [elas próprias desencadeadas](durable-functions-types-features-overview.md#client-functions) através de HTTP ou de um dos outros tipos de gatilho de Funções Azure suportados. Também podem ser acionados utilizando [APIs HTTP incorporados.](durable-functions-http-features.md#built-in-http-apis) Para simplificar, este artigo irá focar-se em cenários que envolvam a azure Storage e os gatilhos de função baseados em HTTP, e opções para aumentar a disponibilidade e minimizar o tempo de inatividade durante as atividades de recuperação de desastres. Outros tipos de gatilho, tais como os gatilhos Service Bus ou Cosmos DB, não serão explicitamente cobertos.

Os seguintes cenários baseiam-se em configurações Active-Passive, uma vez que são guiados pelo uso do Azure Storage. Este padrão consiste em implementar uma aplicação de função de backup (passiva) para uma região diferente. O Gestor de Tráfego monitorizará a aplicação de função primária (ativa) para disponibilidade de HTTP. Falhará na aplicação de função de backup se a primária falhar. Para mais informações, consulte o [Método de Traffic-Routing Prioritária](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) do Gestor de Tráfego da [Azure.](https://azure.microsoft.com/services/traffic-manager/)

> [!NOTE]
> - A configuração Active-Passive proposta garante que um cliente é sempre capaz de desencadear novas orquestrações através de HTTP. No entanto, como consequência de ter duas aplicações de função que partilham o mesmo centro de tarefas no armazenamento, algumas transações de armazenamento de fundo serão distribuídas entre ambas. Esta configuração incorre, portanto, em alguns custos adicionais de saída para a aplicação de função secundária.
> - A conta de armazenamento subjacente e o centro de tarefas são criados na região primária, e são partilhados por ambas as aplicações de função.
> - Todas as aplicações de função que são implantadas de forma redundante devem partilhar as mesmas teclas de acesso de função no caso de serem ativadas através de HTTP. O Tempo de Execução de Funções expõe uma [API de gestão](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite aos consumidores adicionar, eliminar e atualizar as teclas de função. A gestão de chaves também é possível usando [APIs do Gestor de Recursos Azure](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1 - Carregar o cálculo equilibrado com armazenamento partilhado

Se a infraestrutura de computação em Azure falhar, a aplicação de função poderá ficar indisponível. Para minimizar a possibilidade de tal tempo de inatividade, este cenário utiliza duas aplicações de função implementadas em diferentes regiões.
O Traffic Manager está configurado para detetar problemas na aplicação de função primária e redirecionar automaticamente o tráfego para a aplicação de função na região secundária. Esta aplicação de função partilha a mesma conta de Armazenamento Azure e Task Hub. Portanto, o estado das aplicações de função não se perde e o trabalho pode ser retomado normalmente. Uma vez que a saúde seja restaurada na região primária, o Azure Traffic Manager começará automaticamente a encaminhar os pedidos para essa aplicação de função.

![Diagrama mostrando o cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Existem vários benefícios ao utilizar este cenário de implantação:

- Se a infraestrutura de cálculo falhar, os trabalhos podem ser retomados na região de failover sem perda de dados.
- O Traffic Manager cuida automaticamente da falha automática na aplicação de função saudável.
- O Gestor de Tráfego restabelece automaticamente o tráfego para a aplicação de função primária depois de a paralisação ter sido corrigida.

No entanto, utilizando este cenário, considere:

- Se a aplicação de função for implementada utilizando um plano de Serviço de Aplicações dedicado, replicar a infraestrutura de computação no datacenter de falha aumenta os custos.
- Este cenário cobre falhas na infraestrutura de computação, mas a conta de armazenamento continua a ser o único ponto de falha para a app de função. Se ocorrer uma interrupção de armazenamento, a aplicação sofre tempo de inatividade.
- Se a aplicação de função for chumbada, haverá um aumento da latência, uma vez que irá aceder à sua conta de armazenamento em todas as regiões.
- O acesso ao serviço de armazenamento de uma região diferente onde está localizado incorre em custos mais elevados devido ao tráfego de saídas de rede.
- Este cenário depende do Gestor de Tráfego. Tendo em conta [o funcionamento do Traffic Manager,](../../traffic-manager/traffic-manager-how-it-works.md)pode demorar algum tempo até que uma aplicação do cliente que consuma uma Função Durável precise de consultar novamente o endereço da aplicação de função do Gestor de Tráfego.

> [!NOTE]
> A partir do **v2.3.0** da extensão Funções Duradouras, duas aplicações de função podem ser executadas com segurança ao mesmo tempo com a mesma conta de armazenamento e configuração do centro de tarefas. A primeira aplicação a iniciar irá adquirir um contrato de arrendamento de blob ao nível da aplicação que impede outras aplicações de roubar mensagens das filas do centro de tarefas. Se esta primeira aplicação deixar de funcionar, o seu contrato de arrendamento expirará e poderá ser adquirido através de uma segunda aplicação, que irá então proceder ao processo de mensagens do hub de tarefas.
> 
> Antes do v2.3.0, as aplicações de função configuradas para usar a mesma conta de armazenamento processarão mensagens e atualizarão os artefactos de armazenamento simultaneamente, resultando em atrasos globais muito mais elevados e custos de saída. Se as aplicações primárias e réplicas alguma vez tiverem códigos diferentes implantados para elas, mesmo temporariamente, então as orquestrações também podem deixar de ser executadas corretamente devido a inconsistências da função do orquestrador nas duas apps. Por isso, recomenda-se que todas as aplicações que necessitem de geo-distribuição para fins de recuperação de desastres utilizem v2.3.0 ou superior da extensão Durable.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2 - Carregar o cálculo equilibrado com o armazenamento regional

O cenário anterior abrange apenas o caso de falha na infraestrutura computacional. Se o serviço de armazenamento falhar, resultará numa paragem da aplicação de função.
Para garantir o funcionamento contínuo das funções duráveis, este cenário utiliza uma conta de armazenamento local em cada região para onde as aplicações de função são implementadas.

![Diagrama mostrando cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Esta abordagem adiciona melhorias no cenário anterior:

- Se a aplicação de função falhar, o Traffic Manager cuida de falhar na região secundária. No entanto, uma vez que a aplicação de função se baseia na sua própria conta de armazenamento, as funções duráveis continuam a funcionar.
- Durante um failover, não há latência adicional na região de failover uma vez que a app de função e a conta de armazenamento estão indicados.
- A falha da camada de armazenamento provocará falhas nas funções duráveis, o que, por sua vez, irá desencadear uma reorientação para a região de failover. Mais uma vez, uma vez que a aplicação de função e o armazenamento estão isolados por região, as funções duráveis continuarão a funcionar.

Considerações importantes para este cenário:

- Se a aplicação de função for implementada utilizando um plano de Serviço de Aplicações dedicado, replicar a infraestrutura de computação no datacenter de falha aumenta os custos.
- O estado atual não é falhado, o que implica que as orquestrações e entidades existentes serão efetivamente pausadas e indisponíveis até que a região primária recupere.

Resumindo, a troca entre o primeiro e o segundo cenário é que a latência é preservada e os custos de saída são minimizados, mas as orquestrações e entidades existentes estarão indisponíveis durante o tempo de inatividade. A questão de saber se estas compensações são aceitáveis depende dos requisitos do pedido.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3 - Carregue o cálculo equilibrado com armazenamento partilhado GRS

Este cenário é uma modificação sobre o primeiro cenário, implementando uma conta de armazenamento partilhada. A principal diferença é que a conta de armazenamento é criada com geo-replicação ativada.
Funcionalmente, este cenário proporciona as mesmas vantagens que o Cenário 1, mas permite vantagens adicionais de recuperação de dados:

- O armazenamento geo-redundante (GRS) e o GRS de acesso à leitura (RA-GRS) maximizam a disponibilidade para a sua conta de armazenamento.
- Se houver uma paragem regional do serviço de armazenamento, pode [iniciar manualmente uma falha na réplica secundária.](../../storage/common/storage-initiate-account-failover.md) Em circunstâncias extremas em que uma região se perde devido a um desastre significativo, a Microsoft pode iniciar um fracasso regional. Neste caso, não é necessária qualquer ação da sua parte.
- Quando um failover acontece, o estado das funções duráveis será preservado até à última replicação da conta de armazenamento, que normalmente ocorre a cada poucos minutos.

Tal como acontece com os outros cenários, há considerações importantes:

- Um falhanço na réplica pode levar algum tempo. Até que o failover complete e os registos DNS do Azure Storage tenham sido atualizados, a aplicação de função sofrerá uma paragem.
- Há um custo acrescido para a utilização de contas de armazenamento geo-replicadas.
- A replicação grs copia os seus dados assíncroneamente. Algumas das transações mais recentes podem perder-se devido à latência do processo de replicação.

![Diagrama mostrando cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Como descrito no Cenário 1, recomenda-se vivamente que as aplicações de função implementadas com esta estratégia utilizem **v2.3.0** ou superior da extensão de Funções Duráveis.

Para obter mais informações, consulte a documentação de [recuperação e falha de falha da conta de armazenamento do Azure Storage.](../../storage/common/storage-disaster-recovery-guidance.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o design de aplicações altamente disponíveis no Azure Storage](../../storage/common/geo-redundant-design.md)
