---
title: Ler réplicas no banco de dados do Azure para MariaDB
description: Este artigo descreve as réplicas de leitura para o banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 8cfda202e57dcee4f7a783de893fb712501dfd26
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992189"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Ler réplicas no banco de dados do Azure para MariaDB

O recurso de réplica de leitura permite replicar dados de um servidor do Azure para MariaDB para um servidor somente leitura. Pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição de arquivo de log (binlog) do mecanismo MariaDB com a ID de transação global (GTID). Para saber mais sobre a replicação do binlog, consulte a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Você pode criar uma réplica de leitura na mesma região que o servidor mestre ou em qualquer outra região do Azure de sua escolha. As réplicas de leitura (mesma região e região cruzada) estão atualmente em visualização pública.

Réplicas são novos servidores que você gerencia de forma semelhante ao banco de dados do Azure regular para servidores MariaDB. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e armazenamento em GB/mês.

Para saber mais sobre a replicação do GTID, consulte a [documentação de replicação do MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso ler réplica ajuda a melhorar o desempenho e a escala de cargas de trabalho com uso intensivo de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é fazer com que as cargas de trabalho de BI e analíticas usem a réplica de leitura como a fonte de dados para relatórios.

Como as réplicas são somente leitura, elas não reduzem diretamente as sobrecargas de capacidade de gravação no mestre. Esse recurso não é destinado a cargas de trabalho com uso intensivo de gravação.

O recurso ler réplica usa replicação assíncrona. O recurso não é destinado a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica eventualmente se tornam consistentes com os dados no mestre. Use esse recurso para cargas de trabalho que podem acomodar esse atraso.


## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente do servidor mestre. A replicação entre regiões pode ser útil para cenários como planejamento de recuperação de desastres ou trazer dados mais próximos aos seus usuários.

> [!IMPORTANT]
> A replicação entre regiões está atualmente em visualização pública.

Você pode ter um servidor mestre em qualquer [banco de dados do Azure para a região MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal.

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você sempre pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor mestre está localizado. Estas são as regiões de réplica universal:

Leste da Austrália, sudeste da Austrália, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre. Se você não souber o par de sua região, poderá aprender mais no [artigo regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

No entanto, há limitações a serem consideradas: 

* Disponibilidade regional: O banco de dados do Azure para MariaDB está disponível no oeste dos EUA 2, França central, Norte dos EAU e Alemanha central. No entanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões do Azure são emparelhadas somente em uma direção. Essas regiões incluem Índia ocidental, sul do Brasil e US Gov-Virgínia. 
   Isso significa que um servidor mestre na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor mestre na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária da Índia ocidental é sul da Índia, mas a região secundária do Sul da Índia não é oeste da Índia.


## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor mestre não tiver servidores de réplica existentes, o mestre será reiniciado primeiro para se preparar para a replicação.

Quando você inicia o fluxo de trabalho criar réplica, é criado um banco de dados do Azure em branco para o servidor MariaDB. O novo servidor é preenchido com os dados que estavam no servidor mestre. O tempo de criação depende da quantidade de dados no mestre e do tempo desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> Se você não tiver um alerta de armazenamento configurado em seus servidores, recomendamos que você o faça. O alerta informa quando um servidor está se aproximando de seu limite de armazenamento, o que afetará a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Quando você cria uma réplica, ela não herda as regras de firewall ou o ponto de extremidade de serviço VNet do servidor mestre. Essas regras devem ser configuradas independentemente para a réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário que estão disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como faria em um servidor de banco de dados do Azure normal para MariaDB. Para um servidor chamado myreplication com o nome de usuário admin myadmin, você pode se conectar à réplica usando a CLI do MySQL:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

No prompt, digite a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O banco de dados do Azure para MariaDB fornece a métrica **atraso de replicação em segundos** no Azure monitor. Essa métrica está disponível somente para réplicas.

Essa métrica é calculada usando `seconds_behind_master` a métrica disponível no comando `SHOW SLAVE STATUS` do MariaDB.

Defina um alerta para informá-lo quando o retardo de replicação atingir um valor que não é aceitável para sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre um mestre e uma réplica. Depois que a replicação é interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando parar replicação foi iniciado. O servidor autônomo não é atualizado com o servidor mestre.

Quando você opta por interromper a replicação em uma réplica, ela perde todos os links para o mestre anterior e outras réplicas. Não há nenhum failover automatizado entre um mestre e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode ser tornado novamente em uma réplica.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados necessários.

Saiba como [interromper a replicação em uma réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Escalões de preço

Atualmente, as réplicas de leitura só estão disponíveis nos tipos de preço Uso Geral e com otimização de memória.

### <a name="master-server-restart"></a>Reinicialização do servidor mestre

Quando você cria uma réplica para um mestre que não tem réplicas existentes, o mestre primeiro será reiniciado para se preparar para a replicação. Leve isso em consideração e execute essas operações durante um período de fora de pico.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo banco de dados do Azure para o servidor MariaDB. Não é possível estabelecer um servidor existente em uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração de réplica

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento, período de retenção de backup e versão do mecanismo MariaDB. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para novos valores, atualize a configuração de réplica para valores iguais ou maiores. Essa ação garante que a réplica possa acompanhar as alterações feitas no mestre.

### <a name="stopped-replicas"></a>Réplicas interrompidas

Se você parar a replicação entre um servidor mestre e uma réplica de leitura, a réplica parada se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos

Quando um servidor mestre é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas se tornam servidores autônomos. O próprio servidor mestre é excluído.

### <a name="user-accounts"></a>Contas de utilizador

Os usuários no servidor mestre são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

### <a name="server-parameters"></a>Parâmetros do servidor

Para impedir que os dados fiquem fora de sincronia e evitar possíveis perdas de dados ou danos, alguns parâmetros de servidor estão bloqueados para serem atualizados ao usar réplicas de leitura.

Os seguintes parâmetros de servidor estão bloqueados nos servidores mestre e de réplica:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

O [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) parâmetro está bloqueado nos servidores de réplica.

### <a name="other"></a>Outros

- Não há suporte para a criação de uma réplica de uma réplica.
- Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Essa é uma limitação da tecnologia de replicação MariaDB.
- Verifique se as tabelas do servidor mestre têm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerenciar réplicas de leitura usando o CLI do Azure](howto-read-replicas-cli.md)