---
title: Atualização principal da versão na Base de Dados Azure para MySQL - Servidor Único
description: Este artigo descreve como pode atualizar a versão principal para Azure Database para MySQL - Single Server
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 471ccd6176bd8821ce7e40fde6d961bd9bcf7f0c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702152"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Atualização principal da versão na Base de Dados Azure para MySQL Single Server

> [!NOTE]
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, removê-lo-emos deste artigo.
>

> [!IMPORTANT]
> A atualização da versão principal para a base de dados Azure para o MySQL Single Server está em pré-visualização pública.

Este artigo descreve como pode atualizar a sua versão principal do MySQL no local na Base de Dados Azure para o servidor único MySQL.

Esta funcionalidade permitirá que os clientes realizem atualizações in-place dos seus servidores MySQL 5.6 para o MySQL 5.7 com um clique de botão sem qualquer movimento de dados ou a necessidade de alterações nas cordas de ligação da aplicação.

> [!Note]
> * A atualização da versão principal só está disponível para a atualização da versão principal do MySQL 5.6 para MySQL 5.7.
> * O servidor estará indisponível durante toda a operação de atualização. Por isso, é aconselhável efetuar atualizações durante a janela de manutenção planeada. Pode considerar [a realização de uma atualização de versão principal de tempo de inatividade mínima do MySQL 5.6 para MySQL 5.7 utilizando réplica de leitura.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Execute uma grande atualização de versão do MySQL 5.6 para MySQL 5.7 utilizando o portal Azure

Siga estes passos para realizar a atualização de versão importante para o seu servidor Azure Database do mySQL 5.6 utilizando o portal Azure

> [!IMPORTANT]
> Recomendamos realizar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Veja [como realizar a restauração pontual](howto-restore-server-portal.md#point-in-time-restore).

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL 5.6.

2. Na página **'Vista Geral',** clique no botão **'Upgrade'** na barra de ferramentas.

3. Na secção **De Atualização,** selecione **OK** para atualizar a base de dados Azure para o servidor MySQL 5.6 para o servidor 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - visão geral - upgrade":::

4. Uma notificação confirmará que a atualização é bem sucedida.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Execute uma grande atualização da versão do MySQL 5.6 para o MySQL 5.7 utilizando o Azure CLI

Siga estes passos para realizar uma grande atualização de versão para o seu servidor Azure Database do mySQL 5.6 utilizando O Azure CLI

> [!IMPORTANT]
> Recomendamos realizar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Veja [como realizar a restauração pontual](howto-restore-server-cli.md#server-point-in-time-restore).

1. Instale [o Azure CLI para windows](/cli/azure/install-azure-cli) ou utilize o Azure CLI em [Azure Cloud Shell](../cloud-shell/overview.md) para executar os comandos de atualização. 
 
   Esta atualização requer a versão 2.16.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. Execute o comando az version para localizar a versão e as bibliotecas dependentes instaladas. Para atualizar para a versão mais recente, execute o comando az upgrade.

2. Depois de iniciar sôm, execute o comando de atualização do [servidor az mysql:](/cli/azure/mysql/server?preserve-view=true&view=azure-cli-latest#az_mysql_server_upgrade)

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   O pedido de comando mostra a mensagem "Running". Depois de esta mensagem já não ser apresentada, a atualização da versão está completa.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Realize uma grande atualização de versão do MySQL 5.6 para MySQL 5.7 na réplica de leitura usando o portal Azure

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor de réplica de leitura MySQL 5.6.

2. Na página **'Vista Geral',** clique no botão **'Upgrade'** na barra de ferramentas.

3. Na secção **De Atualização,** selecione **OK** para atualizar a base de dados Azure para o MySQL 5.6 ler o servidor de réplica para o servidor 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - visão geral - upgrade":::

4. Uma notificação confirmará que a atualização é bem sucedida.

5. Na página **'Vista Geral',** confirme que a sua base de dados Azure para o MySQL leu a versão do servidor de réplicas de 5.7.

6. Agora vá para o seu servidor primário e [execute a atualização da versão principal](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) nele.

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Execute uma atualização mínima de versão principal de downtime do MySQL 5.6 para MySQL 5.7 utilizando réplicas de leitura

Pode realizar uma atualização mínima de versão principal de downtime do MySQL 5.6 para MySQL 5.7 utilizando réplicas de leitura. A ideia é atualizar a réplica de leitura do seu servidor para 5.7 primeiro e mais tarde falhar a sua aplicação para apontar para ler réplica e torná-la uma nova primária.

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o MySQL 5.6.

2. Crie uma réplica de [leitura](./concepts-read-replicas.md#create-a-replica) a partir do seu servidor primário.

3. [Atualize a sua réplica de leitura](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) para a versão 5.7.

4. Assim que confirmar que o servidor de réplica está a funcionar na versão 5.7, impeça a sua aplicação de se ligar ao seu servidor primário.
 
5. Verifique o estado de replicação e certifique-se de que a réplica está toda apanhada com o primário para que todos os dados estejam sincronizados e certifique-se de que não há novas operações realizadas nas primárias.

   Ligue para o [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando no servidor de réplica para ver o estado de replicação.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Se o estado `Slave_IO_Running` de e `Slave_SQL_Running` são "sim" e o valor de `Seconds_Behind_Master` "0", a replicação está a funcionar bem. `Seconds_Behind_Master` indica quão tarde é a réplica. Se o valor não for "0", significa que a réplica está a processar atualizações. Uma vez confirmado que `Seconds_Behind_Master` é "0", é seguro parar a replicação.

6. Promova a sua réplica de leitura para o [primário, impedindo a replicação.](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server)

7. Aponte a sua aplicação para a nova réplica primária (antiga réplica) que está a executar o servidor 5.7. Cada servidor tem uma cadeia de ligação única. Atualize a sua aplicação para indicar a (antiga) réplica em vez da fonte.

> [!Note]
> Este cenário terá tempo de inatividade apenas nos passos 4, 5 e 6.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Quando é que esta funcionalidade de upgrade será GA, uma vez que temos o MySQL v5.6 no nosso ambiente de produção que precisamos de atualizar?

O GA desta funcionalidade está planeado antes da reforma do MySQL v5.6. No entanto, a funcionalidade está pronta e totalmente suportada pela Azure, pelo que deverá executá-la com confiança no seu ambiente. Como uma melhor prática recomendada, sugerimos fortemente que o execute e teste primeiro numa cópia restaurada do servidor para que possa estimar o tempo de inatividade durante a atualização e realizar um teste de compatibilidade da aplicação antes de o executar na produção. Para obter mais informações, consulte [como executar o restauro pontual](howto-restore-server-portal.md#point-in-time-restore) para criar uma cópia pontual do seu servidor. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Isto causará tempo de inatividade do servidor e, em caso afirmativo, quanto tempo?

Sim, o servidor não estará disponível durante o processo de atualização, pelo que recomendamos que efetue esta operação durante a janela de manutenção planeada. O tempo de inatividade estimado depende do tamanho da base de dados, do tamanho do armazenamento previsto (IOPs previsto) e do número de tabelas na base de dados. O tempo de atualização é diretamente proporcional ao número de tabelas no servidor. Espera-se que as atualizações dos servidores Basic SKU levem mais tempo, uma vez que se encontra na plataforma de armazenamento padrão. Para estimar o tempo de inatividade para o ambiente do seu servidor, recomendamos que realize primeiro o upgrade na cópia restaurada do servidor. Considere [realizar uma atualização mínima de versão principal de downtime do MySQL 5.6 para MySQL 5.7 utilizando réplica de leitura.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>O que acontecerá se não optarmos por atualizar o nosso servidor MySQL v5.6 antes de 5 de fevereiro de 2021?

Pode continuar a executar o seu servidor MySQL v5.6 como antes. O Azure **nunca realizará** uma atualização de força no seu servidor. No entanto, aplicar-se-ão as restrições documentadas na [Base de Dados Azure para a política de versão MySQL.](concepts-version-policy.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a política de versão Azure Database para a política de versão MySQL](concepts-version-policy.md).