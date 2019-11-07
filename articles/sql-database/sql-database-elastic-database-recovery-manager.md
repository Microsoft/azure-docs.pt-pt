---
title: Usando o Gerenciador de recuperação para corrigir problemas de mapa de fragmentos
description: Usar a classe RecoveryManager para resolver problemas com mapas de fragmentos
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 5920f0a3f08d83b1300956ca830b3b9b827fa5e2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690479"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais

A classe [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) fornece aos aplicativos ADO.net a capacidade de detectar e corrigir facilmente qualquer inconsistência entre o GSM (mapa de fragmentos global) e o LSM (mapa de fragmentos local) em um ambiente de banco de dados fragmentado.

O GSM e o LSM acompanham o mapeamento de cada banco de dados em um ambiente fragmentado. Ocasionalmente, ocorre uma interrupção entre o GSM e o LSM. Nesse caso, use a classe RecoveryManager para detectar e reparar a interrupção.

A classe RecoveryManager faz parte da [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md).

![Mapa de fragmentos][1]

Para definições de termo, consulte [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md). Para entender como o **ShardMapManager** é usado para gerenciar dados em uma solução fragmentada, consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Por que usar o Gerenciador de recuperação

Em um ambiente de banco de dados fragmentado, há um locatário por banco de dados, e muitos bancos por servidor. Também pode haver muitos servidores no ambiente. Cada banco de dados é mapeado no mapa de fragmentos, portanto, as chamadas podem ser roteadas para o servidor e o banco de dados corretos. Os bancos de dados são acompanhados de acordo com uma **chave de fragmentação**e cada fragmento recebe um **intervalo de valores de chave**. Por exemplo, uma chave de fragmentação pode representar os nomes de clientes de "D" a "F". O mapeamento de todos os fragmentos (também conhecido como bancos de dados) e seus intervalos de mapeamento estão contidos no **GSM (mapa de fragmentos global)** . Cada banco de dados também contém um mapa dos intervalos contidos no fragmento que é conhecido como **LSM (mapa de fragmentos local)** . Quando um aplicativo se conecta a um fragmento, o mapeamento é armazenado em cache com o aplicativo para recuperação rápida. O LSM é usado para validar dados armazenados em cache.

O GSM e o LSM podem ficar fora de sincronia pelos seguintes motivos:

1. A exclusão de um fragmento cujo intervalo é acreditado não estar mais em uso ou renomear um fragmento. Excluir um fragmento resulta em um **mapeamento de fragmento órfão**. Da mesma forma, um banco de dados renomeado pode causar um mapeamento de fragmento órfão. Dependendo da intenção da alteração, o fragmento pode precisar ser removido ou o local do fragmento precisa ser atualizado. Para recuperar um banco de dados excluído, consulte [restaurar um banco de dados excluído](sql-database-recovery-using-backups.md).
2. Ocorre um evento de failover geográfico. Para continuar, é necessário atualizar o nome do servidor e o nome do banco de dados do Gerenciador de mapa de fragmentos no aplicativo e, em seguida, atualizar os detalhes de mapeamento de fragmento para todos os fragmentos em um mapa de fragmentos. Se houver um failover geográfico, essa lógica de recuperação deverá ser automatizada no fluxo de trabalho de failover. Automatizar ações de recuperação permite uma capacidade de gerenciamento descomplicada para bancos de dados habilitados geograficamente e evita ações humanas manuais. Para saber mais sobre as opções para recuperar um banco de dados se houver uma interrupção data center, consulte [continuidade de negócios](sql-database-business-continuity.md) e [recuperação de desastres](sql-database-disaster-recovery.md).
3. Um fragmento ou o banco de dados ShardMapManager é restaurado para um momento anterior. Para saber mais sobre a recuperação pontual usando backups, consulte [recuperação usando backups](sql-database-recovery-using-backups.md).

Para obter mais informações sobre ferramentas de banco de dados elástico do banco de dados SQL do Azure, replicação geográfica e restauração, consulte o seguinte:

