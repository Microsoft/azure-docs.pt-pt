---
title: Como utilizar a replicação da Colmeia Apache em clusters Azure HDInsight
description: Aprenda a usar a replicação da Colmeia em clusters HDInsight para replicar a metástore da Colmeia e o lago de dados Azure Data Lake De armazenamento Gen 2.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 2e57b7d8121ac0bd6fc9cb693750c41a0ef6a5bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733062"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Como utilizar a replicação da Colmeia Apache em clusters Azure HDInsight

No contexto de bases de dados e armazéns, a replicação é o processo de duplicação de entidades de um armazém para outro. A duplicação pode aplicar-se a uma base de dados inteira ou a um nível menor, como uma tabela ou partição. O objetivo é ter uma réplica que muda sempre que a entidade base muda. A replicação na Colmeia Apache centra-se na recuperação de desastres e oferece replicação de cópia primária unidirecional. Nos clusters HDInsight, a replicação da colmeia pode ser usada para replicar de forma unidirecional a metastore da Colmeia e o lago de dados subjacente associado na Azure Data Lake Storage Gen2.  

A Replicação da Colmeia tem evoluído ao longo dos anos com versões mais recentes que fornecem uma melhor funcionalidade e sendo mais rápida e menos intensiva de recursos. Neste artigo, discutimos a Replicação da Colmeia (Replv2) que é suportada tanto nos tipos de cluster HDInsight 3.6 como HDInsight 4.0.

## <a name="advantages-of-replv2"></a>Vantagens do replv2

[A Replicação da ColmeiaV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) (também chamada Replv2) tem as seguintes vantagens sobre a primeira versão da replicação da Colmeia que utilizou a [HIVE IMPORT-EXPORT](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport):

- Replicação incremental baseada em eventos
- Replicação pontual  
- Requisitos reduzidos de largura de banda  
- Redução do número de cópias intermédias  
- O estado de replicação é mantido
- Replicação restrita  
- Suporte para um modelo hub e spoke  
- Suporte para tabelas ACID (em HDInsight 4.0)

## <a name="replication-phases"></a>Fases de replicação

A replicação baseada em eventos de colmeia está configurada entre os aglomerados primários e secundários. Esta replicação consiste em duas fases distintas: a colocação de botas e as execuções incrementais.

### <a name="bootstrapping"></a>Bootstrapping

O bootstrapping destina-se a funcionar uma vez para replicar o estado base das bases de dados do primário ao secundário. Pode configurar a configuração de botas, se necessário, para incluir um subconjunto das tabelas na base de dados-alvo onde a replicação precisa de ser ativada.

### <a name="incremental-runs"></a>Execuções incrementais

Após a armadilhagem das botas, as execuções incrementais são automatizadas no cluster primário e os eventos gerados durante estas execuções incrementais são reproduzidos no cluster secundário. Quando o cluster secundário alcança o aglomerado primário, o secundário torna-se consistente com os eventos primários.

## <a name="replication-commands"></a>Comandos de replicação

A Hive oferece um conjunto de comandos REPL `DUMP` – e - para `LOAD` `STATUS` orquestrar o fluxo de eventos. O `DUMP` comando gera um registo local de todos os eventos DDL/DML no cluster primário. O `LOAD` comando é uma abordagem para copiar preguiçosamente metadados e dados registados na saída de despejo de replicação extraída e é executado no cluster alvo. O `STATUS` comando corre a partir do cluster alvo para fornecer o mais recente ID do evento que a carga de replicação mais recente foi replicada com sucesso.

### <a name="set-replication-source"></a>Definir fonte de replicação

Antes de começar com a replicação, certifique-se de que a base de dados que deve ser replicada é definida como a fonte de replicação. Pode utilizar o `DESC DATABASE EXTENDED <db_name>` comando para determinar se o parâmetro está definido com o nome da `repl.source.for` apólice.

