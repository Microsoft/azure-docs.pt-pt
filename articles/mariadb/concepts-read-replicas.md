---
title: Réplicas de leitura na base de dados do Azure para MariaDB
description: Este artigo descreve a leitura de réplicas da base de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 8abe257090b5159053a37350c9e24cc27073679b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079369"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas de leitura na base de dados do Azure para MariaDB

A funcionalidade de réplica de leitura permite-lhe replicar dados de uma base de dados do Azure para MariaDB server para um servidor de só de leitura. Pode replicar do servidor mestre para até cinco réplicas na mesma região que o mestre. As réplicas são atualizadas de forma assíncrona utilizando a tecnologia de replicação baseada em posição de arquivo de log binário (binlog) o motor de MariaDB com o ID (GTID) de transação global. Para saber mais sobre a replicação de binlog, consulte a [descrição geral da replicação de binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Mesma região de leitura réplicas está atualmente em pré-visualização pública.

Réplicas são novos servidores que gerencie semelhante regular a base de dados do Azure para MariaDB servidores. Para cada réplica, leia, é-lhe cobrada a computação aprovisionada em vCores e o armazenamento em GB / mês.

Para saber mais sobre a replicação de GTID, consulte a [documentação de replicação de MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Quando utilizar uma réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e dimensionamento de cargas de trabalho de leitura intensiva. Cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para o mestre.

Um cenário comum é fazer com que o BI e cargas de trabalho analíticas utilizam a réplica de leitura como a origem de dados de relatórios.

Uma vez que as réplicas são só de leitura, eles não reduzem diretamente nem encargos de escrita-capacidade no mestre de. Esta funcionalidade não está direcionada para cargas de trabalho intensivas de escrita.

A funcionalidade de réplica de leitura utiliza os replicação assíncrona. A funcionalidade não foi criada para cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica torna-se, eventualmente, consistentes com os dados no mestre de. Utilize esta funcionalidade para cargas de trabalho que podem acomodar este atraso.

Réplicas de leitura podem melhorar o seu plano de recuperação após desastre. Se houver um desastre regional e o servidor principal estiver indisponível, pode direcionar sua carga de trabalho para uma réplica noutra região. Para tal, primeiro permitem que a réplica aceitar escritas utilizando a função de replicação de paragem. Em seguida, pode redirecionar o seu aplicativo ao atualizar a cadeia de ligação. Saiba mais no [parar a replicação](#stop-replication) secção.

## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor principal tiver não existem servidores de réplica existente, o mestre pela primeira vez será reiniciado para se preparar para a replicação.

Quando inicia o fluxo de trabalho de réplica de criar, é criada uma base de dados do Azure em branco para o servidor de MariaDB. O novo servidor é preenchido com os dados que estava no servidor principal. Hora de criação depende da quantidade de dados sobre o modelo global e o tempo desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> Se não tiver um conjunto de alerta de armazenamento a cópia de segurança nos seus servidores, recomendamos que o faça. O alerta informa-o quando um servidor está a atingir o seu limite de armazenamento, o que irá afetar a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ligar a uma réplica

Quando cria uma réplica, ele não herda as regras de firewall ou um ponto final de serviço de VNet do servidor mestre. Estas regras tem de ser definidas independentemente para a réplica.

A réplica herda a conta de administrador do servidor mestre. Todas as contas de utilizador no servidor principal são replicadas para as réplicas de leitura. Só se pode ligar para uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor principal.

Pode ligar para a réplica com o seu nome de anfitrião e uma conta de utilizador válido, tal como faria numa base de dados regulares do Azure para MariaDB server. Para um servidor com o nome **myreplica** com o nome de utilizador de administrador **myadmin**, pode ligar-se para a réplica com o mysql CLI:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Na linha de comandos, introduza a palavra-passe da conta de utilizador.

## <a name="monitor-replication"></a>Monitor de replicação

Base de dados do Azure para MariaDB fornece a **desfasamento em segundos** métricas no Azure Monitor. Esta métrica está disponível para apenas as réplicas.

Esta métrica é calculada usando o `seconds_behind_master` métricas disponíveis da MariaDB `SHOW SLAVE STATUS` comando.

Defina um alerta para o informar de quando o desfasamento de atinge um valor que não é aceitável para a sua carga de trabalho.

## <a name="stop-replication"></a>Parar a replicação

Pode parar a replicação entre um mestre e uma réplica. Depois de parar a replicação entre um servidor principal e uma réplica de leitura, a réplica torna-se um servidor autónomo. Os dados no servidor autónomo são os dados que estava disponíveis na réplica no momento, que o comando de replicação de paragem foi iniciado. O servidor autónomo não fique atualizado com o servidor mestre.

Quando optar por parar a replicação para uma réplica, ele perde todas as ligações para o mestre de anterior e outras réplicas. Não é sem failover automático entre um mestre e sua réplica.

> [!IMPORTANT]
> O servidor autónomo não pode se transformar numa réplica novamente.
> Antes de parar a replicação numa réplica de leitura, certifique-se de que a réplica tem todos os dados que necessita.

Saiba como [parar a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Escalões de preço

Réplicas de leitura só estão atualmente disponíveis nos escalões de preços para fins gerais e memória otimizada.

### <a name="master-server-restart"></a>Reiniciar o servidor mestre

Quando cria uma réplica para um modelo que tenha não réplicas existentes, o mestre pela primeira vez será reiniciado para se preparar para a replicação. . Levar isso em consideração e executar estas operações durante um período de ponta.

### <a name="new-replicas"></a>Novo réplicas

É criada uma réplica de leitura como uma nova base de dados do Azure para MariaDB server. Um servidor existente não pode se transformar numa réplica. Não é possível criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Ao utilizar a mesma configuração de servidor como o mestre, é criada uma réplica. Depois de criar uma réplica, várias configurações podem ser alteradas independentemente do servidor mestre: geração, vCores, armazenamento, período de retenção de cópia de segurança e MariaDB a versão do motor de computação. O escalão de preço também pode ser alterado de forma independente, exceto de ou para o escalão básico.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre é atualizada para novos valores, atualize a configuração de réplica para valores iguais ou superior. Esta ação garante que a réplica pode acompanhar todas as alterações efetuadas a mestre.

### <a name="stopped-replicas"></a>Réplicas paradas

Se parar a replicação entre um servidor principal e uma réplica de leitura, a réplica parada torna-se um servidor autónomo que aceita as leituras e gravações. O servidor autónomo não pode se transformar numa réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Eliminado servidores mestre e autónomo

Quando um servidor mestre for eliminado, a replicação é parada para todas as réplicas de leitura. Essas réplicas tornam-se a servidores autónomos. O servidor mestre em si é eliminado.

### <a name="user-accounts"></a>Contas de utilizador

Os utilizadores no servidor principal são replicados para as réplicas de leitura. Só pode ligar a uma réplica de leitura, com as contas de utilizador disponíveis no servidor principal.

### <a name="server-parameters"></a>Parâmetros do servidor

Para impedir que os dados se torne fora de sincronização e para evitar a potencial perda de dados ou danos, alguns parâmetros de servidor estão bloqueados de serem actualizados quando utilizar réplicas de leitura.

Os seguintes parâmetros de servidor estão bloqueados em servidores mestre e de réplica:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

O [ `event_scheduler` ](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) parâmetro está bloqueado nos servidores de réplica.

### <a name="other"></a>Outros

- Não é suportada a criação de uma réplica de uma réplica.
- Tabelas em memória podem tornar as réplicas fora de sincronia. Esta é uma limitação da tecnologia de replicação MariaDB.
- Certifique-se de que o servidor mestre tabelas tiverem chaves primárias. Falta de chaves primárias pode resultar numa latência de replicação entre o mestre e as réplicas.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar e gerir réplicas de leitura com o portal do Azure](howto-read-replicas-portal.md)
