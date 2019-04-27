---
title: Réplicas de leitura na Base de Dados do Azure para PostgreSQL
description: Este artigo descreve a funcionalidade de réplica de leitura na base de dados do Azure para PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 04/01/2019
ms.date: 04/22/2019
ms.openlocfilehash: f340f1e42b6993a1f834ab05570c669d4241222b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564362"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Réplicas de leitura na Base de Dados do Azure para PostgreSQL

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma base de dados do Azure para o servidor PostgreSQL para um servidor de só de leitura. Pode replicar do servidor mestre para até cinco réplicas dentro da mesma região do Azure. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação nativo do motor de PostgreSQL.

Réplicas são novos servidores que gerencie semelhante regular a base de dados do Azure para servidores PostgreSQL. Para cada réplica, leia, é-lhe cobrada a computação aprovisionada em vCores e o armazenamento em GB / mês.

Saiba como [criar e gerir réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando utilizar uma réplica de leitura
A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e dimensionamento de cargas de trabalho de leitura intensiva. Cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para o mestre.

Um cenário comum é fazer com que o BI e cargas de trabalho analíticas utilizam a réplica de leitura como a origem de dados de relatórios.

Uma vez que as réplicas são só de leitura, eles não reduzem diretamente nem encargos de escrita-capacidade no mestre de. Esta funcionalidade não está direcionada para cargas de trabalho intensivas de escrita.

A funcionalidade de réplica de leitura utiliza os replicação assíncrona do PostgreSQL. A funcionalidade não foi criada para cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica torna-se, eventualmente, consistentes com os dados no mestre de. Utilize esta funcionalidade para cargas de trabalho que podem acomodar este atraso.

## <a name="create-a-replica"></a>Criar uma réplica
O servidor mestre tem de ter o `azure.replication_support` parâmetro definido como **RÉPLICA**. Quando este parâmetro for alterado, um reinício do servidor é necessário para que a alteração tenha efeito. (O `azure.replication_support` parâmetro aplica-se para apenas os escalões fins gerais e com otimização de memória).

Quando inicia o fluxo de trabalho de réplica de criar, é criada uma base de dados do Azure em branco para o servidor PostgreSQL. O novo servidor é preenchido com os dados que estava no servidor principal. Hora de criação depende da quantidade de dados sobre o modelo global e o tempo desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

A funcionalidade de réplica de leitura utiliza a replicação de física do PostgreSQL, a replicação não lógica. Transmissão em fluxo de replicação ao utilizar ranhuras de replicação é o modo de operação do padrão. Quando for necessário, o envio de log é usado para acompanhar.

> [!NOTE]
> Se não tiver um conjunto de alerta de armazenamento a cópia de segurança nos seus servidores, recomendamos que o faça. O alerta informa-o quando um servidor está a atingir o seu limite de armazenamento, o que irá afetar a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ligar a uma réplica
Quando cria uma réplica, ele não herda as regras de firewall ou um ponto final de serviço de VNet do servidor mestre. Estas regras tem de ser definidas independentemente para a réplica.

A réplica herda a conta de administrador do servidor mestre. Todas as contas de utilizador no servidor principal são replicadas para as réplicas de leitura. Só se pode ligar para uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor principal.

Pode ligar para a réplica com o seu nome de anfitrião e uma conta de utilizador válido, tal como faria numa base de dados do Azure normal para o servidor PostgreSQL. Para um servidor com o nome **myreplica** com o nome de utilizador de administrador **myadmin**, pode ligar-se para a réplica com o psql:

```
psql -h myreplica.postgres.database.chinacloudapi.cn -U myadmin@myreplica -d postgres
```

Na linha de comandos, introduza a palavra-passe da conta de utilizador.

## <a name="monitor-replication"></a>Monitor de replicação
Base de dados do Azure para PostgreSQL fornece a **máximo de atraso em réplicas** métricas no Azure Monitor. Esta métrica está disponível no servidor principal apenas. A métrica mostra o desfasamento de bytes entre o modelo global e a réplica de lagging maioria. 

Base de dados do Azure para PostgreSQL também fornece a **desfasamento de réplica** métricas no Azure Monitor. Esta métrica está disponível para apenas as réplicas. 

A métrica é calculada a partir do `pg_stat_wal_receiver` vista:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

A métrica de desfasamento de réplica mostra o tempo desde a última transação repetido. Se não existirem não existem transações ocorridas no servidor principal, a métrica reflete este intervalo de tempo.

Defina um alerta para o informar de quando o atraso de réplica atinge um valor que não é aceitável para a sua carga de trabalho. 

Para obter informações adicionais, consulte o servidor mestre diretamente para obter o desfasamento de replicação em bytes em todas as réplicas.

Na versão 10 do PostgreSQL:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

Na versão 9.6 e anterior do PostgreSQL:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Se um servidor principal ou a réplica de leitura é reiniciado, o tempo que demora a reiniciar e acompanhe é refletido na métrica de desfasamento de réplica.

## <a name="stop-replication"></a>Parar replicação
Pode parar a replicação entre um mestre e uma réplica. A ação de paragem faz com que a réplica para reiniciar e remover as definições de replicação. Depois de parar a replicação entre um servidor principal e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados que estava disponíveis na réplica no momento, que o comando de replicação de paragem foi iniciado. O servidor autónomo não fique atualizado com o servidor mestre.

> [!IMPORTANT]
> O servidor autónomo não pode se transformar numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados que necessita.

Saiba como [parar a replicação para uma réplica](howto-read-replicas-portal.md).


## <a name="considerations"></a>Considerações

Esta secção resume considerações sobre a funcionalidade de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma réplica de leitura, o `azure.replication_support` parâmetro deve ser definido como **RÉPLICA** no servidor principal. Quando este parâmetro for alterado, um reinício do servidor é necessário para que a alteração tenha efeito. O `azure.replication_support` parâmetro aplica-se para apenas os escalões fins gerais e com otimização de memória.

### <a name="new-replicas"></a>Novo réplicas
É criada uma réplica de leitura como uma nova base de dados do Azure para o servidor PostgreSQL. Um servidor existente não pode se transformar numa réplica. Só é possível criar uma réplica de leitura na mesma região do Azure como o modelo. Não é possível criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Ao utilizar a mesma configuração de servidor como o mestre, é criada uma réplica. Depois de criar uma réplica, várias configurações podem ser alteradas independentemente do servidor mestre: computação geração, vCores, armazenamento e período de retenção de cópia de segurança. O escalão de preço também pode ser alterado de forma independente, exceto de ou para o escalão básico.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre é atualizada para novos valores, atualize a configuração de réplica para valores iguais ou superior. Esta ação garante que a réplica pode acompanhar todas as alterações efetuadas a mestre.

PostgreSQL requer o valor da `max_connections` parâmetro na réplica de leitura para ser maior que ou igual ao valor principal; caso contrário, a réplica não inicia. Na base de dados do Azure para PostgreSQL, o `max_connections` valor do parâmetro baseia-se no SKU. Para obter mais informações, consulte [limites na base de dados do Azure para PostgreSQL](concepts-limits.md). 

Se tenta atualizar os valores de servidor, mas não cumprem os limites, receberá um erro.

### <a name="stopped-replicas"></a>Réplicas paradas
Se parar a replicação entre um servidor principal e uma réplica de leitura, reinicia a réplica para aplicar a alteração. A réplica parada torna-se um servidor autónomo que aceita as leituras e gravações. O servidor autónomo não pode se transformar numa réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Eliminado servidores mestre e autónomo
Quando um servidor mestre for eliminado, todas as suas réplicas leitura tornam-se servidores autónomos. As réplicas são reiniciadas para refletir esta alteração.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [criar e gerir réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
