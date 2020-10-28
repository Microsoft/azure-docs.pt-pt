---
title: Melhores práticas da Sincronização de Dados SQL do Azure
description: Saiba mais sobre as melhores práticas para configurar e executar o Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 59e28e4a3d630aac0954802e8777058c00261006
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791448"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Melhores práticas da Sincronização de Dados SQL do Azure 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve as melhores práticas para Azure SQL Data Sync.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O Azure SQL Data Sync **não** suporta a Azure SQL Managed Instance neste momento.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> Segurança e fiabilidade

### <a name="client-agent"></a>Agente cliente

-   Instale o agente cliente utilizando a conta de utilizador menos privilegiada que tenha acesso ao serviço de rede.  
-   Instale o agente cliente num computador que não seja o computador SQL Server.  
-   Não registe uma base de dados no local com mais de um agente.    
    -   Evite isto mesmo que esteja a sincronizar diferentes tabelas para diferentes grupos de sincronização.  
    -   Registar uma base de dados no local com vários agentes clientes coloca desafios quando elimina um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privileges"></a>Contas de base de dados com privilégios menos necessários

-   **Para a configuração da sincronização** . Criar/Alterar Tabela; Alterar Base de Dados; Criar Procedimento; Selecione/ Alter Schema; Criar User-Defined Tipo.

-   **Para sincronização em curso** . Selecione/ Insira/Atualização/ Elimine as tabelas selecionadas para sincronização e em metadados de sincronização e tabelas de rastreio; Executar permissão sobre procedimentos armazenados criados pelo serviço; Execute a permissão nos tipos de tabela definidos pelo utilizador.

-   **Para desprovisionamento** . Alterar nas tabelas parte da sincronização; Selecione/ Elimine em tabelas de metadados sincronizados; Controlo de tabelas de rastreio de sincronização, procedimentos armazenados e tipos definidos pelo utilizador.

A Azure SQL Database suporta apenas um único conjunto de credenciais. Para realizar estas tarefas dentro deste constrangimento, considere as seguintes opções:

-   Altere as credenciais para diferentes fases (por exemplo, *credenciais1* para configuração e *credenciais2* para em curso).  
-   Alterar a permissão das credenciais (isto é, alterar a permissão após a configuração da sincronização).

## <a name="setup"></a>Configuração

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> Considerações e constrangimentos da base de dados

#### <a name="database-size"></a>Tamanho da base de dados

Quando criar uma nova base de dados, desa ajuste o tamanho máximo para que seja sempre maior do que a base de dados que implementa. Se não definir o tamanho máximo para maior do que a base de dados implantada, a sincronização falha. Embora o SQL Data Sync não ofereça crescimento automático, pode executar o `ALTER DATABASE` comando para aumentar o tamanho da base de dados depois de ter sido criado. Certifique-se de que se mantém dentro dos limites de tamanho da base de dados.

> [!IMPORTANT]
> SQL Data Sync armazena metadados adicionais em cada base de dados. Certifique-se de que contabiliza estes metadados quando calcular o espaço necessário. A quantidade de sobrecarga adicionada está relacionada com a largura das tabelas (por exemplo, tabelas estreitas requerem mais sobrecarga) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> Considerações e constrangimentos da tabela

#### <a name="selecting-tables"></a>Selecionar tabelas

Não tens de incluir todas as tabelas que estão numa base de dados num grupo de sincronização. As tabelas que inclui num grupo de sincronização afetam a eficiência e os custos. Inclua tabelas, e as tabelas de que dependem, num grupo de sincronização apenas se as necessidades do negócio o exigirem.

#### <a name="primary-keys"></a>Chaves primárias

Cada tabela de um grupo de sincronização deve ter uma chave primária. O SQL Data Sync não consegue sincronizar uma tabela que não tenha uma chave primária.

Antes de utilizar o SQL Data Sync na produção, teste o desempenho inicial e o desempenho da sincronização em curso.

#### <a name="empty-tables-provide-the-best-performance"></a>Mesas vazias proporcionam o melhor desempenho

As mesas vazias proporcionam o melhor desempenho no tempo de inicialização. Se a tabela alvo estiver vazia, o Data Sync utiliza o inserível a granel para carregar os dados. Caso contrário, o Data Sync faz uma comparação e inserção de linha a linha para verificar se existem conflitos. No entanto, se o desempenho não for uma preocupação, pode configurar uma sincronização entre tabelas que já contêm dados.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> Provisão de bases de dados de destino

O SQL Data Sync fornece autoprovisionamento básico da base de dados.

Esta secção discute as limitações do provisionamento no SQL Data Sync.

#### <a name="autoprovisioning-limitations"></a>Limitações de autoprovisionamento

O SQL Data Sync tem as seguintes limitações para a autoprovisionamento:

