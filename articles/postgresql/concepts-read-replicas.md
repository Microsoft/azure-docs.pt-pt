---
title: Ler réplicas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade de réplica de leitura na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: fd6d3e24adfc22d2f6ea17f09b8dea4638a054b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76769031"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Ler réplicas na Base de Dados Azure para PostgreSQL - Servidor Único

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma Base de Dados Azure para servidor PostgreSQL para um servidor apenas para leitura. Pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas assincronicamente com a tecnologia de replicação nativa do motor PostgreSQL.

As réplicas são novos servidores que gere supor semelhantes ao base de dados azure regular para servidores PostgreSQL. Para cada réplica de leitura, você é cobrado para a computação provisionada em vCores e armazenamento em GB/mês.

Aprenda a [criar e gerir réplicas.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala de cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas das réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para o mestre.

Um cenário comum é ter bi e cargas de trabalho analíticas usar a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita sobre o mestre. Esta funcionalidade não é direcionada para cargas de trabalho intensivas.

A réplica da leitura utiliza replicação assíncrona PostgreSQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre o mestre e a réplica. Os dados da réplica acabam por se tornar consistentes com os dados do mestre. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

## <a name="cross-region-replication"></a>Replicação transversal
Pode criar uma réplica de leitura numa região diferente do seu servidor principal. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou aproximar os dados dos seus utilizadores.

Pode ter um servidor principal em qualquer Base de [Dados Azure para a região de PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor principal pode ter uma réplica na sua região emparelhada ou nas regiões de réplica universal. A imagem abaixo mostra quais as regiões réplicas disponíveis dependendo da sua região principal.

[![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica seleções universais
Pode sempre criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente da localização do seu servidor principal. Estas são as regiões de réplica universal:

Austrália Leste, Austrália Sudeste, Centro dos EUA, Ásia Oriental, Leste dos EUA, Leste DOS 2, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Norte Dos EUA, Norte Da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Oeste dos EUA.

*West US 2 está temporariamente indisponível como uma réplica de região transversal.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, pode criar uma réplica de leitura na região de Azure emparelhada com o seu servidor principal. Se não conhece o par da sua região, pode aprender mais com o [artigo Das Regiões Pares de Azure.](../best-practices-availability-paired-regions.md)

Se estiver a utilizar réplicas de regiões cruzadas para planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Existem limitações a considerar: 

* Disponibilidade regional: Base de Dados Azure para PostgreSQL está disponível em West US 2, France Central, UAE North e Germany Central. No entanto, as suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azure são emparelhadas numa só direção. Estas regiões incluem a Índia Ocidental, Brasil Sul. 
   Isto significa que um servidor principal na Índia Ocidental pode criar uma réplica no sul da Índia. No entanto, um servidor principal no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é o sul da Índia, mas a região secundária do Sul da Índia não é a Índia Ocidental.


## <a name="create-a-replica"></a>Criar uma réplica
Quando iniciar o fluxo de trabalho de réplica de réplica, é criada uma base de dados Azure em branco para o servidor PostgreSQL. O novo servidor está cheio de dados que estavam no servidor principal. O tempo de criação depende da quantidade de dados sobre o mestre e o tempo desde o último backup semanal completo. O tempo pode variar de alguns minutos a várias horas.

Cada réplica está ativada para armazenamento [de automóveis.](concepts-pricing-tiers.md#storage-auto-grow) A função de crescimento automático permite que a réplica acompanhe os dados que lhe são replicados e evitar uma rutura na replicação causada por erros de armazenamento.

A réplica da leitura utiliza replicação física PostgreSQL, não replicação lógica. A replicação de streaming utilizando ranhuras de replicação é o modo de funcionamento predefinido. Quando necessário, o envio de diário de bordo é utilizado para recuperar.

Aprenda a [criar uma réplica de leitura no portal Azure.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar a uma réplica
Quando cria uma réplica, não herda as regras de firewall ou o ponto final do serviço VNet do servidor principal. Estas regras devem ser criadas de forma independente para a réplica.

A réplica herda a conta de administração do servidor principal. Todas as contas de utilizador no servidor principal são replicadas para as réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor principal.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados azure regular para servidor PostgreSQL. Para um servidor chamado **minha réplica** com o nome de utilizador do administrador **myadmin,** pode ligar-se à réplica utilizando o psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No momento, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação
A Base de Dados Azure para PostgreSQL fornece duas métricas para a replicação de monitorização. As duas métricas são **Max Lag Across Replicas** e **Replica Lag**. Para aprender a visualizar estas métricas, consulte o Monitor uma secção **de réplica** do artigo da réplica [lida](howto-read-replicas-portal.md).

A métrica **Max Lag Across Replicas** mostra o lag in bytes entre o mestre e a réplica mais atrasada. Esta métrica está disponível apenas no servidor principal.

A métrica **do Lag réplica** mostra o tempo desde a última transação reproduzida. Se não houver transações no seu servidor principal, a métrica reflete este atraso de tempo. Esta métrica está disponível apenas para servidores de réplicas. A réplica Lag `pg_stat_wal_receiver` é calculada a partir da vista:

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
Podes parar a replicação entre um mestre e uma réplica. A ação stop faz com que a réplica reinicie e remova as suas definições de replicação. Após a replicação ser interrompida entre um servidor principal e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados que estavam disponíveis na réplica no momento em que o comando de replicação stop foi iniciado. O servidor autónomo não alcança o servidor principal.

> [!IMPORTANT]
> O servidor autónomo não pode voltar a ser transformado numa réplica.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados que necessita.

Quando se para a replicação, a réplica perde todas as ligações ao seu anterior mestre e outras réplicas.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="failover"></a>Ativação pós-falha
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
Antes de criar uma `azure.replication_support` réplica de leitura, o parâmetro deve ser definido para **RÉPLICA** no servidor principal. Quando este parâmetro é alterado, é necessário reiniciar o servidor para que a alteração faça efeito. O `azure.replication_support` parâmetro aplica-se apenas aos níveis otimizados para o Propósito Geral e memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como uma nova Base de Dados Azure para o servidor PostgreSQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração de réplica
Uma réplica é criada usando as mesmas definições de cálculo e armazenamento que o mestre. Após a criação de uma réplica, várias definições podem ser alteradas independentemente do servidor principal: geração de computação, vCores, armazenamento e período de retenção de backup. O nível de preços também pode ser alterado de forma independente, exceto para ou a partir do nível Básico.

> [!IMPORTANT]
> Antes de uma definição de mestre ser atualizada para um novo valor, atualize a configuração da réplica para um valor igual ou maior. Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no mestre.

PostgreSQL exige que o `max_connections` valor do parâmetro na réplica de leitura seja maior ou igual ao valor principal; caso contrário, a réplica não vai começar. Na Base de Dados Azure para `max_connections` PostgreSQL, o valor do parâmetro baseia-se no SKU. Para mais informações, consulte [Limites na Base de Dados Azure para PostgreSQL](concepts-limits.md). 

Se tentar atualizar os valores do servidor acima descritos, mas não aderir aos limites, receberá um erro.

As regras de firewall, as regras de rede virtual e as definições de parâmetros não são herdadas do servidor principal para a réplica quando a réplica é criada ou posteriormente.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL exige](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que o `max_prepared_transactions` valor do parâmetro na réplica de leitura seja maior ou igual ao valor principal; caso contrário, a réplica não vai começar. Se quiser mudar `max_prepared_transactions` o mestre, mude-o primeiro nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se parar a replicação entre um servidor principal e uma réplica de leitura, a réplica reinicia para aplicar a alteração. A réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode voltar a ser transformado numa réplica.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestres e autónomos eliminados
Quando um servidor principal é eliminado, todas as suas réplicas de leitura tornam-se servidores autónomos. As réplicas são reiniciadas para refletir esta mudança.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [criar e gerir réplicas de leitura no portal Azure.](howto-read-replicas-portal.md)
* Aprenda a [criar e gerir réplicas de leitura no Azure CLI e REST API](howto-read-replicas-cli.md).