Se a apólice estiver programada e o `repl.source.for` parâmetro não estiver definido, então tem de definir primeiro este parâmetro utilizando `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Despeje metadados para o lago de dados

O `REPL DUMP [database name]. => location / event_id` comando é utilizado na fase de bootstrap para despejar metadados relevantes para a Azure Data Lake Storage Gen2. O `event_id` especificado é o evento mínimo para o qual os metadados relevantes foram colocados na Azure Data Lake Storage Gen2. 
 
```sql
repl dump tpcds_orc; 
```
Exemplo de saída:

| dump_dir|last_repl_id
|-|-|
|/tmp/colmeia/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Carregar dados para o cluster alvo

O `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` comando é utilizado para carregar dados no cluster alvo tanto para a bootstrap como para as fases incrementais de replicação. `[database name]`Pode ser o mesmo que a fonte ou um nome diferente no cluster alvo. O `[location]` representa a localização a partir da saída do `REPL DUMP` comando anterior. Isto significa que o cluster-alvo deve ser capaz de falar com o cluster de origem. A `WITH` cláusula foi adicionada principalmente para evitar o reinício do cluster alvo, permitindo a replicação.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Saída do último ID do evento replicado

O `REPL STATUS [database name]` comando é executado em clusters-alvo e saídas a última replicada `event_id` . O comando também permite que os utilizadores saibam a que estado o seu cluster alvo é replicado. Pode utilizar a saída deste comando para construir o próximo `REPL DUMP` comando para replicação incremental.

```sql
repl status tpcds_orc;
```

Exemplo de saída:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Despeje dados e metadados relevantes para o lago de dados

O `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` comando é utilizado para despejar metadados e dados relevantes para o Armazenamento do Lago de Dados Azure. Este comando é utilizado na fase incremental e é executado no armazém de origem. O `FROM [event-id]` é necessário para a fase incremental, e o valor pode ser derivado `event-id` executando o comando no armazém `REPL STATUS [database name]` alvo.

```sql
repl dump tpcds_orc from 2925;
```

Exemplo de saída:

|dump_dir|last_repl_id|
|-|-|
| /tmp/colmeia/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Processo de replicação da colmeia

Os seguintes passos são os eventos sequenciais que ocorrem durante o processo de replicação da Colmeia.

1. Certifique-se de que as tabelas a replicar são definidas como fonte de replicação para uma determinada política.

1. O `REPL_DUMP` comando é emitido para o cluster primário com restrições associadas como nome de base de dados, gama de ID de evento e URL de armazenamento de gen2 de armazenamento de dados de dados do Azure.

1. O sistema serializa uma descarga de todos os eventos rastreados desde a metásta até ao mais recente. Esta lixeira é armazenada na conta de armazenamento do Azure Data Lake Storage Gen2 no cluster primário do URL que é especificado pelo `REPL_DUMP` .  

1. O cluster primário persiste os metadados de replicação para o armazenamento de Azure Data Lake Storage Gen2 do cluster primário. O caminho é configurável na Colmeia Config UI em Ambari. O processo fornece o caminho onde os metadados são armazenados e o ID do mais recente evento DML/DDL rastreado.

1. O `REPL_LOAD` comando é emitido a partir do agrupamento secundário. O comando aponta para o caminho configurado no passo 3.

1. O cluster secundário lê o ficheiro de metadados com eventos rastreados que foram criados no Passo 3. Certifique-se de que o cluster secundário tem conectividade de rede com o armazenamento da Azure Data Lake Network Gen2 do cluster primário de onde os eventos `REPL_DUMP` rastreados são armazenados.  

1. O cluster secundário gera cópia distribuída `DistCP` () computação.

1. O cluster secundário copia dados do armazenamento do cluster primário.  

1. A metásta no cluster secundário é atualizada.  

1. O último ID do evento rastreado está armazenado na metastore primária.

A replicação incremental segue o mesmo processo, e requer o último ID do evento replicado como entrada. Isto leva a uma cópia incremental desde o último evento de replicação. As replicações incrementais são normalmente automatizadas com uma frequência pré-determinada para atingir os objetivos necessários do ponto de recuperação (RPO).

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Diagrama de replicação da colmeia":::

## <a name="replication-patterns"></a>Padrões de replicação  

A replicação é normalmente configurada de uma forma unidirecional entre o primário e o secundário, onde o principal atende a ler e escrever pedidos. O agrupamento secundário serve apenas para ler pedidos. As gravações são permitidas no secundário se houver um desastre, mas a replicação inversa precisa de ser configurada de volta para as primárias.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Padrão de replicação da colmeia":::

Existem muitos padrões adequados para a replicação da Colmeia, incluindo Primário – Secundário, Hub e Falado, e Retransmissor.

Em HDInsight Ative Primary – Standby Secondary é um padrão comum de continuidade de negócios e recuperação de desastres (BCDR) e HiveReplicationV2 pode usar este padrão com clusters de Hadoop HDInsight separados regionalmente com olhando VNet. Uma máquina virtual comum espreitada para ambos os clusters pode ser usada para hospedar os scripts de automatização de replicação. Para obter mais informações sobre possíveis padrões BCDR HDInsight, consulte a [documentação de continuidade do negócio HDInsight](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Replicação da colmeia com pacote de segurança empresarial  

Nos casos em que a replicação da Hive é planeada em clusters HDInsight Hadoop com Pacote de Segurança Empresarial, você tem que ter em conta mecanismos de replicação para ranger metatore e Azure Ative Directory Domain Services (AD DS).  

Utilize a funcionalidade de conjuntos de réplicas Azure AD DS para criar mais de uma réplica Azure AD set por inquilino Azure AD em várias regiões. Cada conjunto de réplicas individuais precisa de ser espreitado com VNets HDInsight nas respetivas regiões. Nesta configuração, são aplicadas alterações ao Azure AD DS, incluindo configuração, identidade do utilizador e credenciais, grupos, objetos de política de grupo, objetos de computador e outras alterações a todos os conjuntos de réplicas no domínio gerido utilizando a replicação Azure AD DS.
  
As políticas dos Rangers podem ser periodicamente apoiadas e replicadas desde o primário ao secundário usando a funcionalidade de Import-Export Ranger. Pode optar por replicar todas ou um subconjunto de políticas ranger dependendo do nível de autorizações que procura implementar no cluster secundário.  

## <a name="sample-code"></a>Código de exemplo  

A seguinte sequência de código fornece um exemplo de como a colocação de botas e a replicação incremental podem ser implementadas numa tabela de amostras chamada `tpcds_orc` .  

1. Coloque a mesa como fonte para uma política de replicação.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Despejo de botas no aglomerado primário.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Exemplo de saída:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Carga de botas no aglomerado secundário. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Verifique o `REPL` estado no agrupamento secundário.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Despejo incremental no aglomerado primário.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Exemplo de saída:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Carga incremental no cluster secundário.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Verifique `REPL` o estado no cluster secundário.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

- [Continuidade do negócio Azure HDInsight](../hdinsight-business-continuity.md)
- [Azure HDInsight arquiteturas de continuidade de negócios](../hdinsight-business-continuity-architecture.md)
- [Azure HDInsight estudo de caso de arquitetura de solução altamente disponível](../hdinsight-high-availability-case-study.md)
- [O que é Apache Hive e HiveQL em Azure HDInsight?](../hadoop/hdinsight-use-hive.md)