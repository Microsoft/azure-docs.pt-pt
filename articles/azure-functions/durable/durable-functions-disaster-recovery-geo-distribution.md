---
title: Funções de recuperação e geodistribuição de desastres azure duráveis
description: Conheça a recuperação de desastres e a geodistribuição em Funções Duráveis.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433335"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Recuperação e geodistribuição de desastres em Funções Duráveis Azure

Em Funções Duráveis, todo o estado é perpersistido no Armazenamento Azure. Um [centro de tarefas](durable-functions-task-hubs.md) é um recipiente lógico para os recursos de Armazenamento Azure que são usados para orquestrações. As funções de orquestrador e de atividade só podem interagir entre si quando pertencem ao mesmo centro de tarefas.
Os cenários descritos propõem opções de implantação para aumentar a disponibilidade e minimizar o tempo de inatividade durante as atividades de recuperação de desastres.

É importante notar que estes cenários são baseados em configurações Active-Passive, uma vez que são guiados pelo uso do Armazenamento Azure. Este padrão consiste em implementar uma aplicação de função de backup (passiva) para uma região diferente. O Traffic Manager monitorizará a aplicação de função primária (ativa) para a disponibilidade. Falhará na aplicação de função de backup se a primeira falhar. Para mais informações, consulte o [Método prioritário de encaminhamento de tráfego](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) do Gestor de [Tráfego.](https://azure.microsoft.com/services/traffic-manager/)

>[!NOTE]
>
> - A configuração Active-Passive proposta garante que um cliente é sempre capaz de desencadear novas orquestrações via HTTP. No entanto, como consequência de ter duas aplicações de função que partilham o mesmo armazenamento, o processamento de antecedentes será distribuído entre ambas, concorrendo para mensagens nas mesmas filas. Esta configuração incorre em custos de saída adicionais para a aplicação de função secundária.
> - A conta de armazenamento subjacente e o centro de tarefas são criados na região primária, e são partilhados por ambas as aplicações de função.
> - Todas as aplicações de função que são implantadas redundantemente, devem partilhar as mesmas teclas de acesso de função no caso de serem ativadas via HTTP. O Tempo de Execução de Funções expõe uma API de [gestão](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite aos consumidores adicionar, eliminar e atualizar programáticamente as teclas de função.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1 - Computação equilibrada de carga com armazenamento partilhado

Se a infraestrutura computacional em Azure falhar, a aplicação de funções pode ficar indisponível. Para minimizar a possibilidade de tal tempo de inatividade, este cenário utiliza duas aplicações de função implementadas em diferentes regiões.
O Traffic Manager está configurado para detetar problemas na aplicação de função primária e redirecionar automaticamente o tráfego para a aplicação de funções na região secundária. Esta aplicação de função partilha a mesma conta de Armazenamento Azure e Task Hub. Portanto, o estado das aplicações de função não se perde e o trabalho pode ser retomado normalmente. Assim que a saúde for restaurada para a região primária, o Gestor de Tráfego azure começará a encaminhar automaticamente os pedidos para essa aplicação de função.

![Diagrama mostrando cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Existem vários benefícios ao utilizar este cenário de implementação:

- Se a infraestrutura computacional falhar, os trabalhos podem ser retomados na região falhada sem perdas estatais.
- O Gestor de Tráfego cuida da falha automática na aplicação de função saudável automaticamente.
- O Gestor de Tráfego restabelece automaticamente o tráfego para a aplicação de função primária depois de a paragem ter sido corrigida.

No entanto, usando este cenário considere:

- Se a aplicação de funções for implementada utilizando um plano de Serviço de Aplicações dedicado, replicar a infraestrutura de cálculo no fail over datacenter aumenta os custos.
- Este cenário cobre falhas na infraestrutura de cálculo, mas a conta de armazenamento continua a ser o ponto único de falha para a app de função. Se houver uma paragem de armazenamento, a aplicação sofre um tempo de inatividade.
- Se a aplicação de funções falhar, haverá um aumento da latência, uma vez que acederá à sua conta de armazenamento em todas as regiões.
- Aceder ao serviço de armazenamento de uma região diferente onde está localizado incorre em custos mais elevados devido ao tráfego de egress de rede.
- Este cenário depende do Gestor de Tráfego. Considerando como funciona o Gestor de [Tráfego,](../../traffic-manager/traffic-manager-how-it-works.md)pode demorar algum tempo até que uma aplicação de cliente que consuma uma Função Durável precise de consultar novamente o endereço da aplicação de funções do Gestor de Tráfego.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2 - Carga com putação equilibrada com armazenamento regional

O cenário anterior abrange apenas o caso de falha na infraestrutura de cálculo. Se o serviço de armazenamento falhar, resultará numa interrupção da aplicação de funções.
Para garantir o funcionamento contínuo das funções duráveis, este cenário utiliza uma conta de armazenamento local em cada região para a qual as aplicações de função são implementadas.

![Diagrama mostrando cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Esta abordagem adiciona melhorias no cenário anterior:

- Se a aplicação de função falhar, o Gestor de Tráfego cuida de falhar na região secundária. No entanto, como a aplicação de funções depende da sua própria conta de armazenamento, as funções duradouras continuam a funcionar.
- Durante uma falha, não há latência adicional na região de falha, uma vez que a aplicação de função e a conta de armazenamento estão co-localizadas.
- A falha da camada de armazenamento provocará falhas nas funções duradouras, o que, por sua vez, irá desencadear uma reorientação para a falha sobre a região. Mais uma vez, uma vez que a aplicação de funções e o armazenamento estão isolados por região, as funções duradouras continuarão a funcionar.

Considerações importantes para este cenário:

- Se a aplicação de funções for implementada utilizando um plano de AppService dedicado, replicar a infraestrutura de cálculo no fail over datacenter aumenta os custos.
- O estado atual não falhou, o que implica que executar e checkpoint funções falhará. Cabe ao pedido do cliente retentar/reiniciar o trabalho.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3 - Computação equilibrada de carga com armazenamento partilhado GRS

Este cenário é uma modificação ao longo do primeiro cenário, implementando uma conta de armazenamento partilhada. A principal diferença de que a conta de armazenamento é criada com geo-replicação ativada.
Funcionalmente, este cenário proporciona as mesmas vantagens que o Cenário 1, mas permite vantagens adicionais de recuperação de dados:

- O armazenamento geo-redundante (GRS) e o GRS de acesso de leitura (RA-GRS) maximizam a disponibilidade para a sua conta de armazenamento.
- Se houver uma falha na região do serviço de armazenamento, uma das possibilidades é que as operações do datacenter determinem que o armazenamento deve ser falhado na região secundária. Neste caso, o acesso à conta de armazenamento será redirecionado de forma transparente para a cópia geo-replicada da conta de armazenamento, sem a intervenção do utilizador.
- Neste caso, o estado das funções duráveis será preservado até à última replicação da conta de armazenamento, que ocorre a cada poucos minutos.

Tal como acontece com os outros cenários, existem considerações importantes:

- A falha na réplica é feita pelos operadores de datacenter e pode demorar algum tempo. Até lá, a aplicação de funções sofrerá uma paragem.
- Há um custo acrescido para a utilização de contas de armazenamento geo-replicadas.
- GrS ocorre assincronicamente. Algumas das últimas transações podem perder-se devido à latência do processo de replicação.

![Diagrama mostrando cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Passos seguintes

Pode ler mais sobre [design de aplicações altamente disponíveis usando RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
