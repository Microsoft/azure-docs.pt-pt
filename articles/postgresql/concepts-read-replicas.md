---
title: Ler réplicas no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de réplica de leitura no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1571fc449bd40063c531f9942fe9b51da56f783c
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764323"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Ler réplicas no banco de dados do Azure para PostgreSQL-servidor único

O recurso de réplica de leitura permite replicar dados de um servidor do Azure para PostgreSQL para um servidor somente leitura. Pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação nativa do mecanismo PostgreSQL.

Réplicas são novos servidores que você gerencia de forma semelhante ao banco de dados do Azure regular para servidores PostgreSQL. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e armazenamento em GB/mês.

Saiba como [criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso ler réplica ajuda a melhorar o desempenho e a escala de cargas de trabalho com uso intensivo de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é fazer com que as cargas de trabalho de BI e analíticas usem a réplica de leitura como a fonte de dados para relatórios.

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação no mestre. Esse recurso não é destinado a cargas de trabalho com uso intensivo de gravação.

O recurso ler réplica usa replicação assíncrona do PostgreSQL. O recurso não é destinado a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados no mestre. Use esse recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente do servidor mestre. A replicação entre regiões pode ser útil para cenários como planejamento de recuperação de desastres ou trazer dados mais próximos aos seus usuários.

Você pode ter um servidor mestre em qualquer [região do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo de sua região mestra.

[![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você sempre pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor mestre está localizado. Estas são as regiões de réplica universal:

Leste da Austrália, sudeste da Austrália, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre. Se você não souber o par de sua região, poderá aprender mais no [artigo regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Há limitações a serem consideradas: 

* Disponibilidade regional: O banco de dados do Azure para PostgreSQL está disponível no oeste dos EUA 2, França central, Norte dos EAU e Alemanha central. No entanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões do Azure são emparelhadas somente em uma direção. Essas regiões incluem Índia ocidental, sul do Brasil. 
   Isso significa que um servidor mestre na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor mestre na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária da Índia ocidental é sul da Índia, mas a região secundária do Sul da Índia não é oeste da Índia.


## <a name="create-a-replica"></a>Criar uma réplica
O servidor mestre deve ter o `azure.replication_support` parâmetro definido como **réplica**. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. (O `azure.replication_support` parâmetro aplica-se somente às camadas de uso geral e com otimização de memória).

Quando você inicia o fluxo de trabalho criar réplica, um servidor do banco de dados do Azure para PostgreSQL em branco é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. O tempo de criação depende da quantidade de dados no mestre e do tempo desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica é habilitada para [crescimento automático](concepts-pricing-tiers.md#storage-auto-grow)do armazenamento. O recurso de aumento automático permite que a réplica acompanhe os dados replicados para ele e evite uma interrupção na replicação causada por erros de armazenamento insuficiente.

O recurso ler réplica usa replicação física PostgreSQL, não replicação lógica. A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para acompanhar.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall ou o ponto de extremidade de serviço VNet do servidor mestre. Essas regras devem ser configuradas independentemente para a réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como faria em um servidor de banco de dados do Azure comum para PostgreSQL. Para um servidor chamado **minha réplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No prompt, digite a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação
O banco de dados do Azure para PostgreSQL fornece duas métricas para monitorar a replicação. As duas métricas são o **retardo máximo entre réplicas** e **atraso de réplica**. Para saber como exibir essas métricas, consulte a seção **monitorar uma réplica** do artigo de [instruções de leitura de réplica](howto-read-replicas-portal.md).

A métrica de **atraso máximo entre réplicas** mostra o atraso em bytes entre o mestre e a réplica mais deatrasante. Essa métrica está disponível somente no servidor mestre.

A métrica de **atraso de réplica** mostra o tempo desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no servidor mestre, a métrica refletirá esse intervalo de tempo. Essa métrica está disponível somente para servidores de réplica. A latência de réplica é calculada a `pg_stat_wal_receiver` partir da exibição:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Defina um alerta para informá-lo quando a latência de réplica atingir um valor que não é aceitável para sua carga de trabalho. 

Para obter informações adicionais, consulte o servidor mestre diretamente para obter o retardo de replicação em bytes em todas as réplicas.

No PostgreSQL versão 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

No PostgreSQL versão 9,6 e anterior:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Se um servidor mestre ou uma réplica de leitura for reiniciado, o tempo necessário para reiniciar e se acumular será refletido na métrica de retardo de réplica.

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um mestre e uma réplica. A ação de parada faz com que a réplica seja reiniciada e remova suas configurações de replicação. Depois que a replicação é interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando parar replicação foi iniciado. O servidor autônomo não é atualizado com o servidor mestre.

> [!IMPORTANT]
> O servidor autônomo não pode ser tornado novamente em uma réplica.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados necessários.

Quando você interrompe a replicação, a réplica perde todos os links para o mestre anterior e outras réplicas.

Saiba como [interromper a replicação em uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Ativação pós-falha
Não há nenhum failover automatizado entre servidores mestre e de réplica. 

Como a replicação é assíncrona, há um atraso entre o mestre e a réplica. A quantidade de latência pode ser influenciada por vários fatores, como a intensidade da carga de trabalho em execução no servidor mestre e a latência entre os data centers. Na maioria dos casos, a latência de réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica*de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica do mestre indicará a quantidade de dados perdidos.

Depois que você decidir que deseja fazer failover para uma réplica, 

1. Parar a replicação na réplica<br/>
   Essa etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será reiniciado e será desvinculado do mestre. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção [parar replicação](#stop-replication) deste artigo para entender as implicações dessa ação.
    
2. Aponte seu aplicativo para a réplica (antiga)<br/>
   Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez do mestre.
    
Depois que o aplicativo processar leituras e gravações com êxito, você terá concluído o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 acima.


## <a name="considerations"></a>Considerações

Esta seção resume as considerações sobre o recurso de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma réplica de leitura, `azure.replication_support` o parâmetro deve ser definido como **réplica** no servidor mestre. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. O `azure.replication_support` parâmetro aplica-se somente às camadas de uso geral e com otimização de memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de banco de dados do Azure para PostgreSQL. Não é possível estabelecer um servidor existente em uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração de réplica
Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes que uma configuração mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no mestre.

PostgreSQL requer que o valor do `max_connections` parâmetro na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. No banco de dados do Azure para `max_connections` PostgreSQL, o valor do parâmetro é baseado na SKU. Para obter mais informações, consulte [limites no banco de dados do Azure para PostgreSQL](concepts-limits.md). 

Se você tentar atualizar os valores do servidor, mas não atender aos limites, você receberá um erro.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL requer](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que o valor do `max_prepared_transactions` parâmetro na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. Se você quiser alterar `max_prepared_transactions` o mestre, primeiro altere-o nas réplicas.

### <a name="stopped-replicas"></a>Réplicas interrompidas
Se você parar a replicação entre um servidor mestre e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica parada torna-se um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure](howto-read-replicas-cli.md).
