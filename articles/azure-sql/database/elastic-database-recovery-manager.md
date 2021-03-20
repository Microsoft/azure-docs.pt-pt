---
title: Gestor de Recuperação para corrigir problemas de mapa de fragmentos
description: Use a classe RecoveryManager para resolver problemas com mapas de fragmentos
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792230"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A classe [RecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) fornece ADO.NET aplicações a capacidade de detetar e corrigir facilmente quaisquer inconsistências entre o mapa global de fragmentos (GSM) e o mapa de fragmentos local (LSM) num ambiente de base de dados fragmentos.

O GSM e o LSM acompanham o mapeamento de cada base de dados num ambiente desoçado. Ocasionalmente, ocorre uma rutura entre o GSM e o LSM. Nesse caso, utilize a classe RecoveryManager para detetar e reparar a rutura.

A classe RecoveryManager faz parte da biblioteca de [clientes Elastic Database.](elastic-database-client-library.md)

![Mapa de fragmentos][1]

Para definições de termo, consulte [o glossário de ferramentas elastic database](elastic-scale-glossary.md). Para entender como o **ShardMapManager** é usado para gerir dados numa solução de solução sharded, consulte a gestão do [mapa Shard.](elastic-scale-shard-map-management.md)

## <a name="why-use-the-recovery-manager"></a>Por que usar o gestor de recuperação

Num ambiente de base de dados desontes, há um inquilino por base de dados, e muitas bases de dados por servidor. Também pode haver muitos servidores no ambiente. Cada base de dados está mapeada no mapa de fragmentos, para que as chamadas possam ser encaminhadas para o servidor e base de dados corretos. As bases de dados são rastreadas de acordo com uma **chave de fragmentos,** e cada fragmento é atribuído uma **gama de valores-chave**. Por exemplo, uma chave de fragmentos pode representar os nomes dos clientes de "D" para "F". O mapeamento de todos os fragmentos (também conhecidos como bases de dados) e as suas gamas de mapeamento estão contidos no **mapa global de fragmentos (GSM)**. Cada base de dados também contém um mapa das gamas contidas no fragmento que é conhecido como o **mapa de fragmentos local (LSM)**. Quando uma aplicação se conecta a um fragmento, o mapeamento é em cache com a aplicação para uma recuperação rápida. O LSM é utilizado para validar dados em cache.

O GSM e o LSM podem ficar dessincronizados pelas seguintes razões:

1. A supressão de um fragmento cujo alcance se acredita já não estar em uso, ou renomeação de um fragmento. Apagar um fragmento resulta num **mapeamento de fragmentos órfãos.** Da mesma forma, uma base de dados renomeada pode causar um mapeamento de fragmentos órfãos. Dependendo da intenção da alteração, o fragmento pode ter de ser removido ou a localização do fragmento precisa de ser atualizada. Para recuperar uma base de dados eliminada, consulte [Restaurar uma base de dados eliminada](recovery-using-backups.md).
2. Ocorre um evento de geo-falha. Para continuar, é necessário atualizar o nome do servidor e o nome da base de dados do gestor do mapa de fragmentos na aplicação e, em seguida, atualizar os detalhes do mapeamento de fragmentos para todos os fragmentos num mapa de fragmentos. Se houver um geo-failover, essa lógica de recuperação deve ser automatizada dentro do fluxo de trabalho de failover. Automatizar ações de recuperação permite uma gestão sem atritos para bases de dados geo-activadas e evita ações humanas manuais. Para saber mais sobre as opções para recuperar uma base de dados se houver uma falha no data center, consulte [a Continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md) e a [Recuperação de Desastres.](disaster-recovery-guidance.md)
3. Ou um fragmento ou a base de dados ShardMapManager é restaurada para um tempo de ponto anterior. Para obter mais sobre a recuperação do tempo utilizando cópias de segurança, consulte [Recovery usando cópias de segurança](recovery-using-backups.md).

Para obter mais informações sobre as ferramentas de base de dados elásticas Azure SQL, geo-replicação e restauro, consulte o seguinte:

