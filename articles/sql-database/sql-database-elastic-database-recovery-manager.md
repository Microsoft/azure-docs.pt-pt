---
title: Utilizar o Gestor de recuperação para corrigir problemas de mapa de partições horizontais | Documentos da Microsoft
description: Usar a classe RecoveryManager para resolver problemas com mapas de partições horizontais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 1bab1ed9e2a24b0a84f4327d47a910934319b397
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849980"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Utilizar a classe RecoveryManager para corrigir problemas do mapa de partições horizontais

O [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) classe fornece os aplicativos ADO.NET a capacidade de facilmente detetar e corrigir quaisquer inconsistências entre o mapa de partições horizontais global (GSM) e o mapa de partições horizontais local (LSM) num ambiente de base de dados em partição horizontal.

O GSM e LSM controlam o mapeamento de cada base de dados num ambiente em partição horizontal. Ocasionalmente, uma interrupção ocorre entre o GSM e o LSM. Nesse caso, utilize a classe RecoveryManager para detetar e reparar a garantia de reparação.

A classe RecoveryManager faz parte do [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md).

![Mapa de partições horizontais][1]

Para obter definições de termo, consulte [Glossário de ferramentas de bases de dados elásticas](sql-database-elastic-scale-glossary.md). Para compreender como o **ShardMapManager** é utilizado para gerir os dados numa solução em partição horizontal, consulte [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Porquê utilizar o Gestor de recuperação

Num ambiente de base de dados em partição horizontal, existe um inquilino por base de dados e muitas bases de dados por servidor. Também pode haver vários servidores no ambiente. Cada base de dados é mapeado no mapa de partições horizontais, para que as chamadas podem ser encaminhadas para o servidor correto e a base de dados. Bases de dados são controladas em conformidade com um **chave de fragmentação**, e cada partição horizontal é atribuído um **intervalo de valores chave**. Por exemplo, uma chave de fragmentação pode representar os nomes de cliente de "D" para "F." O mapeamento de todas as partições horizontais (também conhecido como bases de dados) e seus intervalos de mapeamento estão contidas na **mapa de partições horizontais global (GSM)**. Cada base de dados também contém um mapa dos intervalos contidos na partição horizontal que é conhecido como o **mapa de partições horizontais local (LSM)**. Quando uma aplicação que se liga a uma partição horizontal, o mapeamento é colocado em cache com a aplicação para uma recuperação rápida. O LSM é utilizado para validar dados em cache.

O GSM e LSM podem ficar dessincronizada pelos seguintes motivos:

1. A eliminação de uma partição horizontal cujo intervalo é crê-se que já não está em utilização ou mudar o nome de uma partição horizontal. A eliminar uma partição horizontal resulta numa **órfãos mapeamento de partições horizontais**. Da mesma forma, uma base de dados de nome mudado pode causar um mapeamento de partições horizontais órfãos. Dependendo da intenção da alteração, a partição horizontal poderá ter de ser removido ou a localização de partição horizontal tem de ser atualizado. Para recuperar uma base de dados eliminada, consulte [restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md).
2. Ocorre um evento de ativação pós-falha geográfica. Para continuar, um tem de atualizar o nome do servidor e o nome de base de dados do Gestor de mapas de partições horizontais no aplicativo e, em seguida, atualizar os detalhes de mapeamento de partições horizontais para todas as partições horizontais num mapa de partições horizontais. Se houver uma ativação pós-falha geográfica, essa lógica de recuperação deve ser automatizada do fluxo de trabalho de ativação pós-falha. Automatização de ações de recuperação permite uma capacidade de gerenciamento sem conflitos para bases de dados geo-ativado e evita as ações humanas manuais. Para saber mais sobre as opções para recuperar uma base de dados, se houver uma indisponibilidade do Centro de dados, veja [continuidade de negócio](sql-database-business-continuity.md) e [recuperação após desastre](sql-database-disaster-recovery.md).
3. Uma partição horizontal ou a base de dados ShardMapManager é restaurado para um anteriormente ponto no tempo. Para saber mais sobre o ponto de recuperação de tempo utilizando cópias de segurança, consulte [recuperação utilizando cópias de segurança](sql-database-recovery-using-backups.md).

Para obter mais informações sobre a ferramentas do Azure SQL da base de dados elástica da base de dados, replicação geográfica, consulte o seguinte:

* [Descrição geral: Cloud de recuperação de desastres de continuidade e a base de dados empresariais com base de dados SQL](sql-database-business-continuity.md)
* [Introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md)  
* [Gestão de ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recuperando RecoveryManager de um ShardMapManager

A primeira etapa é criar uma instância de RecoveryManager. O [método GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) devolve o Gestor de recuperação para a atual [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) instância. Para resolver eventuais inconsistências verificadas no mapa de partições horizontais, tem primeiro de obter RecoveryManager para o mapa de partições horizontais específico.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Neste exemplo, o RecoveryManager é inicializado a partir do ShardMapManager. ShardMapManager que contém um ShardMap também já foi inicializada.

Uma vez que este código de aplicativo manipula o mapa de partições horizontais em si, as credenciais utilizadas no método de fábrica (no exemplo anterior, smmConnectionString) devem ser as credenciais que têm permissões de leitura / escrita na base de dados GSM referenciado pela ligação cadeia de caracteres. Estas credenciais são normalmente diferentes das credenciais utilizadas para abrir ligações para encaminhamento dependente de dados. Para obter mais informações, consulte [com as credenciais no cliente de base de dados elástica](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Remover uma partição horizontal do ShardMap depois de eliminar uma partição horizontal

O [DetachShard método](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) desliga a partição horizontal determinada do mapa de partições horizontais e elimina os mapeamentos associados com a partição horizontal.  

* O parâmetro de localização é a localização de partição horizontal, especificamente o nome do servidor e o nome de base de dados, de partição horizontal que está a ser desanexado.
* O parâmetro de shardMapName é o nome do mapa de partições horizontais. Isto só é necessário quando vários mapas de partições horizontais são geridos pelo Gestor de mapas de partições horizontais mesmo. Opcional.

> [!IMPORTANT]
> Use essa técnica somente se estiver se de que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não verificar dados para o intervalo que está a ser movido, então é melhor incluir verificações em seu código.

Neste exemplo remove as partições horizontais de mapa de partições horizontais.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

O mapa de partições horizontais reflete a localização de partições horizontais no GSM antes da eliminação de partição horizontal. Uma vez que a partição horizontal foi eliminada, presume-se isto foi intencional e o intervalo da chave de fragmentação não se encontra em utilização. Caso contrário, pode executar a hora de início de ponto de restauro. Para recuperar a partição horizontal a partir de um ponto no tempo anterior. (Nesse caso, reveja a secção seguinte para detectar inconsistências de partições horizontais.) Para recuperar, consulte [ponto de recuperação de tempo](sql-database-recovery-using-backups.md).

Porque se assume que a eliminação de base de dados foi intencional, a ação de limpeza administrativas final é eliminar a entrada para a partição horizontal no Gestor de mapas de partições horizontais. Isto impede que o aplicativo de inadvertidamente escrever as informações para um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Para detetar as diferenças de mapeamento

O [DetectMappingDifferences método](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) seleciona e devolve um dos mapas de partições horizontais (locais ou globais) como a fonte verdadeira e reconcilia mapeamentos em ambos os mapas de partições horizontais (GSM e LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* O *localização* Especifica o nome do servidor e o nome de base de dados.
* O *shardMapName* parâmetro é o nome do mapa de partições horizontais. Isto só é necessário se vários mapas de partições horizontais são geridos pelo Gestor de mapas de partições horizontais mesmo. Opcional.

## <a name="to-resolve-mapping-differences"></a>Para resolver as diferenças de mapeamento

O [ResolveMappingDifferences método](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) seleciona um dos mapas de partições horizontais (locais ou globais) como a fonte verdadeira e concilie mapeamentos em ambos os mapas de partições horizontais (GSM e LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* O *RecoveryToken* parâmetro enumera as diferenças dos mapeamentos entre o GSM e LSM para a partição horizontal específica.
* O [MappingDifferenceResolution enumeração](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) é utilizado para indicar o método para resolver a diferença entre os mapeamentos de partição horizontal.
* **MappingDifferenceResolution.KeepShardMapping** é recomendado que quando o LSM contém o mapeamento preciso e, portanto, o mapeamento na partição horizontal deve ser utilizado. Isso normalmente é o caso, se houver uma ativação pós-falha: a partição horizontal agora reside num novo servidor. Uma vez que a partição horizontal tem de ser removida do GSM (usando o método RecoveryManager.DetachShard), um mapeamento já não existe o GSM. Por conseguinte, o LSM deve ser usado para voltar a estabelecer o mapeamento de partição horizontal.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anexar uma partição horizontal para o ShardMap após o restauro de uma partição horizontal

O [AttachShard método](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) anexa a partição horizontal especificada para o mapa de partições horizontais. Em seguida, Deteta quaisquer inconsistências de mapa de partições horizontais e atualiza os mapeamentos de acordo com a partição horizontal no ponto de restauro de partição horizontal. Presume-se que a base de dados também foi mudado para refletir o banco de dados nome original (antes de que foi restaurada a partição horizontal), uma vez que o restauro de ponto no tempo é predefinido para uma nova base de dados anexada com o carimbo de hora.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* O *localização* parâmetro é o nome do servidor e o nome de base de dados, de partição horizontal que está a ser anexado.
* O *shardMapName* parâmetro é o nome do mapa de partições horizontais. Isto só é necessário quando vários mapas de partições horizontais são geridos pelo Gestor de mapas de partições horizontais mesmo. Opcional.

Este exemplo adiciona uma partição horizontal para o mapa de partições horizontais que foi recentemente restaurado a partir de uma hora de início de ponto anterior. Uma vez que a partição horizontal (ou seja, o mapeamento para a partição horizontal no LSM) foi restaurada, é potencialmente inconsistente com a entrada de partição horizontal no GSM. Fora deste código de exemplo, a partição horizontal foi restaurada e o nome mudada para o nome original da base de dados. Uma vez que ele foi restaurado, é assumido que o mapeamento no LSM é o mapeamento fidedigno.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>A atualizar localizações de partições horizontais após uma ativação pós-falha geográfica (restauro) de partições horizontais

Se houver uma ativação pós-falha geográfica, a base de dados secundária é feita escrever acessível e torna-se a nova base de dados primário. O nome do servidor e, potencialmente, a base de dados (consoante a configuração), poderá ser diferente do principal original. Por conseguinte, as entradas de mapeamento para a partição horizontal no GSM e LSM devem ser corrigidas. Da mesma forma, se a base de dados é restaurado para um nome diferente ou local ou para um ponto anterior no tempo, isso poderá causar inconsistências de mapas de partições horizontais. O Gestor de mapas de partições horizontais processa a distribuição de conexões abertas a base de dados correta. Distribuição baseia-se os dados no mapa de partições horizontais e o valor da chave de fragmentação que é o destino do pedido de aplicações. Após uma ativação pós-falha geográfica, estas informações têm de ser atualizadas com o nome do servidor precisas, o nome de base de dados e o mapeamento de partições horizontais da base de dados recuperada.

## <a name="best-practices"></a>Melhores práticas

Ativação pós-falha geográfica e a recuperação são operações que normalmente é geridas por um administrador de nuvem do aplicativo intencionalmente utilizando uma das funcionalidades de continuidade de negócio de bases de dados SQL do Azure. Planejamento da continuidade dos negócios requer processos, procedimentos e medidas para garantir que as operações comerciais podem continuar sem interrupções. Os métodos disponíveis como parte da classe RecoveryManager deve ser utilizada neste fluxo de trabalho para garantir que o GSM e LSM são mantidas atualizadas com base na ação de recuperação executada. Existem cinco passos básicos para garantir corretamente que o GSM e LSM refletem as informações precisas após um evento de ativação pós-falha. O código do aplicativo para executar estes passos pode ser integrado de fluxo de trabalho e ferramentas existentes.

1. Obter o RecoveryManager o ShardMapManager.
2. Desligar a partição horizontal antiga do mapa de partições horizontais.
3. Anexe a nova partição horizontal para o mapa de partições horizontais, incluindo a nova localização de partição horizontal.
4. Detectar inconsistências no mapeamento entre o GSM e LSM.
5. Resolva diferenças entre o GSM e LSM, considerar como fidedigno o LSM.

Neste exemplo executa as seguintes etapas:

1. Remove as partições horizontais de mapa de partições horizontais que refletem as localizações de partições horizontais antes do evento de ativação pós-falha.
2. Anexa partições horizontais para o mapa de partições horizontais que reflete as novas localizações de partições horizontais (o parâmetro "Configuration.SecondaryServer" é o novo nome de servidor, mas o mesmo nome de base de dados).
3. Obtém os tokens de recuperação através da deteção de diferenças de mapeamento entre o GSM e LSM para cada partição horizontal.
4. Resolve as inconsistências por confiar o mapeamento de LSM de cada partição horizontal.

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
