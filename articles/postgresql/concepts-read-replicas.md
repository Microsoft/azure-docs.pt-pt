---
title: Ler réplicas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade de réplica de leitura na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: fd6d3e24adfc22d2f6ea17f09b8dea4638a054b6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769031"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Ler réplicas na Base de Dados Azure para PostgreSQL - Servidor Único

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma Base de Dados Azure para servidor PostgreSQL para um servidor apenas para leitura. Pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas assincronicamente com a tecnologia de replicação nativa do motor PostgreSQL.

As réplicas são novos servidores que gere supor semelhantes ao base de dados azure regular para servidores PostgreSQL. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e armazenamento em GB/mês.

Aprenda a [criar e gerir réplicas.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso ler réplica ajuda a melhorar o desempenho e a escala de cargas de trabalho com uso intensivo de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é fazer com que as cargas de trabalho de BI e analíticas usem a réplica de leitura como a fonte de dados para relatórios.

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação no mestre. Esse recurso não é destinado a cargas de trabalho com uso intensivo de gravação.

A réplica da leitura utiliza replicação assíncrona PostgreSQL. O recurso não é destinado a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados no mestre. Use esse recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente do servidor mestre. A replicação entre regiões pode ser útil para cenários como planejamento de recuperação de desastres ou trazer dados mais próximos aos seus usuários.

Pode ter um servidor principal em qualquer Base de [Dados Azure para a região de PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo de sua região mestra.

[![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Pode sempre criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente da localização do seu servidor principal. Estas são as regiões de réplica universal:

Leste da Austrália, sudeste da Austrália, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA.

\* Oeste dos EUA 2 está temporariamente indisponível como um local de réplica entre regiões.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre. Se você não souber o par de sua região, poderá aprender mais no [artigo regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Existem limitações a considerar: 

* Disponibilidade regional: Base de Dados Azure para PostgreSQL está disponível em West US 2, France Central, UAE North e Germany Central. No entanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: algumas regiões do Azure são emparelhadas apenas em uma direção. Estas regiões incluem a Índia Ocidental, Brasil Sul. 
   Isso significa que um servidor mestre na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor mestre na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária da Índia ocidental é sul da Índia, mas a região secundária do Sul da Índia não é oeste da Índia.


## <a name="create-a-replica"></a>Criar uma réplica
Quando iniciar o fluxo de trabalho de réplica de réplica, é criada uma base de dados Azure em branco para o servidor PostgreSQL. O novo servidor é preenchido com os dados que estavam no servidor mestre. O tempo de criação depende da quantidade de dados no mestre e do tempo desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica é habilitada para [crescimento automático](concepts-pricing-tiers.md#storage-auto-grow)do armazenamento. A função de crescimento automático permite que a réplica acompanhe os dados que lhe são replicados e evitar uma rutura na replicação causada por erros de armazenamento.

A réplica da leitura utiliza replicação física PostgreSQL, não replicação lógica. A replicação de streaming utilizando ranhuras de replicação é o modo de funcionamento predefinido. Quando necessário, o envio de diário de bordo é utilizado para recuperar.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando cria uma réplica, não herda as regras de firewall ou o ponto final do serviço VNet do servidor principal. Estas regras devem ser criadas de forma independente para a réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor mestre.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados azure regular para servidor PostgreSQL. Para um servidor chamado **minha réplica** com o nome de utilizador do administrador **myadmin,** pode ligar-se à réplica utilizando o psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No prompt, digite a senha da conta de usuário.

## <a name="monitor-replication"></a>Replicação do monitor
A Base de Dados Azure para PostgreSQL fornece duas métricas para a replicação de monitorização. As duas métricas são **Max Lag Across Replicas** e **Replica Lag**. Para aprender a visualizar estas métricas, consulte o Monitor uma secção **de réplica** do artigo da réplica [lida](howto-read-replicas-portal.md).

A métrica **Max Lag Across Replicas** mostra o lag in bytes entre o mestre e a réplica mais atrasada. Esta métrica está disponível apenas no servidor principal.

A métrica **do Lag réplica** mostra o tempo desde a última transação reproduzida. Se não houver transações no seu servidor principal, a métrica reflete este atraso de tempo. Esta métrica está disponível apenas para servidores de réplicas. A réplica Lag é calculada a partir da visão `pg_stat_wal_receiver`:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Detete um alerta para informá-lo quando o lag da réplica atingir um valor que não é aceitável para a sua carga de trabalho. 

Para obter informações adicionais, consultar o servidor principal diretamente para obter o lag de replicação em bytes em todas as réplicas.

Na versão PostgreSQL 10:

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
> Se um servidor principal ou a réplica de leitura recomeçar, o tempo que leva para reiniciar e recuperar é refletido na métrica De Lag réplica.

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um mestre e uma réplica. A ação stop faz com que a réplica reinicie e remova as suas definições de replicação. Depois que a replicação é interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando parar replicação foi iniciado. O servidor autônomo não é atualizado com o servidor mestre.

> [!IMPORTANT]
> O servidor autônomo não pode ser tornado novamente em uma réplica.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados necessários.

Quando se para a replicação, a réplica perde todas as ligações ao seu anterior mestre e outras réplicas.

Saiba como [interromper a replicação em uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Ativação Pós-Falha
Não existe uma falha automatizada entre os servidores mestre e réplica. 

Como a replicação é assíncrona, há um desfasamento entre o mestre e a réplica. A quantidade de lag pode ser influenciada por uma série de fatores como o quão pesada é a carga de trabalho no servidor principal e a latência entre centros de dados. Na maioria dos casos, o lag de réplicavaria entre alguns segundos a alguns minutos. Pode rastrear o seu lag de replicação real utilizando a réplica métrica *Lag,* que está disponível para cada réplica. Esta métrica mostra o tempo desde a última transação reproduzida. Recomendamos que identifique qual é o seu atraso médio observando o seu lag de réplica sem um período de tempo. Pode definir um alerta sobre o lag de réplicas, para que, se sair do seu alcance esperado, possa tomar medidas.

> [!Tip]
> Se falhar na réplica, o lag no momento em que desliga a réplica do mestre indicará quantos dados se perdem.

Uma vez que você decidiu que quer falhar com uma réplica, 

1. Pare a replicação da réplica<br/>
   Este passo é necessário para que o servidor de réplica suposições aceites. Como parte deste processo, o servidor de réplica recomeçará e será desligado do mestre. Uma vez iniciado a replicação de paragem, o processo de backend normalmente leva cerca de 2 minutos para ser concluído. Consulte a secção de [reparação](#stop-replication) de paragem deste artigo para entender as implicações desta ação.
    
2. Aponte a sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma cadeia de ligação única. Atualize a sua aplicação para apontar para a réplica (antiga) em vez do mestre.
    
Uma vez que a sua aplicação é processada com sucesso, lê e escreve, você completou a falha. A quantidade de tempo de inatividade das suas experiências de aplicação dependerá quando detetar um problema e completar os passos 1 e 2 acima.


## <a name="considerations"></a>Considerações

Esta secção resume considerações sobre a réplica da leitura.

### <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma réplica de leitura, o parâmetro `azure.replication_support` deve ser definido para **REPLICA** no servidor principal. Quando este parâmetro é alterado, é necessário reiniciar o servidor para que a alteração faça efeito. O parâmetro `azure.replication_support` aplica-se apenas aos níveis otimizados para fins gerais e memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como uma nova Base de Dados Azure para o servidor PostgreSQL. Não é possível estabelecer um servidor existente em uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração de réplica
Uma réplica é criada usando as mesmas definições de cálculo e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes de uma definição de mestre ser atualizada para um novo valor, atualize a configuração da réplica para um valor igual ou maior. Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no mestre.

PostgreSQL exige que o valor do parâmetro `max_connections` na réplica de leitura seja maior ou igual ao valor principal; caso contrário, a réplica não vai começar. Na Base de Dados Azure para PostgreSQL, o valor do parâmetro `max_connections` baseia-se no SKU. Para mais informações, consulte [Limites na Base de Dados Azure para PostgreSQL](concepts-limits.md). 

Se tentar atualizar os valores do servidor acima descritos, mas não aderir aos limites, receberá um erro.

As regras de firewall, as regras de rede virtual e as definições de parâmetros não são herdadas do servidor principal para a réplica quando a réplica é criada ou posteriormente.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL exige](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que o valor do parâmetro `max_prepared_transactions` na réplica de leitura seja maior ou igual ao valor principal; caso contrário, a réplica não vai começar. Se quiser mudar `max_prepared_transactions` no mestre, mude-o primeiro nas réplicas.

### <a name="stopped-replicas"></a>Réplicas interrompidas
Se parar a replicação entre um servidor principal e uma réplica de leitura, a réplica reinicia para aplicar a alteração. A réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autônomo não pode ser tornado novamente em uma réplica.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor principal é eliminado, todas as suas réplicas de leitura tornam-se servidores autónomos. As réplicas são reiniciadas para refletir esta mudança.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [criar e gerir réplicas de leitura no portal Azure.](howto-read-replicas-portal.md)
* Aprenda a [criar e gerir réplicas de leitura no Azure CLI e REST API](howto-read-replicas-cli.md).