-   Selecione apenas as colunas que são criadas na tabela de destino. Quaisquer colunas que não fazem parte do grupo de sincronização não estão aparentados nas tabelas de destino.
-   Os índices são criados apenas para colunas selecionadas. Se o índice da tabela de origem tiver colunas que não fazem parte do grupo de sincronização, esses índices não são a provisionados nas tabelas de destino.  
-   Os índices nas colunas do tipo XML não são a provisionados.  
-   Os constrangimentos de VERIFICAÇÃO não são a provisionados.  
-   Os gatilhos existentes nas tabelas de origem não são a provisionados.  
-   As vistas e os procedimentos armazenados não são criados na base de dados de destino.
-   EM ATUALIZAÇÃO AS ações CASCADE e ON DELETE CASCADE sobre restrições de chaves estrangeiras não são recriadas nas tabelas de destino.
-   Se tiver colunas decimais ou numéricas com uma precisão superior a 28, o SQL Data Sync pode encontrar um problema de transbordo de conversão durante a sincronização. Recomendamos que limite a precisão das colunas decimais ou numéricas a 28 ou menos.

#### <a name="recommendations"></a>Recomendações

-   Utilize a capacidade de autoprovisionamento do SQL Data Sync apenas quando estiver a experimentar o serviço.  
-   Para a produção, provisão o esquema da base de dados.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> Onde localizar a base de dados do hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário de empresa-nuvem

Para minimizar a latência, mantenha a base de dados do hub perto da maior concentração do tráfego de base de dados do grupo de sincronização.

#### <a name="cloud-to-cloud-scenario"></a>Cenário nuvem-a-nuvem

-   Quando todas as bases de dados de um grupo de sincronização estiverem num centro de dados, o hub deve estar localizado no mesmo datacenter. Esta configuração reduz a latência e o custo da transferência de dados entre datacenters.
-   Quando as bases de dados de um grupo de sincronização estão em vários centros de dados, o hub deve estar localizado no mesmo centro de dados que a maioria das bases de dados e tráfego de bases de dados.

#### <a name="mixed-scenarios"></a>Cenários mistos

Aplique as diretrizes anteriores a configurações complexas do grupo de sincronização, como as que são uma mistura de cenários de empresa-a-nuvem e nuvem-a-nuvem.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evite sincronização inicial lenta e dispendiosa

Nesta secção, discutimos a sincronização inicial de um grupo de sincronização. Aprenda a ajudar a evitar que uma sincronização inicial desapere um tempo mais longo e seja mais dispendiosa do que o necessário.

#### <a name="how-initial-sync-works"></a>Como funciona a sincronização inicial

Quando criar um grupo de sincronização, comece com os dados numa única base de dados. Se tiver dados em várias bases de dados, o SQL Data Sync trata cada linha como um conflito que precisa de ser resolvido. Esta resolução de conflitos faz com que a sincronização inicial vá devagar. Se tiver dados em várias bases de dados, a sincronização inicial pode demorar entre vários dias e vários meses, dependendo do tamanho da base de dados.

Se as bases de dados estiverem em diferentes datacenters, cada linha deve viajar entre os diferentes centros de dados. Isto aumenta o custo de uma sincronização inicial.

#### <a name="recommendation"></a>Recomendação

Se possível, comece com os dados em apenas uma das bases de dados do grupo de sincronização.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> Design para evitar ciclos de sincronização

Ocorre um ciclo de sincronização quando existem referências circulares dentro de um grupo de sincronização. Nesse cenário, cada alteração numa base de dados é replicada de forma interminável e circular através das bases de dados do grupo de sincronização.   

Certifique-se de que evita os ciclos de sincronização, porque provocam uma degradação do desempenho e podem aumentar significativamente os custos.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Alterações que não se propagam

#### <a name="reasons-that-changes-fail-to-propagate"></a>As razões pelas quais as alterações não se propagam

As alterações podem não se propagar por uma das seguintes razões:

-   Incompatibilidade de esquema/tipo de dados.
-   Inserção de nulos em colunas não nulas.
-   Violando constrangimentos de chaves estrangeiras.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando as mudanças não se propagam?

-   O grupo Sync mostra que está num estado de **alerta.**
-   Os detalhes estão listados no portal UI log viewer.
-   Se a questão não for resolvida durante 45 dias, a base de dados fica desatualizada.

> [!NOTE]
> Estas mudanças nunca se propagam. A única maneira de recuperar neste cenário é recriar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendação

Monitorize regularmente a saúde do grupo de sincronização e da base de dados através da interface do portal e do registo.


## <a name="maintenance"></a>Manutenção

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> Evite bases de dados desatualizadas e grupos de sincronização