* [Visão geral: continuidade dos negócios de nuvem e recuperação de desastre do banco de dados](sql-database-business-continuity.md)
* [Introdução às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md)  
* [Gerenciamento de ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recuperando RecoveryManager de um ShardMapManager

A primeira etapa é criar uma instância de RecoveryManager. O [método Getrecoverymanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) retorna o Gerenciador de recuperação para a instância de [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) atual. Para resolver quaisquer inconsistências no mapa de fragmentos, você deve primeiro recuperar o RecoveryManager para o mapa de fragmentos específico.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Neste exemplo, o RecoveryManager é inicializado a partir do ShardMapManager. O ShardMapManager que contém um ShardMap também já foi inicializado.

Como esse código de aplicativo manipula o mapa de fragmentos em si, as credenciais usadas no método de fábrica (no exemplo anterior, smmConnectionString) devem ser credenciais com permissões de leitura/gravação no banco de dados GSM referenciado pela conexão Strings. Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões para roteamento dependente de dados. Para obter mais informações, consulte [usando credenciais no cliente do banco de dados elástico](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Removendo um fragmento do ShardMap depois que um fragmento é excluído

O [método DetachShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) desanexa o fragmento fornecido do mapa de fragmentos e exclui os mapeamentos associados ao fragmento.  

* O parâmetro Location é o local do fragmento, especificamente o nome do servidor e o nome do banco de dados, do fragmento que está sendo desanexado.
* O parâmetro shardMapName é o nome do mapa de fragmentos. Isso só é necessário quando vários mapas de fragmento são gerenciados pelo mesmo Gerenciador de mapa de fragmentos. Opcional.

> [!IMPORTANT]
> Use essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não verificam os dados para o intervalo que está sendo movido, portanto, é melhor incluir verificações em seu código.

Este exemplo remove fragmentos do mapa de fragmentos.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

O mapa de fragmentos reflete o local do fragmento no GSM antes da exclusão do fragmento. Como o fragmento foi excluído, supõe-se que isso era intencional e o intervalo da chave de fragmentação não está mais em uso. Caso contrário, você pode executar a restauração pontual. para recuperar o fragmento de um ponto anterior no tempo. (Nesse caso, examine a seção a seguir para detectar inconsistências de fragmentos.) Para recuperar, consulte [recuperação pontual](sql-database-recovery-using-backups.md).

Como supõe-se que a exclusão do banco de dados foi intencional, a ação de limpeza administrativa final é excluir a entrada para o fragmento no Gerenciador de mapa de fragmentos. Isso impede que o aplicativo grave informações inadvertidamente em um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Para detectar diferenças de mapeamento

O [método DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) seleciona e retorna um dos mapas de fragmento (local ou global) como a fonte de verdade e reconcilia mapeamentos em ambos os mapas de fragmento (GSM e LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* O *local* especifica o nome do servidor e o nome do banco de dados.
* O parâmetro *shardMapName* é o nome do mapa de fragmentos. Isso só será necessário se vários mapas de fragmentos forem gerenciados pelo mesmo Gerenciador de mapa de fragmentos. Opcional.

## <a name="to-resolve-mapping-differences"></a>Para resolver diferenças de mapeamento

O [método ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) seleciona um dos mapas de fragmento (local ou global) como a origem de verdade e reconcilia os mapeamentos em ambos os mapas de fragmento (GSM e LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* O parâmetro *RecoveryToken* enumera as diferenças nos mapeamentos entre o GSM e o LSM para o fragmento específico.
* A [Enumeração MappingDifferenceResolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) é usada para indicar o método para resolver a diferença entre os mapeamentos de fragmentos.
* **MappingDifferenceResolution. KeepShardMapping** é recomendado quando o LSM contém o mapeamento preciso e, portanto, o mapeamento no fragmento deve ser usado. Normalmente, esse é o caso se houver um failover: o fragmento agora reside em um novo servidor. Como o fragmento deve ser removido primeiro do GSM (usando o método RecoveryManager. DetachShard), um mapeamento não existe mais no GSM. Portanto, o LSM deve ser usado para restabelecer o mapeamento de fragmentos.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anexar um fragmento ao ShardMap após a restauração de um fragmento

O [método AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) anexa o fragmento fornecido ao mapa de fragmentos. Em seguida, ele detecta quaisquer inconsistências de mapa de fragmentos e atualiza os mapeamentos para que correspondam ao fragmento no ponto da restauração do fragmento. Supõe-se que o banco de dados também seja renomeado para refletir o nome do banco de dados original (antes de o fragmento ser restaurado), já que a restauração pontual usa como padrão um novo banco de dados anexado com o carimbo de data/hora.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* O parâmetro *Location* é o nome do servidor e o nome do banco de dados do fragmento que está sendo anexado.
* O parâmetro *shardMapName* é o nome do mapa de fragmentos. Isso só é necessário quando vários mapas de fragmento são gerenciados pelo mesmo Gerenciador de mapa de fragmentos. Opcional.

Este exemplo adiciona um fragmento ao mapa de fragmentos que foi restaurado recentemente de um ponto anterior. Como o fragmento (ou seja, o mapeamento para o fragmento no LSM) foi restaurado, ele é potencialmente inconsistente com a entrada de fragmento no GSM. Fora deste código de exemplo, o fragmento foi restaurado e renomeado para o nome original do banco de dados. Como ele foi restaurado, presume-se que o mapeamento no LSM seja o mapeamento confiável.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Atualizando locais de fragmento após um failover geográfico (restauração) dos fragmentos

Se houver um failover geográfico, o banco de dados secundário se tornará acessível para gravação e se tornará o novo banco de dados primário. O nome do servidor e, possivelmente, o banco de dados (dependendo da sua configuração), pode ser diferente do primário original. Portanto, as entradas de mapeamento para o fragmento no GSM e no LSM devem ser corrigidas. Da mesma forma, se o banco de dados for restaurado para um nome ou local diferente, ou para um ponto anterior no tempo, isso poderá causar inconsistências nos mapas de fragmentos. O Gerenciador de mapa de fragmentos manipula a distribuição de conexões abertas para o banco de dados correto. A distribuição é baseada nos dados no mapa de fragmentos e no valor da chave de fragmentação que é o destino da solicitação de aplicativos. Após um failover geográfico, essas informações devem ser atualizadas com o nome do servidor, o nome do banco de dados e o mapeamento de fragmentos precisos do banco de dados recuperado.

## <a name="best-practices"></a>Melhores práticas

O failover geográfico e a recuperação são operações normalmente gerenciadas por um administrador de nuvem do aplicativo, intencionalmente, utilizando um dos recursos de continuidade de negócios dos bancos de dados SQL do Azure. O planejamento de continuidade de negócios requer processos, procedimentos e medidas para garantir que as operações de negócios possam continuar sem interrupções. Os métodos disponíveis como parte da classe RecoveryManager devem ser usados nesse fluxo de trabalho para garantir que o GSM e o LSM sejam mantidos atualizados com base na ação de recuperação realizada. Há cinco etapas básicas para garantir adequadamente que o GSM e o LSM reflitam as informações precisas após um evento de failover. O código do aplicativo para executar essas etapas pode ser integrado às ferramentas e ao fluxo de trabalho existentes.

1. Recupere o RecoveryManager do ShardMapManager.
2. Desanexe o fragmento antigo do mapa de fragmentos.
3. Anexe o novo fragmento ao mapa de fragmentos, incluindo o novo local do fragmento.
4. Detecte inconsistências no mapeamento entre o GSM e o LSM.
5. Resolva as diferenças entre o GSM e o LSM, confiando no LSM.

Este exemplo executa as seguintes etapas:

1. Remove fragmentos do mapa de fragmentos que refletem os locais de fragmento antes do evento de failover.
2. Anexa fragmentos ao mapa de fragmentos que refletem os novos locais de fragmento (o parâmetro "Configuration. SecondaryServer" é o novo nome do servidor, mas o mesmo nome do banco de dados).
3. Recupera os tokens de recuperação detectando diferenças de mapeamento entre o GSM e o LSM para cada fragmento.
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
