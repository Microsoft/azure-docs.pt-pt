---
title: Recuperação de desastre e distribuição geográfica no Durable Functions-Azure
description: Saiba mais sobre recuperação de desastres e distribuição geográfica no Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: ba459d2d2f3aaf595c1d834e2b29a231e3b2bb12
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232946"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Recuperação após desastre e distribuição geográfica

## <a name="overview"></a>Descrição geral

Em Durable Functions, todos os Estados são persistidos no armazenamento do Azure. Um [Hub de tarefas](durable-functions-task-hubs.md) é um contêiner lógico para recursos de armazenamento do Azure que são usados para orquestrações. As funções de orquestrador e atividade só podem interagir umas com as outras quando pertencem ao mesmo Hub de tarefas.
Os cenários descritos propõem opções de implantação para aumentar a disponibilidade e minimizar o tempo de inatividade durante as atividades de recuperação de desastre.

É importante observar que esses cenários se baseiam em configurações ativas/passivas, pois são guiados pelo uso do armazenamento do Azure. Esse padrão consiste na implantação de um aplicativo de função de backup (passivo) em uma região diferente. O Gerenciador de tráfego irá monitorar o aplicativo de funções primário (ativo) para disponibilidade. Ele fará failover para o aplicativo de função de backup se o primário falhar. Para obter mais informações, consulte [método de roteamento de tráfego de prioridade](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) do [Gerenciador de tráfego](https://azure.microsoft.com/services/traffic-manager/).

>[!NOTE]
>
> - A configuração ativa-passiva proposta garante que um cliente sempre consiga disparar novas orquestrações via HTTP. No entanto, como consequência de ter dois aplicativos de função compartilhando o mesmo armazenamento, o processamento em segundo plano será distribuído entre ambos, competindo mensagens nas mesmas filas. Essa configuração incorre em custos de saída adicionais para o aplicativo de função secundário.
> - A conta de armazenamento subjacente e o Hub de tarefas são criados na região primária e compartilhados por ambos os aplicativos de funções.
> - Todos os aplicativos de funções que são implantados com redundância devem compartilhar as mesmas chaves de acesso de função no caso de serem ativados via HTTP. O tempo de execução do Functions expõe uma [API de gerenciamento](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite aos consumidores adicionar, excluir e atualizar programaticamente as chaves de função.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1-computação com balanceamento de carga com armazenamento compartilhado

Se a infraestrutura de computação no Azure falhar, o aplicativo de funções poderá ficar indisponível. Para minimizar a possibilidade de tal tempo de inatividade, esse cenário usa dois aplicativos de funções implantados em regiões diferentes.
O Gerenciador de tráfego está configurado para detectar problemas no aplicativo de funções primário e redirecionar automaticamente o tráfego para o aplicativo de funções na região secundária. Esse aplicativo de funções compartilha a mesma conta de armazenamento do Azure e Hub de tarefas. Portanto, o estado dos aplicativos de função não é perdido e o trabalho pode retomar normalmente. Depois que a integridade for restaurada para a região primária, o Gerenciador de tráfego do Azure começará a rotear solicitações para esse aplicativo de função automaticamente.

![Diagrama mostrando o cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Há vários benefícios ao usar esse cenário de implantação:

- Se a infraestrutura de computação falhar, o trabalho poderá continuar na região de failover sem perda de estado.
- O Gerenciador de tráfego cuida do failover automático para o aplicativo de função íntegro automaticamente.
- O Gerenciador de tráfego restabelece automaticamente o tráfego para o aplicativo de funções primário depois que a interrupção é corrigida.

No entanto, ao usar esse cenário, considere:

- Se o aplicativo de funções for implantado usando um plano do serviço de aplicativo dedicado, a replicação da infraestrutura de computação no datacenter de failover aumentará os custos.
- Esse cenário abrange interrupções na infraestrutura de computação, mas a conta de armazenamento continua a ser o ponto único de falha para o aplicativo de funções. Se houver uma interrupção de armazenamento, o aplicativo sofrerá um tempo de inatividade.
- Se o aplicativo de funções passar por failover, haverá maior latência, pois ele acessará sua conta de armazenamento entre regiões.
- O acesso ao serviço de armazenamento de uma região diferente na qual ele está localizado incorre em um custo maior devido ao tráfego de saída da rede.
- Esse cenário depende do Gerenciador de tráfego. Considerando [como funciona o Gerenciador de tráfego](../../traffic-manager/traffic-manager-how-it-works.md), pode ser algum tempo até que um aplicativo cliente que consuma uma função durável precise consultar novamente o endereço do aplicativo de funções do Gerenciador de tráfego.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2-computação com balanceamento de carga com armazenamento regional

O cenário anterior abrange apenas o caso de falha na infraestrutura de computação. Se o serviço de armazenamento falhar, isso resultará em uma interrupção do aplicativo de funções.
Para garantir a operação contínua das funções duráveis, esse cenário usa uma conta de armazenamento local em cada região para a qual os aplicativos de funções são implantados.

![Diagrama mostrando o cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Essa abordagem adiciona melhorias ao cenário anterior:

- Se o aplicativo de funções falhar, o Gerenciador de tráfego cuidará do failover para a região secundária. No entanto, como o aplicativo de funções depende de sua própria conta de armazenamento, as funções duráveis continuam a funcionar.
- Durante um failover, não há nenhuma latência adicional na região de failover, pois o aplicativo de funções e a conta de armazenamento estão colocalizados.
- A falha da camada de armazenamento causará falhas nas funções duráveis, que, por sua vez, disparará um redirecionamento para a região de failover. Novamente, como o aplicativo de funções e o armazenamento são isolados por região, as funções duráveis continuarão a funcionar.

Considerações importantes para este cenário:

- Se o aplicativo de funções for implantado usando um plano de AppService dedicado, a replicação da infraestrutura de computação no datacenter de failover aumentará os custos.
- O estado atual não faz failover, o que implica que as funções de execução e ponto de verificação falharão. Cabe ao aplicativo cliente tentar novamente/reiniciar o trabalho.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3-computação com balanceamento de carga com armazenamento compartilhado GRS

Esse cenário é uma modificação no primeiro cenário, implementando uma conta de armazenamento compartilhado. A principal diferença de que a conta de armazenamento é criada com a replicação geográfica habilitada.
Funcionalmente, esse cenário fornece as mesmas vantagens que o cenário 1, mas permite vantagens de recuperação de dados adicionais:

- O GRS (armazenamento com redundância geográfica) e o GRS de acesso de leitura (RA-GRS) maximizam a disponibilidade para sua conta de armazenamento.
- Se houver uma interrupção de região do serviço de armazenamento, uma das possibilidades é que as operações do datacenter determinem que o failover do armazenamento deve ser realizado na região secundária. Nesse caso, o acesso à conta de armazenamento será Redirecionado de forma transparente para a cópia replicada geograficamente da conta de armazenamento, sem a intervenção do usuário.
- Nesse caso, o estado das funções duráveis será preservado até a última replicação da conta de armazenamento, que ocorre a cada poucos minutos.

Assim como nos outros cenários, há considerações importantes:

- O failover para a réplica é feito por operadores do datacenter e pode levar algum tempo. Até esse momento, o aplicativo de funções sofrerá uma interrupção.
- Há um custo maior para o uso de contas de armazenamento replicadas geograficamente.
- GRS ocorre de forma assíncrona. Algumas das transações mais recentes podem ser perdidas devido à latência do processo de replicação.

![Diagrama mostrando o cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Passos seguintes

Você pode ler mais sobre como [criar aplicativos altamente disponíveis usando o Ra-grs](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
