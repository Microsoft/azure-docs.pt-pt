---
title: Recuperação após desastre e distribuição geográfica nas funções duráveis - Azure
description: Saiba mais sobre a recuperação após desastre e distribuição geográfica nas funções durável.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108210"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Recuperação após desastre e distribuição geográfica

## <a name="overview"></a>Descrição geral

Nas funções durável, todos os Estados são mantidos no armazenamento do Azure. R [hub tarefas](durable-functions-task-hubs.md) é um contentor lógico para os recursos de armazenamento do Azure que são utilizadas para orquestrações. As funções do Orchestrator e atividade só podem interagir entre si quando eles pertencem ao mesmo hub de tarefa.
Os cenários descritos propor opções de implementação para aumentar a disponibilidade e minimizar o período de indisponibilidade durante as atividades de recuperação após desastre.

É importante observar que esses cenários são com base nas configurações de ativo-passivo, uma vez que eles são orientados pela utilização do armazenamento do Azure. Este padrão consiste na implementação de uma aplicação de cópia de segurança de funções (passivo) para uma região diferente. O Gestor de tráfego irá monitorizar a aplicação de função (Active Directory) principal para disponibilidade. Ocorrerá uma falha ao longo para a aplicação de funções de cópia de segurança se primária falhar. Para obter mais informações, consulte [Gestor de tráfego](https://azure.microsoft.com/services/traffic-manager/)do [o método de encaminhamento de tráfego de prioridade.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - A configuração ativa-passiva proposta garante que um cliente sempre é capaz de acionar novo orquestrações através de HTTP. No entanto, como consequência ter duas aplicações de funções partilham o mesmo armazenamento, processamento em segundo plano será distribuído entre os dois, competindo por mensagens em filas mesmo. Esta configuração incorre em custos de saída foi adicionado para a aplicação de função secundária.
> - O hub de conta e tarefas de armazenamento subjacente são criadas na região primária e são partilhados por ambas as aplicações de função.
> - Todas as aplicações de função adquiria implementadas, têm de partilhar as mesmas chaves de acesso de função no caso de ser ativado através de HTTP. O Runtime das funções expõe uma [gestão de API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) que permite que os consumidores através de programação para adicionar, eliminar e atualizar teclas de função.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Cenário 1 - com balanceamento de carga computação com armazenamento partilhado

Se a infraestrutura de computação no Azure falhar, a aplicação de funções pode ficar indisponível. Para minimizar a possibilidade de tal tempo de inatividade, este cenário utiliza duas aplicações de funções implementadas em regiões diferentes.
O Gestor de tráfego está configurado para detetar problemas na aplicação de função principal e redirecionar automaticamente o tráfego para a aplicação de funções na região secundária. Esta aplicação de função compartilha a mesma conta de armazenamento do Azure e o Hub de tarefa. Por conseguinte, o estado das aplicações de função não é perdido e trabalho pode ser retomar normalmente. Depois de restaurar o estado de funcionamento para a região primária, o Gestor de tráfego do Azure começará rotear as solicitações para essa aplicação de função automaticamente.

![Diagrama que mostra o cenário 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Há vários benefícios ao utilizar neste cenário de implementação:

- Se falhar a infraestrutura de computação, o trabalho pode retomar a falharão ao longo da região sem perda de estado.
- O Gestor de tráfego se encarrega da automática ativação pós-falha para a aplicação de função em bom estado automaticamente.
- O Gestor de tráfego novamente a estabelece automaticamente o tráfego para a aplicação de função principal após a falha foi corrigida.

No entanto, considere a utilização deste cenário:

- Se a aplicação de função é implementada com um plano de serviço de aplicações dedicado, replicar a infraestrutura de computação no falhe ao longo do Centro de dados aumenta os custos.
- Este cenário aborda as falhas na infraestrutura de computação, mas a conta de armazenamento continua a ser o ponto único de falha para a aplicação de função. Se houver uma falha no armazenamento, o aplicativo sofre um período de indisponibilidade.
- Se a aplicação de funções é a ativação pós-falha, haverá uma maior latência, uma vez que ele irá acessar sua conta de armazenamento em várias regiões.
- Acessando o serviço de armazenamento de uma região diferente, onde está localizado incorre num custo mais elevado devido ao tráfego de saída de rede.
- Neste cenário depende no Gestor de tráfego. Considerando [como funciona o Gestor de tráfego](../../traffic-manager/traffic-manager-how-it-works.md), poderá ser algum tempo até que uma aplicação de cliente que consome uma função durável precisa consultar novamente o endereço de aplicação de função do Gestor de tráfego.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Cenário 2 - com balanceamento de carga computação com o armazenamento regional

O cenário anterior abrange apenas o caso de falha na infraestrutura de computação. Se o serviço de armazenamento falhar, irá resultar num período de indisponibilidade da aplicação de função.
Para garantir a operação contínua das funções duráveis, este cenário utiliza uma conta de armazenamento local em cada região aos quais as aplicações de função são implementadas.

![Diagrama que mostra o cenário 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Esta abordagem adiciona melhorias no cenário anterior:

- Se a aplicação de funções falhar, o Gestor de tráfego se encarrega de ativação pós-falha para a região secundária. No entanto, uma vez que a aplicação de funções de suporte de dados baseia-se na sua própria conta de armazenamento, as funções duráveis continuam a funcionar.
- Durante uma ativação pós-falha, não existe nenhuma latência adicional no falhe ao longo de região, uma vez que a aplicação de funções e a conta de armazenamento estão localizadas conjuntamente.
- Falha da camada de armazenamento irá causar falhas no funções duráveis, o que, por sua vez, irão acionar um redirecionamento para a ativação pós-falha região. Novamente, uma vez que a aplicação de função e armazenamento são isolados por região, as funções duráveis continuarão a funcionar.

Considerações importantes para este cenário:

- Se a aplicação de função é implementada com um plano de serviço de aplicações dedicado, replicar a infraestrutura de computação no falhe ao longo do Centro de dados aumenta os custos.
- Estado atual não é efetuado a ativação pós-falha, que indica que a execução e as funções foi efetuada a verificação irão falhar. Cabe-lhe para a aplicação de cliente para o trabalho de repetição/reinicialização.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Cenário 3 - com balanceamento de carga computação com o armazenamento GRS partilhado

Este cenário é uma modificação ao longo do primeiro cenário, a implementação de uma conta de armazenamento partilhado. A principal diferença que a conta de armazenamento é criada com georreplicação ativada.
Funcionalmente, este cenário fornece as mesmas vantagens cenário 1, mas permite que as vantagens de recuperação de dados adicionais:

- Armazenamento georredundante (GRS) e GRS de acesso de leitura (RA-GRS) maximizar a disponibilidade para a sua conta de armazenamento.
- Se houver uma falha de região do serviço de armazenamento, uma das possibilidades é que as operações de datacenter determinam que armazenamento deve efetuar a ativação pós-falha para a região secundária. Neste caso, acesso de conta de armazenamento será redirecionado forma transparente para a cópia georreplicado da conta do storage, sem intervenção do utilizador.
- Neste caso, o estado das funções duráveis será mantido até a última replicação da conta de armazenamento, o que ocorre a intervalos de poucos minutos.

Tal como acontece com os outros cenários, existem considerações importantes:

- Ativação pós-falha para a réplica é feita por operadores de centro de dados e pode demorar algum tempo. Até esse momento, a aplicação de funções sofrerá um período de indisponibilidade.
- Há um custo maior para a utilização de contas de armazenamento georreplicado.
- GRS ocorre de forma assíncrona. Algumas das mais recentes transações poderão perder-se devido a latência do processo de replicação.

![Diagrama que mostra o cenário 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Passos Seguintes

Pode ler mais sobre [criação altamente disponíveis de aplicativos com RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
