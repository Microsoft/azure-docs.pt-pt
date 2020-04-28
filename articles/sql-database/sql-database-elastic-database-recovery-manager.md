---
title: Gestor de Recuperação para corrigir problemas no mapa do fragmento
description: Use a classe RecoveryManager para resolver problemas com mapas de fragmentos
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73823869"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais

A classe [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) fornece ADO.NET aplicações a capacidade de detetar e corrigir facilmente quaisquer inconsistências entre o mapa global do fragmento (GSM) e o mapa local de fragmentos (LSM) num ambiente de base de dados fragmento.

O GSM e o LSM rastreiam o mapeamento de cada base de dados num ambiente fragmento. Ocasionalmente, ocorre uma rutura entre o GSM e o LSM. Nesse caso, utilize a classe RecoveryManager para detetar e reparar a rutura.

A classe RecoveryManager faz parte da biblioteca de clientes da Base de [Dados Elástica.](sql-database-elastic-database-client-library.md)

![Mapa do fragmento][1]

Para definições de termo, consulte o glossário das [ferramentas elásticas database](sql-database-elastic-scale-glossary.md). Para entender como o **ShardMapManager** é usado para gerir dados numa solução esforçada, consulte a gestão do [mapa de Shard](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Por que usar o gestor de recuperação

Num ambiente de base de dados fragmento, há um inquilino por base de dados, e muitas bases de dados por servidor. Também pode haver muitos servidores no ambiente. Cada base de dados está mapeada no mapa do fragmento, para que as chamadas possam ser encaminhadas para o servidor e base de dados corretos. As bases de dados são rastreadas de acordo com uma **chave sharding**, e cada fragmento é atribuído a uma **gama de valores-chave**. Por exemplo, uma chave de sharding pode representar os nomes dos clientes de "D" a "F". O mapeamento de todos os fragmentos (bases de dados aka) e as suas gamas de mapeamento estão contidos no mapa global do **fragmento (GSM)**. Cada base de dados também contém um mapa das gamas contidas no fragmento que é conhecido como o mapa local do **fragmento (LSM)**. Quando uma aplicação se conecta a um fragmento, o mapeamento é emcached com a app para uma rápida recuperação. O LSM é utilizado para validar dados em cache.

O GSM e o LSM podem ficar dessincronizados pelas seguintes razões:

1. A eliminação de um fragmento cujo alcance se acredita já não estar em uso, ou renomear um fragmento. Apagar um fragmento resulta num **mapeamento órfão.** Da mesma forma, uma base de dados renomeada pode causar um mapeamento órfão. Dependendo da intenção da mudança, o fragmento pode ter de ser removido ou a localização do fragmento tem de ser atualizada. Para recuperar uma base de dados eliminada, consulte [Restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md).
2. Ocorre um evento de geo-failover. Para continuar, é necessário atualizar o nome do servidor e o nome da base de dados do gestor de mapas de fragmentos na aplicação e, em seguida, atualizar os detalhes de mapeamento de fragmentos para todos os fragmentos num mapa de fragmentos. Se houver uma geo-failover, essa lógica de recuperação deve ser automatizada dentro do fluxo de trabalho de failover. Automatizar ações de recuperação permite uma gestão sem atritos para bases de dados geoactivas e evita ações humanas manuais. Para saber mais sobre as opções para recuperar uma base de dados se houver uma falha no data center, consulte a [Continuidade do Negócio](sql-database-business-continuity.md) e a [Recuperação de Desastres.](sql-database-disaster-recovery.md)
3. Ou um fragmento ou a base de dados ShardMapManager é restaurado para um tempo anterior. Para saber sobre a recuperação do tempo ponto no tempo utilizando backups, consulte [Recovery utilizando backups](sql-database-recovery-using-backups.md).

Para obter mais informações sobre as ferramentas de base de dados elásticas azure SQL Database, geo-replicação e Restauro, consulte o seguinte:

* [Visão geral: Continuidade de negócios em nuvem e recuperação de desastres de base de dados com base de dados SQL](sql-database-business-continuity.md)
* [Começar com ferramentas de base de dados elásticas](sql-database-elastic-scale-get-started.md)  
* [Gestão shardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recuperação do RecoveryManager de um ShardMapManager

O primeiro passo é criar uma instância RecoveryManager. O [método GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) devolve o gestor de recuperação para a atual instância [Do ShardMapManager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Para resolver eventuais inconsistências no mapa do fragmento, primeiro deve recuperar o RecoveryManager para o mapa de fragmentos específico.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Neste exemplo, o RecoveryManager é inicializado a partir do ShardMapManager. O ShardMapManager contendo um ShardMap também já está inicializado.

Uma vez que este código de aplicação manipula o próprio mapa do fragmento, as credenciais utilizadas no método de fábrica (no exemplo anterior, smmConnectionString) devem ser credenciais que tenham permissões de leitura na base de dados gSM referenciada pela cadeia de ligação. Estas credenciais são tipicamente diferentes das credenciais usadas para abrir ligações para o encaminhamento dependente de dados. Para mais informações, consulte [A utilização de credenciais no cliente da base](sql-database-elastic-scale-manage-credentials.md)de dados elástica .

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Remover um fragmento do ShardMap depois de um fragmento ser eliminado

O [método DetachShard desprende](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) o fragmento dado do mapa do fragmento e elimina mapeamentos associados ao fragmento.  

* O parâmetro de localização é a localização do fragmento, especificamente o nome do servidor e o nome da base de dados, do fragmento que está a ser desconectado.
* O parâmetro shardMapName é o nome do mapa do fragmento. Isto só é necessário quando vários mapas de fragmentos são geridos pelo mesmo gestor de mapas de fragmentos. Opcional.

> [!IMPORTANT]
> Utilize esta técnica apenas se tiver a certeza de que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não verificam os dados da gama que está a ser movida, pelo que o melhor é incluir verificações no seu código.

Este exemplo remove fragmentos do mapa do fragmento.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

O mapa do fragmento reflete a localização do fragmento no GSM antes da eliminação do fragmento. Como o fragmento foi apagado, presume-se que isto foi intencional, e o intervalo de teclas sharding já não está em uso. Caso contrário, pode executar o tempo de restauro pontual. para recuperar o fragmento de um ponto no tempo anterior. (Nesse caso, reveja a seguinte secção para detetar inconsistências do fragmento.) Para recuperar, consulte [point in time recovery](sql-database-recovery-using-backups.md).

Uma vez que se assume que a eliminação da base de dados foi intencional, a última ação de limpeza administrativa é a eliminação da entrada no fragmento no gestor do mapa de fragmentos. Isto impede que a aplicação escreva inadvertidamente informações para um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Para detetar diferenças de mapeamento

O [método DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) seleciona e devolve um dos mapas de fragmentos (locais ou globais) como fonte de verdade e concilia mapeamentos em ambos os mapas de fragmentos (GSM e LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* A *localização* especifica o nome do servidor e o nome da base de dados.
* O parâmetro *shardMapName* é o nome do mapa do fragmento. Isto só é necessário se vários mapas de fragmentos forem geridos pelo mesmo gestor de mapas de fragmentos. Opcional.

## <a name="to-resolve-mapping-differences"></a>Para resolver as diferenças de mapeamento

O [método ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) seleciona um dos mapas de fragmentos (locais ou globais) como fonte de verdade e concilia mapeamentos em ambos os mapas de fragmentos (GSM e LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* O parâmetro *RecoveryToken* enumera as diferenças nos mapeamentos entre o GSM e o LSM para o fragmento específico.
* A [enumeração mappingDifferenceResolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) é usada para indicar o método para resolver a diferença entre os mapeamentos de fragmentos.
* **MappingDifferenceResolution.KeepShardMapping** é recomendado que quando o LSM contém o mapeamento preciso e, portanto, o mapeamento no fragmento deve ser usado. Normalmente, é o caso se houver uma falha: o fragmento agora reside num novo servidor. Uma vez que o fragmento deve ser removido do GSM (utilizando o método RecoveryManager.DetachShard), já não existe um mapeamento no GSM. Por conseguinte, o LSM deve ser utilizado para restabelecer o mapeamento do fragmento.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Prenda um fragmento ao ShardMap depois de um fragmento ser restaurado

O [método AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) fixa o fragmento dado ao mapa do fragmento. Em seguida, deteta quaisquer inconsistências do mapa de fragmentos e atualiza os mapeamentos para combinar com o fragmento no ponto da restauração do fragmento. Presume-se que a base de dados também é renomeada para refletir o nome original da base de dados (antes de o fragmento ser restaurado), uma vez que o tempo de restauração pontual está em incumprimento de uma nova base de dados anexada com a marca de tempo.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* O parâmetro de *localização* é o nome do servidor e nome da base de dados do fragmento que está a ser ligado.
* O parâmetro *shardMapName* é o nome do mapa do fragmento. Isto só é necessário quando vários mapas de fragmentos são geridos pelo mesmo gestor de mapas de fragmentos. Opcional.

Este exemplo adiciona um fragmento ao mapa do fragmento que foi recentemente restaurado de um tempo anterior. Uma vez que o fragmento (nomeadamente o mapeamento do fragmento no LSM) foi restaurado, é potencialmente inconsistente com a entrada de fragmentos no GSM. Fora deste código de exemplo, o fragmento foi restaurado e renomeado para o nome original da base de dados. Uma vez que foi restaurado, assume-se que o mapeamento no LSM é o mapeamento de confiança.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Atualizar as localizações do fragmento após uma geo-falha (restauro) dos fragmentos

Se houver uma geo-falha, a base de dados secundária é tornada a escrita acessível e torna-se a nova base de dados primária. O nome do servidor, e potencialmente a base de dados (dependendo da sua configuração), podem ser diferentes da primária original. Por conseguinte, as entradas de mapeamento do fragmento no GSM e lSM devem ser fixadas. Da mesma forma, se a base de dados for restaurada a um nome ou localização diferente, ou a um ponto mais precoce, isso pode causar inconsistências nos mapas do fragmento. O Shard Map Manager trata da distribuição de ligações abertas à base de dados correta. A distribuição baseia-se nos dados do mapa do fragmento e no valor da chave de sharding que é o alvo do pedido de aplicações. Após uma falha geo-failover, esta informação deve ser atualizada com o nome exato do servidor, nome da base de dados e mapeamento de fragmentos da base de dados recuperada.

## <a name="best-practices"></a>Melhores práticas

Geo-failover e recuperação são operações normalmente geridas por um administrador de nuvem da aplicação utilizando intencionalmente uma das funcionalidades de continuidade do negócio das bases de dados Azure SQL. O planeamento da continuidade do negócio requer processos, procedimentos e medidas para garantir que as operações comerciais possam continuar sem interrupção. Os métodos disponíveis no âmbito da classe RecoveryManager devem ser utilizados neste fluxo de trabalho para garantir que o GSM e o LSM sejam mantidos atualizados com base nas medidas de recuperação tomadas. Existem cinco passos básicos para garantir adequadamente que o GSM e o LSM reflitam as informações precisas após um evento de failover. O código de aplicação para executar estes passos pode ser integrado nas ferramentas existentes e no fluxo de trabalho.

1. Recupere o RecoveryManager do ShardMapManager.
2. Retire o velho fragmento do mapa do fragmento.
3. Prenda o novo fragmento ao mapa do fragmento, incluindo a nova localização do fragmento.
4. Detete inconsistências no mapeamento entre o GSM e o LSM.
5. Resolver diferenças entre o GSM e o LSM, confiando no LSM.

Este exemplo executa os seguintes passos:

1. Remove fragmentos do Mapa do Fragmento que refletem as localizações do fragmento antes do evento de failover.
2. Anexa fragmentos ao Mapa do Fragmento que reflete as novas localizações do fragmento (o parâmetro "Configuração.SecondaryServer" é o novo nome do servidor, mas o mesmo nome de base de dados).
3. Recupera os tokens de recuperação detetando diferenças de mapeamento entre o GSM e o LSM para cada fragmento.
4. Resolve as inconsistências confiando no mapeamento do LSM de cada fragmento.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