Um grupo de sincronização ou uma base de dados num grupo de sincronização podem ficar desatualizados. Quando o estado de um grupo de sincronização está **desatualizado,** deixa de funcionar. Quando o estado de uma base de dados está **desatualizado,** os dados podem ser perdidos. É melhor evitar este cenário em vez de tentar recuperar dele.

#### <a name="avoid-out-of-date-databases"></a>Evite bases de dados desatualizadas

O estado de uma base de dados é definido como **desatualizado** quando estiver offline por 45 dias ou mais. Para evitar um **estado desatualizado** numa base de dados, certifique-se de que nenhuma das bases de dados está offline durante 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evite grupos de sincronização desatualizados

O estado de um grupo de sincronização é definido como **desatualizado** quando qualquer alteração no grupo de sincronização não se propagar para o resto do grupo de sincronização durante 45 dias ou mais. Para evitar um **estado desatualizado** num grupo de sincronização, verifique regularmente o registo histórico do grupo de sincronização. Certifique-se de que todos os conflitos são resolvidos e que as alterações são propagadas com sucesso em todas as bases de dados do grupo de sincronização.

Um grupo de sincronização pode não aplicar uma alteração por uma destas razões:

-   Incompatibilidade de esquemas entre as tabelas.
-   Incompatibilidade de dados entre tabelas.
-   Inserir uma linha com um valor nulo numa coluna que não permita valores nulos.
-   Atualizar uma linha com um valor que viola um constrangimento de chave estrangeira.

Para evitar grupos de sincronização desatualizados:

-   Atualize o esquema para permitir os valores contidos nas linhas falhadas.
-   Atualize os valores-chave externos para incluir os valores contidos nas linhas falhadas.
-   Atualize os valores de dados na linha falhada para que sejam compatíveis com o esquema ou as teclas estrangeiras na base de dados-alvo.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> Evitar problemas de desprovisionamento

Em algumas circunstâncias, não registar uma base de dados com um agente cliente pode causar a falha da sincronização.

#### <a name="scenario"></a>Cenário

1. O sync group A foi criado utilizando uma instância SQL Database e uma base de dados SQL Server, que está associada ao agente local 1.
2. A mesma base de dados no local está registada com o agente local 2 (este agente não está associado a nenhum grupo de sincronização).
3. A não registo da base de dados no local do agente local 2 remove as tabelas de rastreio e meta para sincronização do grupo A para a base de dados no local.
4. As operações do grupo A falham, com este erro: "A operação atual não pôde ser concluída porque a base de dados não está prevista para sincronização ou não tem permissões para as tabelas de configuração de sincronização."

#### <a name="solution"></a>Solução

Para evitar este cenário, não registe uma base de dados com mais de um agente.

Para recuperar deste cenário:

1. Remova a base de dados de cada grupo de sincronização a que pertence.  
2. Adicione a base de dados de volta a cada grupo de sincronização de onde a removeu.  
3. Implementar cada grupo de sincronização afetado (esta ação prevê a base de dados).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> Modificar um grupo de sincronização

Não tente remover uma base de dados de um grupo de sincronização e, em seguida, editar o grupo de sincronização sem primeiro implementar uma das alterações.

Em vez disso, primeiro remova uma base de dados de um grupo de sincronização. Em seguida, desloque a mudança e aguarde que a desprovisionamento termine. Quando a desprovisionamento estiver terminada, pode editar o grupo de sincronização e implementar as alterações.

Se tentar remover uma base de dados e, em seguida, editar um grupo de sincronização sem primeiro implementar uma das alterações, uma ou outra operação falha. A interface do portal pode tornar-se inconsistente. Se isso acontecer, refresque a página para restaurar o estado correto.

### <a name="avoid-schema-refresh-timeout"></a>Evite o tempo limite de atualização do esquema

Se tiver um esquema complexo para sincronizar, poderá encontrar um "tempo limite de funcionamento" durante uma atualização de esquema se a base de dados de metadados sincronizado tiver um SKU inferior (exemplo: básico). 

#### <a name="solution"></a>Solução

Para mitigar este problema, por favor, dimensione a sua base de dados de metadados sincronizados para ter um SKU mais elevado, como o S3. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o SQL Data Sync, consulte:

-   Visão geral - [Sync dados em várias bases de dados de nuvem e no local com Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar Sincronização de Dados SQL
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados SQL do Azure e o SqL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados SQL e uma base de dados numa instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](./monitor-tune-overview.md)
-   Resolução de problemas - [Problemas de resolução de problemas com Azure SQL Data Sync](sql-data-sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação de alterações de esquema no Azure SQL Data Sync](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [Use o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

Para obter mais informações sobre a Base de Dados SQL, consulte:

-   [Visão geral da base de dados sql](sql-database-paas-overview.md)
-   [Gestão do ciclo de vida da base de dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))