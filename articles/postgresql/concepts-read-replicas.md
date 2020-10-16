---
title: Ler réplicas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade de réplica de leitura na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 3b660875288db1f16f13d58b1538a876e2ff2666
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123304"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leia réplicas na Base de Dados Azure para PostgreSQL - Servidor Único

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma Base de Dados Azure para servidor PostgreSQL para um servidor apenas de leitura. Pode replicar-se do servidor primário até cinco réplicas. As réplicas são atualizadas assincronamente com a tecnologia de replicação nativa do PostgreSQL.

As réplicas são novos servidores que gere, à semelhança dos servidores da Base de Dados do Azure para PostgreSQL normais. Para cada réplica de leitura, você é cobrado para o cálculo provisionado em vCores e armazenamento em GB/mês.

Saiba como [criar e gerir réplicas.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para as primárias.

Um cenário comum é fazer com que as cargas de trabalho bi e analíticas utilizem a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita nas primárias. Esta funcionalidade não está direcionada para cargas de trabalho de escrita intensa.

A funcionalidade de réplica de leitura utiliza a replicação assíncronea postgresQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre as primárias e a réplica. Os dados sobre a réplica eventualmente tornam-se consistentes com os dados sobre o primário. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Pode criar uma réplica de leitura numa região diferente do seu servidor primário. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou a aproximação de dados aos seus utilizadores.

>[!NOTE]
> Os servidores de nível básico só suportam a replicação da mesma região.

Pode ter um servidor primário em qualquer [Base de Dados Azure para a região postgreSQL.](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) Um servidor primário pode ter uma réplica na sua região emparelhada ou nas regiões réplicas universais. A imagem abaixo mostra quais as regiões réplicas disponíveis dependendo da sua região primária.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões réplicas":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões réplicas universais
Pode sempre criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente do local onde o seu servidor principal esteja localizado. Estas são as regiões universais de réplica:

Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Eua Central, Leste Asiático, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul, Norte Central dos EUA, Norte da Europa Central, Eua Central Do Sul, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Eua Ocidental, Eua Ocidental 2, West Central EUA.

### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, pode criar uma réplica de leitura na região emparelhada Azure do seu servidor primário. Se não conhece o par da sua região, pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se estiver a utilizar réplicas transversais para o planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Existem limitações a considerar: 

* Disponibilidade regional: A Azure Database for PostgreSQL está disponível em France Central, UAE North e Germany Central. No entanto, as suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azures são emparelhadas apenas numa direção. Estas regiões incluem a Índia Ocidental, Brasil Sul. 
   Isto significa que um servidor primário na Índia Ocidental pode criar uma réplica no Sul da Índia. No entanto, um servidor primário no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é a Índia do Sul, mas a região secundária do Sul da Índia não é a Índia Ocidental.


## <a name="create-a-replica"></a>Criar uma réplica
Quando inicia o fluxo de trabalho de réplicas, é criada uma Base de Dados Azure em branco para servidor PostgreSQL. O novo servidor está cheio dos dados que estavam no servidor primário. O tempo de criação depende da quantidade de dados sobre o primário e o tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas.

Todas as réplicas estão ativadas para o armazenamento [de crescimento automático.](concepts-pricing-tiers.md#storage-auto-grow) A funcionalidade de crescimento automático permite que a réplica acompanhe os dados replicados e evite uma rutura na replicação causada por erros de armazenamento.

A funcionalidade de réplica de leitura utiliza a replicação física postgreSQL, não a replicação lógica. A replicação de streaming utilizando ranhuras de replicação é o modo de funcionamento predefinido. Quando necessário, o envio de registos é utilizado para o seu catch up.

Saiba como [criar uma réplica de leitura no portal Azure.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar-se a uma réplica
Quando cria uma réplica, não herda as regras de firewall ou o ponto final de serviço VNet do servidor primário. Estas regras devem ser criadas de forma independente para a réplica.

A réplica herda a conta de administração do servidor primário. Todas as contas de utilizador no servidor primário são replicadas nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor primário.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados Azure regular para servidor PostgreSQL. Para um servidor chamado **minha réplica** com o nome de utilizador de administração **myadmin,** pode ligar-se à réplica utilizando psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

A pedido, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação
A Azure Database for PostgreSQL fornece duas métricas para monitorização da replicação. As duas métricas são **Max Lag Através de Réplicas** e Replica **Lag**. Para aprender a ver estas métricas, consulte o **Monitor uma** secção réplica do artigo de réplica [de leitura](howto-read-replicas-portal.md).

A métrica **Max Lag Across Replicas** mostra o desfasamento nos bytes entre a réplica primária e a réplica mais atrasada. Esta métrica está disponível apenas no servidor primário, e só estará disponível se pelo menos uma das réplicas de leitura estiver ligada à primária.

A métrica **Replica Lag** mostra o tempo desde a última transação reproduzida. Se não houver transações no seu servidor primário, a métrica reflete este desfasamento temporal. Esta métrica está disponível apenas para servidores de réplicas. O Lag de réplica é calculado a partir da `pg_stat_wal_receiver` vista:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Desaça um alerta para informá-lo quando o lag da réplica atingir um valor que não é aceitável para a sua carga de trabalho. 

Para obter informações adicionais, consultar o servidor primário diretamente para obter o lag de replicação em bytes em todas as réplicas.

Na versão 10 do PostgreSQL:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

Na versão 9.6 do PostgreSQL e anterior:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Se um servidor primário ou uma réplica de leitura recomeçarem, o tempo necessário para reiniciar e recuperar é refletido na métrica Replica Lag.

## <a name="stop-replication"></a>Parar replicação
Pode parar a replicação entre uma primária e uma réplica. A ação de paragem faz com que a réplica reinicie e remova as suas definições de replicação. Após a replicação ser interrompida entre um servidor primário e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados disponíveis na réplica no momento em que o comando de replicação de paragem foi iniciado. O servidor autónomo não alcança o servidor primário.

> [!IMPORTANT]
> O servidor autónomo não pode ser transformado numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados necessários.

Quando paras de replicação, a réplica perde todas as ligações com as suas réplicas primárias anteriores e outras.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="failover"></a>Ativação pós-falha
Não existe falha automatizada entre servidores primários e réplicas. 

Como a replicação é assíncronea, há um desfasamento entre a primária e a réplica. A quantidade de lag pode ser influenciada por uma série de fatores como o peso da carga de trabalho que funciona no servidor primário e a latência entre centros de dados. Em casos típicos, o desfasamento da réplica varia entre alguns segundos e alguns minutos. No entanto, nos casos em que as primárias têm cargas de trabalho muito pesadas e a réplica não está a recuperar suficientemente depressa, o lag pode ser maior. Pode rastrear o seu lag de replicação real usando o *Lag métrico de Réplica,* que está disponível para cada réplica. Esta métrica mostra o tempo desde a última transação reproduzida. Recomendamos que identifique qual é o seu atraso médio observando o seu atraso de réplica durante um período de tempo. Pode definir um alerta no lag de réplica, para que, se for fora do alcance esperado, possa tomar medidas.

> [!Tip]
> Se falhar na réplica, o lag no momento em que desvincular a réplica da primária indicará quantos dados são perdidos.

Uma vez que tenha decidido que quer falhar para uma réplica, 

1. Pare a replicação na réplica<br/>
   Este passo é necessário para que o servidor de réplica seja capaz de aceitar escritas. Como parte deste processo, o servidor de réplica reiniciará e será desvinculado da primária. Uma vez iniciado a replicação stop, o processo de backend normalmente demora cerca de 2 minutos a ser concluído. Consulte a secção de replicação de [paragem](#stop-replication) deste artigo para entender as implicações desta ação.
    
2. Aponte a sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma cadeia de ligação única. Atualize a sua aplicação para apontar para a (antiga) réplica em vez da primária.
    
Uma vez que a sua aplicação esteja a processar com sucesso as leituras e as escritas, completou o failover. A quantidade de tempo de inatividade das suas experiências de aplicação dependerá de quando detetar um problema e completar os passos 1 e 2 acima.

### <a name="disaster-recovery"></a>Recuperação após desastre

Quando há um grande evento de desastre, como a disponibilidade de zonas ou falhas regionais, você pode realizar uma operação de recuperação de desastres promovendo a sua réplica de leitura. A partir do portal UI, pode navegar para o servidor de réplicas de leitura. Em seguida, clique no separador de replicação e pode parar a réplica para promovê-la para ser um servidor independente. Em alternativa, pode utilizar o [CLI Azure](https://docs.microsoft.com/cli/azure/postgres/server/replica?view=azure-cli-latest#az_postgres_server_replica_stop) para parar e promover o servidor de réplicas.

## <a name="considerations"></a>Considerações

Esta secção resume considerações sobre a funcionalidade de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Leia réplicas e [descodões lógicas](concepts-logical.md) ambos dependem do postgres escrever antecipadamente log (WAL) para obter informações. Estas duas funcionalidades precisam de diferentes níveis de registo de postgres. A descodão lógica precisa de um nível mais elevado de registo do que réplicas lidas.

Para configurar o nível certo de registo, utilize o parâmetro de suporte de replicação Azure. O suporte de replicação Azure tem três opções de definição:

* **Off** - Coloca a menor informação no WAL. Esta definição não está disponível na maioria dos servidores Azure Database para servidores PostgreSQL.  
* **Réplica** - Mais verboso do que **desligado**. Este é o nível mínimo de exploração madeireira necessário para [que as réplicas de leitura](concepts-read-replicas.md) funcionem. Esta definição é o padrão na maioria dos servidores.
* **Lógico** - Mais verboso do que **réplica.** Este é o nível mínimo de exploração madeireira para a descodão lógica para funcionar. As réplicas de leitura também funcionam neste cenário.

O servidor precisa de ser reiniciado após uma alteração deste parâmetro. Internamente, este parâmetro define os parâmetros postgres, `wal_level` `max_replication_slots` e `max_wal_senders` .

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como uma nova Base de Dados Azure para servidor PostgreSQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica lida.

### <a name="replica-configuration"></a>Configuração de réplica
Uma réplica é criada utilizando as mesmas definições de computação e armazenamento que as primárias. Após a criação de uma réplica, várias configurações podem ser alteradas, incluindo o período de armazenamento e retenção de backup.

As regras de firewall, as regras de rede virtual e as definições de parâmetros não são herdadas do servidor primário para a réplica quando a réplica é criada ou posteriormente.

### <a name="scaling"></a>Dimensionamento
Escalar vCores ou entre Propósito Geral e Memória Otimizada:
* O PostgreSQL requer que a `max_connections` definição num servidor secundário seja [maior ou igual à definição na primária,](https://www.postgresql.org/docs/current/hot-standby.html)caso contrário o secundário não será iniciado.
* Na Base de Dados Azure para PostgreSQL, as ligações máximas permitidas para cada servidor são fixadas ao sku compute uma vez que as ligações ocupam a memória. Você pode saber mais sobre o [mapeamento entre max_connections e compute skus](concepts-limits.md).
* **Escalonamento**: Primeiro escalone o cálculo de uma réplica, em seguida, escalone o primário. Esta ordem evitará que os erros violem a `max_connections` exigência.
* **Escalonamento :** Primeiro escale o cálculo da primária, em seguida, reduza a réplica. Se tentar escalar a réplica abaixo da primária, haverá um erro, uma vez que isto viola o `max_connections` requisito.

Armazenamento de escalonamento:
* Todas as réplicas têm armazenamento automático habilitado para evitar problemas de replicação de uma réplica completa de armazenamento. Esta definição não pode ser desativada.
* Também pode escalar manualmente o armazenamento, como faria em qualquer outro servidor


### <a name="basic-tier"></a>Escalão básico
Os servidores de nível básico só suportam a replicação da mesma região.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL exige que](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) o valor do `max_prepared_transactions` parâmetro na réplica de leitura seja maior ou igual ao valor primário; caso contrário, a réplica não será iniciada. Se quiser mudar `max_prepared_transactions` nas primárias, primeiro altere-a nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se parar a replicação entre um servidor primário e uma réplica de leitura, a réplica reinicia para aplicar a alteração. A réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

### <a name="deleted-primary-and-standalone-servers"></a>Servidores primários e autónomos eliminados
Quando um servidor primário é eliminado, todas as suas réplicas de leitura tornam-se servidores autónomos. As réplicas são reiniciadas para refletir esta mudança.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar e gerir réplicas de leitura no portal Azure.](howto-read-replicas-portal.md)
* Saiba como [criar e gerir réplicas de leitura na ALI Azure e NA API REST.](howto-read-replicas-cli.md)