* [Visão geral: Continuidade do negócio em nuvem e recuperação de desastres na base de dados com Base de Dados SQL](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Começar com ferramentas elásticas de base de dados](elastic-scale-get-started.md)  
* [Gestão ShardMap](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recuperação de Recuperação De Um ShardMapManager

O primeiro passo é criar um caso RecoveryManager. O [método GetRecoveryManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) devolve o gestor de recuperação para o caso [atual do ShardMapManager.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Para resolver quaisquer inconsistências no mapa de fragmentos, você deve primeiro recuperar o RecoveryManager para o mapa de fragmentos particular.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Neste exemplo, o RecoveryManager é inicializado a partir do ShardMapManager. O ShardMapManager que contém um ShardMap também já está inicializado.

Uma vez que este código de aplicação manipula o próprio mapa de fragmentos, as credenciais utilizadas no método de fábrica (no exemplo anterior, smmConnectionString) devem ser credenciais que tenham permissões de leitura na base de dados GSM referenciadas pela cadeia de ligação. Estas credenciais são tipicamente diferentes das credenciais usadas para abrir ligações para o encaminhamento dependente de dados. Para obter mais informações, consulte [a utilização de credenciais no cliente da base de dados elástica.](elastic-scale-manage-credentials.md)

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Remover um fragmento do ShardMap depois de um fragmento ser eliminado

O [método DetachShard](/previous-versions/azure/dn842083(v=azure.100)) separa o fragmento dado do mapa de fragmentos e elimina os mapeamentos associados ao fragmento.  

* O parâmetro de localização é a localização do fragmento, especificamente o nome do servidor e o nome da base de dados, do fragmento que está a ser destacado.
* O parâmetro shardMapName é o nome do mapa de fragmentos. Isto só é necessário quando vários mapas de fragmentos são geridos pelo mesmo gestor de mapas de fragmentos. Opcional.

> [!IMPORTANT]
> Utilize esta técnica apenas se tiver a certeza de que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não verificam os dados para a gama que está a ser movida, pelo que o melhor é incluir verificações no seu código.

Este exemplo remove fragmentos do mapa de fragmentos.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

O mapa de fragmentos reflete a localização do fragmento no GSM antes da eliminação do fragmento. Como o fragmento foi apagado, presume-se que foi intencional, e a chave de fragmentos já não está em uso. Caso contrário, pode executar o tempo de restauro. para recuperar o fragmento de um ponto no tempo anterior. (Nesse caso, reveja a seguinte secção para detetar inconsistências de fragmentos.) Para recuperar, consulte [Ponto na recuperação do tempo.](recovery-using-backups.md)

Uma vez que se supõe que a supressão da base de dados foi intencional, a ação de limpeza administrativa final consiste em apagar a entrada no fragmento no gestor do mapa de fragmentos. Isto impede que a aplicação escreva inadvertidamente informações para um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Para detetar diferenças de mapeamento

O [método DetectMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) seleciona e devolve um dos mapas de fragmentos (locais ou globais) como fonte de verdade e concilia mapeamentos em ambos os mapas de fragmentos (GSM e LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* A *localização* especifica o nome do servidor e o nome da base de dados.
* O parâmetro *shardMapName* é o nome do mapa de fragmentos. Isto só é necessário se vários mapas de fragmentos forem geridos pelo mesmo gestor de mapas de fragmentos. Opcional.

## <a name="to-resolve-mapping-differences"></a>Para resolver diferenças de mapeamento

O [método ResolveMappingDifferences](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) seleciona um dos mapas de fragmentos (locais ou globais) como fonte de verdade e concilia mapeamentos em ambos os mapas de fragmentos (GSM e LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* O parâmetro *RecoveryToken* enumera as diferenças nos mapeamentos entre o GSM e o LSM para o fragmento específico.
* A [enumeração mappingDifferenceResolution](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) é usada para indicar o método para resolver a diferença entre os mapeamentos de fragmentos.
* **MappingDifferenceResolution.KeepShardMapping** é recomendado que quando o LSM contém o mapeamento preciso e, portanto, o mapeamento no fragmento deve ser usado. Este é normalmente o caso se houver uma falha: o fragmento agora reside num novo servidor. Uma vez que o fragmento deve ser removido primeiro do GSM (utilizando o método RecoveryManager.DetachShard), já não existe um mapeamento no GSM. Por conseguinte, o LSM deve ser utilizado para restabelecer o mapeamento de fragmentos.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Prenda um fragmento ao ShardMap depois de restaurado um fragmento

O [método AttachShard](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) liga o fragmento dado ao mapa de fragmentos. Em seguida, deteta quaisquer inconsistências do mapa de fragmentos e atualiza os mapeamentos para combinar com o fragmento no ponto da restauração do fragmento. Presume-se que a base de dados também é renomeada para refletir o nome original da base de dados (antes do fragmento ser restaurado), uma vez que o prazo de restauração por defeitos para uma nova base de dados anexada à marca de tempo.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* O parâmetro *de localização* é o nome do servidor e o nome da base de dados, do fragmento que está a ser anexado.
* O parâmetro *shardMapName* é o nome do mapa de fragmentos. Isto só é necessário quando vários mapas de fragmentos são geridos pelo mesmo gestor de mapas de fragmentos. Opcional.

Este exemplo adiciona um fragmento ao mapa de fragmentos que foi recentemente restaurado a partir de um tempo de ponto-in anterior. Uma vez que o fragmento (nomeadamente o mapeamento do fragmento no LSM) foi restaurado, é potencialmente inconsistente com a entrada de fragmentos no GSM. Fora deste código de exemplo, o fragmento foi restaurado e renomeado para o nome original da base de dados. Desde que foi restaurado, é assumido que o mapeamento no LSM é o mapeamento de confiança.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Atualizar as localizações de fragmentos após uma geo-falha (restauro) dos fragmentos

Se houver um geo-failover, a base de dados secundária torna-se acessível à escrita e torna-se a nova base de dados primária. O nome do servidor, e potencialmente a base de dados (dependendo da sua configuração), pode ser diferente do primário original. Por conseguinte, as entradas de mapeamento do fragmento no GSM e LSM devem ser fixadas. Da mesma forma, se a base de dados for restaurada a um nome ou localização diferente, ou a um ponto anterior no tempo, isso pode causar inconsistências nos mapas de fragmentos. O Shard Map Manager trata da distribuição de ligações abertas à base de dados correta. A distribuição baseia-se nos dados do mapa de fragmentos e no valor da chave de fragmentos que é o alvo do pedido de aplicações. Após uma falha de geo-failover, estas informações devem ser atualizadas com o nome exato do servidor, nome da base de dados e mapeamento de fragmentos da base de dados recuperada.

## <a name="best-practices"></a>Melhores práticas

Geo-failover e recuperação são operações normalmente geridas por um administrador em nuvem da aplicação utilizando intencionalmente funcionalidades de continuidade de negócios da Azure SQL Database. O planeamento da continuidade do negócio requer processos, procedimentos e medidas para garantir que as operações comerciais possam continuar sem interrupção. Os métodos disponíveis no âmbito da classe RecoveryManager devem ser utilizados neste fluxo de trabalho para garantir que o GSM e o LSM sejam mantidos atualizados com base nas medidas de recuperação tomadas. Existem cinco passos básicos para garantir que o GSM e o LSM reflitam as informações exatas após um evento de failover. O código de aplicação para executar estes passos pode ser integrado em ferramentas e fluxos de trabalho existentes.

1. Recuperar o RecoveryManager do ShardMapManager.
2. Retire o fragmento velho do mapa de fragmentos.
3. Fixe o novo fragmento ao mapa de fragmentos, incluindo a nova localização do fragmento.
4. Detetar inconsistências no mapeamento entre o GSM e o LSM.
5. Resolver as diferenças entre o GSM e o LSM, confiando no LSM.

Este exemplo executa os seguintes passos:

1. Remove fragmentos do Shard Map que refletem as localizações de fragmentos antes do evento de failover.
2. Anexa fragmentos ao Mapa de Fragmentos que refletem as novas localizações de fragmentos (o parâmetro "Configuration.SecondaryServer" é o nome do novo servidor, mas o mesmo nome de base de dados).
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

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png