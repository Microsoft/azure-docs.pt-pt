---
title: Ler réplicas - Azure Database for MySQL
description: 'Saiba mais sobre réplicas lidas na Base de Dados Azure para o MySQL: escolher regiões, criar réplicas, ligar-se a réplicas, monitorizar a replicação e parar a replicação.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2021
ms.custom: references_regions
ms.openlocfilehash: c380a3edb556adb72d067cb2910c8afbf66b99a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98250269"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas de leitura na Base de Dados do Azure para MySQL

A funcionalidade de réplica de leitura permite replicar dados de um servidor de Base de Dados do Azure para MySQL para um servidor só de leitura. Pode replicar do servidor de origem para até cinco réplicas. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação baseada na posição dos ficheiros de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que geres similares ao Azure Database regular para servidores MySQL. Para cada réplica de leitura, você é cobrado para o cálculo provisionado em vCores e armazenamento em GB/mês.

Para saber mais sobre funcionalidades e problemas de replicação do MySQL, consulte a [documentação de replicação mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.
>

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para a fonte.

Um cenário comum é fazer com que as cargas de trabalho bi e analíticas utilizem a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita na fonte. Esta funcionalidade não está direcionada para cargas de trabalho de escrita intensa.

A funcionalidade de réplica de leitura utiliza a replicação assíncronea mySQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre a fonte e a réplica. Os dados sobre a réplica eventualmente tornam-se consistentes com os dados da fonte. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões

Pode criar uma réplica de leitura numa região diferente do seu servidor de origem. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou a aproximação de dados aos seus utilizadores.

Pode ter um servidor de origem em qualquer [Base de Dados Azure para a região mySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Um servidor de origem pode ter uma réplica na sua região emparelhada ou nas regiões universais de réplicas. A imagem a seguir mostra quais as regiões réplicas disponíveis dependendo da sua região de origem.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões réplicas":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões réplicas universais

Pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente do local onde o seu servidor de origem está localizado. As regiões de réplica universal apoiadas incluem:

Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Eua Central, Leste Asiático, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul, Norte Central dos EUA, Norte da Europa Central, Eua Central Do Sul, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Eua Ocidental, Eua Ocidental 2, West Central EUA.

### <a name="paired-regions"></a>Regiões emparelhadas

Além das regiões de réplica universal, pode criar uma réplica de leitura na região emparelhada Azure do seu servidor de origem. Se não conhece o par da sua região, pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se estiver a usar réplicas de regiões cruzadas para o planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

No entanto, existem limitações a considerar: 

* Disponibilidade regional: A Azure Database for MySQL está disponível em France Central, UAE North e Germany Central. No entanto, as suas regiões emparelhadas não estão disponíveis.

* Pares unidirecionais: Algumas regiões azures são emparelhadas apenas numa direção. Estas regiões incluem a Índia Ocidental, o Sul do Brasil e o Gov Virginia dos EUA.
   Isto significa que um servidor de origem na Índia Ocidental pode criar uma réplica no Sul da Índia. No entanto, um servidor de origem no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é a Índia do Sul, mas a região secundária do Sul da Índia não é a Índia Ocidental.

## <a name="create-a-replica"></a>Criar uma réplica

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MySQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

Se um servidor de origem não tiver servidores de réplica existentes, a fonte reiniciará primeiro para se preparar para a replicação.

Quando inicia o fluxo de trabalho de réplicas, é criada uma Base de Dados Azure em branco para o servidor MySQL. O novo servidor está preenchido com os dados que estavam no servidor de origem. O tempo de criação depende da quantidade de dados sobre a fonte e do tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma subscrição que o servidor de origem. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação.

Todas as réplicas estão ativadas para o armazenamento [de crescimento automático.](concepts-pricing-tiers.md#storage-auto-grow) A funcionalidade de crescimento automático permite que a réplica acompanhe os dados replicados e evite uma interrupção na replicação causada por erros fora de armazenamento.

Saiba como [criar uma réplica de leitura no portal Azure.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar-se a uma réplica

Na criação, uma réplica herda as regras de firewall do servidor de origem. Posteriormente, estas regras são independentes do servidor de origem.

A réplica herda a conta de administração do servidor de origem. Todas as contas de utilizador no servidor de origem são replicadas nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor de origem.

Pode ligar-se à réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria numa base de dados Azure regular para o servidor MySQL. Para um servidor chamado **myreplica** com o nome de utilizador de administração **myadmin,** pode ligar-se à réplica utilizando o mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

A pedido, introduza a palavra-passe para a conta de utilizador.

## <a name="monitor-replication"></a>Monitorizar a replicação

A base de dados Azure para o MySQL fornece o **lag de replicação em segundos** métrica no Azure Monitor. Esta métrica está disponível apenas para réplicas. Esta métrica é calculada usando a `seconds_behind_master` métrica disponível no comando do MySQL. `SHOW SLAVE STATUS` Desaça um alerta para informá-lo quando o lag de replicação atingir um valor que não é aceitável para a sua carga de trabalho.

Se vir um atraso de replicação aumentado, consulte a [latência da replicação da resolução de problemas](howto-troubleshoot-replication-latency.md) para resolver problemas e compreender possíveis causas.

## <a name="stop-replication"></a>Parar replicação

Pode parar a replicação entre uma fonte e uma réplica. Após a replicação ser interrompida entre um servidor de origem e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados disponíveis na réplica no momento em que o comando de replicação de paragem foi iniciado. O servidor autónomo não alcança o servidor de origem.

Quando opta por parar a replicação de uma réplica, perde todas as ligações com a sua fonte anterior e outras réplicas. Não há falha automatizada entre uma fonte e a sua réplica.

> [!IMPORTANT]
> O servidor autónomo não pode ser transformado numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados necessários.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="failover"></a>Ativação pós-falha

Não há falha automatizada entre servidores de origem e réplicas.

Como a replicação é assíncronea, há um desfasamento entre a fonte e a réplica. A quantidade de lag pode ser influenciada por muitos fatores como o peso da carga de trabalho no servidor de origem e a latência entre centros de dados. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Pode rastrear o seu lag de replicação real usando o *Lag métrico de Réplica,* que está disponível para cada réplica. Esta métrica mostra o tempo desde a última transação reproduzida. Recomendamos que identifique qual é o seu atraso médio observando o seu atraso de réplica durante um período de tempo. Pode definir um alerta no lag de réplica, para que, se for fora do alcance esperado, possa tomar medidas.

> [!Tip]
> Se falhar na réplica, o lag no momento em que desvincular a réplica da fonte indicará a quantidade de dados perdidos.

Depois de decidir que quer falhar com uma réplica:

1. Pare a replicação na réplica<br/>
   Este passo é necessário para que o servidor de réplica seja capaz de aceitar escritas. Como parte deste processo, o servidor de réplica será desvinculado da fonte. Depois de iniciar a replicação stop, o processo de backend normalmente demora cerca de 2 minutos a ser concluído. Consulte a secção de replicação de [paragem](#stop-replication) deste artigo para entender as implicações desta ação.

2. Aponte a sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma cadeia de ligação única. Atualize a sua aplicação para indicar a (antiga) réplica em vez da fonte.

Depois de a sua aplicação processar com sucesso leituras e escritas, completou o failover. A quantidade de tempo de inatividade das suas experiências de aplicação dependerá de quando detetar um problema e completar os passos 1 e 2 listados anteriormente.

## <a name="global-transaction-identifier-gtid"></a>Identificador global de transações (GTID)

O identificador de transações globais (GTID) é um identificador único criado com cada transação comprometida num servidor de origem e está desligado por padrão na Base de Dados Azure para o MySQL. O GTID é suportado nas versões 5.7 e 8.0 e apenas em servidores que suportam o armazenamento até 16 TB. Para saber mais sobre o GTID e como é usado na replicação, consulte a replicação do MySQL com documentação [GTID.](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html)

O MySQL suporta dois tipos de transações: transações GTID (identificadas com GTID) e transações anónimas (não têm um GTID atribuído)

Os seguintes parâmetros do servidor estão disponíveis para configurar o GTID: 

|**Parâmetro do servidor**|**Descrição**|**Valor Predefinido**|**Valores**|
|--|--|--|--|
|`gtid_mode`|Indica se os GTIDs são usados para identificar transações. As alterações entre modos só podem ser feitas um passo de cada vez por ordem ascendente (ex. `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: As transações novas e de replicação devem ser anónimas <br> `OFF_PERMISSIVE`: As novas transações são anónimas. As transações replicadas podem ser transações anónimas ou GTID. <br> `ON_PERMISSIVE`: As novas transações são transações GTID. As transações replicadas podem ser transações anónimas ou GTID. <br> `ON`: Ambas as transações novas e replicadas devem ser transações GTID.|
|`enforce_gtid_consistency`|Reforça a consistência gtid permitindo a execução de apenas as declarações que podem ser registadas de forma transacionalmente segura. Este valor deve ser definido `ON` antes de permitir a replicação do GTID. |`OFF`|`OFF`: Todas as transações são permitidas para violar a consistência GTID.  <br> `ON`: Nenhuma transação é permitida para violar a consistência GTID. <br> `WARN`: Todas as transações são permitidas para violar a consistência GTID, mas um aviso é gerado. | 

> [!NOTE]
> Depois de o GTID estar ativado, não é possível desligá-lo. Se precisar de desligar o GTID, contacte o suporte. 

Para ativar o GTID e configurar o comportamento de consistência, atualize os `gtid_mode` parâmetros e `enforce_gtid_consistency` os parâmetros do servidor utilizando o [portal Azure](howto-server-parameters.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)ou [PowerShell](howto-configure-server-parameters-using-powershell.md).

Se o GTID estiver ativado num servidor de origem `gtid_mode` (= ON), as réplicas recém-criadas também terão GTID ativado e utilizam a replicação GTID. Para manter a replicação consistente, não é possível atualizar `gtid_mode` o servidor de origem ou réplica.

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Escalões de preço

Atualmente, as réplicas de leitura só estão disponíveis nos níveis de preços otimizados para fins gerais e memória.

> [!NOTE]
> O custo de execução do servidor de réplica baseia-se na região onde o servidor de réplica está em execução.

### <a name="source-server-restart"></a>Reinício do servidor de origem

Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como uma nova Base de Dados Azure para o servidor MySQL. Um servidor existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica lida.

### <a name="replica-configuration"></a>Configuração de réplica

Uma réplica é criada utilizando a mesma configuração do servidor que a fonte. Após a criação de uma réplica, várias configurações podem ser alteradas independentemente do servidor de origem: geração de cálculo, vCores, armazenamento e período de retenção de backup. O nível de preços também pode ser alterado de forma independente, exceto de ou para o nível básico.

> [!IMPORTANT]
> Antes de atualizar uma configuração do servidor de origem para os novos valores, atualize a configuração da réplica para valores iguais ou superiores Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no original.

As regras de firewall e as definições de parâmetros são herdadas do servidor de origem para a réplica quando a réplica é criada. Depois, as regras da réplica são independentes.

### <a name="stopped-replicas"></a>Réplicas paradas

Se parar a replicação entre um servidor de origem e uma réplica de leitura, a réplica parada torna-se um servidor autónomo que aceita tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

### <a name="deleted-source-and-standalone-servers"></a>Fonte eliminada e servidores autónomos

Quando um servidor de origem é eliminado, a replicação é interrompida para todas as réplicas lidas. Estas réplicas tornam-se automaticamente servidores autónomos e podem aceitar tanto as leituras como as escritas. O servidor de origem em si é eliminado.

### <a name="user-accounts"></a>Contas de utilizador

Os utilizadores do servidor de origem são replicados nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador disponíveis no servidor de origem.

### <a name="server-parameters"></a>Parâmetros do servidor

Para impedir que os dados fiquem dessincronizados e evitar potenciais perdas de dados ou corrupção, a atualização de alguns parâmetros de servidor é bloqueada ao utilizar réplicas de leitura.

Os seguintes parâmetros do servidor estão bloqueados tanto nos servidores de origem como em réplicas:

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parâmetro está bloqueado nos servidores de réplicas.

Para atualizar um dos parâmetros acima no servidor de origem, elimine os servidores de réplicas, atualize o valor do parâmetro na fonte e recrie réplicas.

### <a name="gtid"></a>GTID

O GTID é suportado em:

* Versões MySQL 5.7 e 8.0.
* Servidores que suportam o armazenamento até 16 TB. Consulte o artigo [do nível de preços](concepts-pricing-tiers.md#storage) para a lista completa das regiões que suportam 16 armazenamentos de TB.

GTID está desligado por defeito. Depois de o GTID estar ativado, não podes desligá-lo. Se precisar de desligar o GTID, contacte o suporte.

Se o GTID estiver ativado num servidor de origem, as réplicas recém-criadas também terão GTID ativada e utilizam a replicação GTID. Para manter a replicação consistente, não é possível atualizar `gtid_mode` o servidor de origem ou réplica.

### <a name="other"></a>Outro

* Criar uma réplica de uma réplica não é suportado.
* As tabelas de memória podem fazer com que as réplicas fiquem dessincronizadas. Esta é uma limitação da tecnologia de replicação MySQL. Leia mais na documentação de referência do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para obter mais informações.
* Certifique-se de que as tabelas do servidor de origem têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre a fonte e as réplicas.
* Reveja a lista completa das limitações de replicação do [MySQL na documentação MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar e gerir réplicas de leitura usando o portal Azure](howto-read-replicas-portal.md)
* Saiba como [criar e gerir réplicas de leitura usando o ALI Azure CLI e REST API](howto-read-replicas-cli.md)