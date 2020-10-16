---
title: Ler réplicas - Azure Database for MySQL
description: 'Saiba mais sobre réplicas lidas na Base de Dados Azure para o MySQL: escolher regiões, criar réplicas, ligar-se a réplicas, monitorizar a replicação e parar a replicação.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: de1e0e077eacfe4779834c46da7de4d8c4a2c75f
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126669"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas de leitura na Base de Dados do Azure para MySQL

A funcionalidade de réplica de leitura permite replicar dados de um servidor de Base de Dados do Azure para MySQL para um servidor só de leitura. Pode replicar do servidor de origem para até cinco réplicas. As réplicas são atualizadas de forma assíncrona com a tecnologia de replicação baseada na posição dos ficheiros de registo binário nativo (binlog) do motor MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que geres similares ao Azure Database regular para servidores MySQL. Para cada réplica de leitura, você é cobrado para o cálculo provisionado em vCores e armazenamento em GB/mês.

Para saber mais sobre funcionalidades e problemas de replicação do MySQL, consulte a [documentação de replicação mySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas e as cargas de trabalho de escrita podem ser encaminhadas para o servidor mestre.

Um cenário comum é fazer com que as cargas de trabalho bi e analíticas utilizem a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita para o mestre. Esta funcionalidade não está direcionada para cargas de trabalho de escrita intensa.

A funcionalidade de réplica de leitura utiliza a replicação assíncronea mySQL. A funcionalidade não se destina a cenários de replicação sincronizados. Haverá um atraso mensurável entre a fonte e a réplica. Os dados sobre a réplica eventualmente tornam-se consistentes com os dados do mestre. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso.

> [!IMPORTANT]
> A Base de Dados do Azure para MySQL utiliza o registo binário baseado em **LINHA**. Se a sua tabela não tiver uma chave primária, todas as linhas da tabela são analisadas à procura de operações DML. Tal causa um aumento no atraso da replicação. Para garantir que a réplica seja capaz de acompanhar as alterações na origem, geralmente é recomendável adicionar uma chave primária em tabelas no servidor de origem antes de criar ou recriar o servidor de réplica, se já tiver um.

## <a name="cross-region-replication"></a>Replicação entre regiões
Pode criar uma réplica de leitura numa região diferente do seu servidor de origem. A replicação transversal pode ser útil para cenários como o planeamento de recuperação de desastres ou a aproximação de dados aos seus utilizadores.

Pode ter um servidor de origem em qualquer [Base de Dados Azure para a região mySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Um servidor de origem pode ter uma réplica na sua região emparelhada ou nas regiões universais de réplicas. A imagem abaixo mostra quais as regiões réplicas disponíveis dependendo da sua região de origem.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões réplicas":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões réplicas universais
Pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente do local onde o seu servidor de origem está localizado. As regiões de réplica universal apoiadas incluem:

Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Eua Central, Leste Asiático, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul, Norte Central dos EUA, Norte da Europa Central, Eua Central Do Sul, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Eua Ocidental, Eua Ocidental 2, West Central EUA.

### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, pode criar uma réplica de leitura na região emparelhada Azure do seu servidor de origem. Se não conhece o par da sua região, pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se estiver a utilizar réplicas transversais para o planeamento de recuperação de desastres, recomendamos que crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

No entanto, existem limitações a considerar: 

* Disponibilidade regional: A Azure Database for MySQL está disponível em France Central, UAE North e Germany Central. No entanto, as suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azures são emparelhadas apenas numa direção. Estas regiões incluem a Índia Ocidental, o Sul do Brasil e o Gov Virginia dos EUA. 
   Isto significa que um servidor de origem na Índia Ocidental pode criar uma réplica no Sul da Índia. No entanto, um servidor de origem no Sul da Índia não pode criar uma réplica na Índia Ocidental. Isto porque a região secundária da Índia Ocidental é a Índia do Sul, mas a região secundária do Sul da Índia não é a Índia Ocidental.

## <a name="create-a-replica"></a>Criar uma réplica

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MySQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

Se um servidor de origem não tiver servidores de réplica existentes, a fonte reiniciará primeiro para se preparar para a replicação.

Quando inicia o fluxo de trabalho de réplicas, é criada uma Base de Dados Azure em branco para o servidor MySQL. O novo servidor está preenchido com os dados que estavam no servidor de origem. O tempo de criação depende da quantidade de dados sobre a fonte e do tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma subscrição que o servidor de origem. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) após a criação.

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

Se vir um atraso de replicação aumentado, consulte a [latência da resolução de resolução de problemas](howto-troubleshoot-replication-latency.md) para resolver problemas e compreenda possíveis causas.

## <a name="stop-replication"></a>Parar replicação

Pode parar a replicação entre uma fonte e uma réplica. Após a replicação ser interrompida entre um servidor de origem e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados disponíveis na réplica no momento em que o comando de replicação de paragem foi iniciado. O servidor autónomo não alcança o servidor de origem.

Quando opta por parar a replicação de uma réplica, perde todas as ligações com a sua fonte anterior e outras réplicas. Não há falha automatizada entre uma fonte e a sua réplica.

> [!IMPORTANT]
> O servidor autónomo não pode ser transformado numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados necessários.

Aprenda a parar a [replicação a uma réplica.](howto-read-replicas-portal.md)

## <a name="failover"></a>Ativação pós-falha

Não existe falha automatizada entre servidores de origem e réplicas. 

Como a replicação é assíncronea, há um desfasamento entre a fonte e a réplica. A quantidade de lag pode ser influenciada por uma série de fatores como o peso da carga de trabalho que funciona no servidor de origem e a latência entre centros de dados. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Pode rastrear o seu lag de replicação real usando o *Lag métrico de Réplica,* que está disponível para cada réplica. Esta métrica mostra o tempo desde a última transação reproduzida. Recomendamos que identifique qual é o seu atraso médio observando o seu atraso de réplica durante um período de tempo. Pode definir um alerta no lag de réplica, para que, se for fora do alcance esperado, possa tomar medidas.

> [!Tip]
> Se falhar na réplica, o lag no momento em que desvincular a réplica da fonte indicará a quantidade de dados perdidos.

Uma vez que tenha decidido que quer falhar para uma réplica, 

1. Pare a replicação na réplica<br/>
   Este passo é necessário para que o servidor de réplica seja capaz de aceitar escritas. Como parte deste processo, o servidor de réplica será desvinculado do mestre. Uma vez iniciado a replicação stop, o processo de backend normalmente demora cerca de 2 minutos a ser concluído. Consulte a secção de replicação de [paragem](#stop-replication) deste artigo para entender as implicações desta ação.
    
2. Aponte a sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma cadeia de ligação única. Atualize a sua aplicação para apontar para a (antiga) réplica em vez do mestre.
    
Uma vez que a sua aplicação esteja a processar com sucesso as leituras e as escritas, completou o failover. A quantidade de tempo de inatividade das suas experiências de aplicação dependerá de quando detetar um problema e completar os passos 1 e 2 acima.

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

Uma réplica é criada utilizando a mesma configuração do servidor que o mestre. Após a criação de uma réplica, várias configurações podem ser alteradas independentemente do servidor de origem: geração de cálculo, vCores, armazenamento e período de retenção de backup. O nível de preços também pode ser alterado de forma independente, exceto de ou para o nível básico.

> [!IMPORTANT]
> Antes de atualizar uma configuração do servidor de origem para os novos valores, atualize a configuração da réplica para valores iguais ou superiores Esta ação garante que a réplica pode acompanhar quaisquer alterações feitas no mestre.

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
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parâmetro está bloqueado nos servidores de réplicas. 

Para atualizar um dos parâmetros acima no servidor de origem, por favor, elimine os servidores de réplicas, atualize o valor do parâmetro no master e recrie réplicas.

### <a name="other"></a>Outro

- Os identificadores globais de transações (GTID) não são suportados.
- A criação de uma réplica de uma réplica não é suportada.
- As tabelas de memória podem fazer com que as réplicas fiquem dessincronizadas. Esta é uma limitação da tecnologia de replicação MySQL. Leia mais na documentação de referência do [MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para obter mais informações.
- Certifique-se de que as tabelas do servidor de origem têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre a fonte e as réplicas.
- Reveja a lista completa das limitações de replicação do [MySQL na documentação MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar e gerir réplicas de leitura usando o portal Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerir réplicas de leitura usando o ALI Azure CLI e REST API](howto-read-replicas-cli.md)
