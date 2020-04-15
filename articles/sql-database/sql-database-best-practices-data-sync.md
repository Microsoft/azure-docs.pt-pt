---
title: Melhores práticas para a Sincronização de Dados
description: Conheça as melhores práticas para configurar e executar o Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 2b72d52463164c2a059fce316cc11a63aad62e7c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380936"
---
# <a name="best-practices-for-sql-data-sync"></a>Melhores práticas para a Sincronização de Dados SQL 

Este artigo descreve as melhores práticas para o Azure SQL Data Sync.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure SQL Data Sync **não** suporta a Instância Gerida pela Base de Dados Azure SQL neste momento.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Segurança e fiabilidade

### <a name="client-agent"></a>Agente cliente

-   Instale o agente cliente utilizando a conta de utilizador menos privilegiada que tem acesso ao serviço de rede.  
-   Instale o agente cliente num computador que não seja o computador SQL Server no local.  
-   Não registe uma base de dados no local com mais de um agente.    
    -   Evite isto mesmo que esteja a sincronizar diferentes tabelas para diferentes grupos de sincronização.  
    -   Registar uma base de dados no local com vários agentes de clientes coloca desafios quando elimina um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privileges"></a>Contas de base de dados com privilégios menos necessários

-   **Para a configuração de sincronização.** Criar/Alterar Tabela; Alterar Base de Dados; Criar procedimento; Selecione/ Alter Schema; Criar tipo definido pelo utilizador.

-   **Para sincronização contínua.** Selecione/ Insira/ Atualize/ Elimine em tabelas selecionadas para sincronização e em metadados sincronizados e tabelas de rastreio; Executar a permissão em procedimentos armazenados criados pelo serviço; Executar permissão em tipos de tabela definidos pelo utilizador.

-   **Para a deprovisionamento.** Alterar em tabelas parte da sincronização; Selecione/Elimine em tabelas de metadados sincronizados; Controlo em tabelas de rastreio de sincronização, procedimentos armazenados e tipos definidos pelo utilizador.

A Base de Dados Azure SQL suporta apenas um único conjunto de credenciais. Para realizar estas tarefas dentro desta restrição, considere as seguintes opções:

-   Alterar as credenciais para diferentes fases (por exemplo, *credenciais1* para configuração e *credenciais2* para o curso).  
-   Alterar a permissão das credenciais (isto é, alterar a permissão após a configuração da sincronização).

## <a name="setup"></a>Configuração

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Considerações e constrangimentos da base de dados

#### <a name="sql-database-instance-size"></a>Tamanho da instância de base de dados SQL

Quando criar uma nova instância de Base de Dados SQL, detete o tamanho máximo para que seja sempre maior do que a base de dados que implementa. Se não definir o tamanho máximo para maior do que a base de dados implementada, a sincronização falha. Embora o SQL Data Sync não ofereça crescimento automático, pode executar o `ALTER DATABASE` comando para aumentar o tamanho da base de dados depois de ter sido criada. Certifique-se de que se mantém dentro dos limites de tamanho da instância da Base de Dados SQL.

> [!IMPORTANT]
> O SQL Data Sync armazena metadados adicionais em cada base de dados. Certifique-se de que contabiliza estes metadados quando calcular o espaço necessário. A quantidade de despesas adicionais está relacionada com a largura das tabelas (por exemplo, as mesas estreitas requerem mais sobrecarga) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>Considerações e constrangimentos de tabela

#### <a name="selecting-tables"></a>Tabelas de seleção

Não é preciso incluir todas as mesas que estão numa base de dados num grupo de sincronização. As tabelas que você inclui em um grupo de sincronização afetam eficiência e custos. Inclua tabelas, e as tabelas de que dependem, num grupo de sincronização apenas se as necessidades das empresas o exigirem.

#### <a name="primary-keys"></a>Chaves primárias

Cada mesa de um grupo de sincronização deve ter uma chave primária. O serviço SQL Data Sync não pode sincronizar uma tabela que não tenha uma chave primária.

Antes de utilizar o SQL Data Sync em produção, teste o desempenho inicial e contínuo da sincronização.

#### <a name="empty-tables-provide-the-best-performance"></a>As mesas vazias proporcionam o melhor desempenho

As mesas vazias proporcionam o melhor desempenho no momento da inicialização. Se a tabela alvo estiver vazia, o Data Sync utiliza a inserção a granel para carregar os dados. Caso contrário, o Data Sync faz uma comparação e inserção consecutivas para verificar se existem conflitos. Se o desempenho não for uma preocupação, no entanto, pode configurar sincronização entre tabelas que já contêm dados.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Basede dados de destino de provisionamento

O SQL Data Sync fornece o fornecimento básico de bases de dados.

Esta secção discute as limitações do provisionamento no SQL Data Sync.

#### <a name="autoprovisioning-limitations"></a>Limitações de fornecimento automático

O SQL Data Sync tem as seguintes limitações para o fornecimento automático:

-   Selecione apenas as colunas que são criadas na tabela de destino. Quaisquer colunas que não façam parte do grupo de sincronização não estão aprovisionadas nas tabelas de destino.
-   Os índices são criados apenas para colunas selecionadas. Se o índice de tabela de origem tem colunas que não fazem parte do grupo de sincronização, esses índices não estão aprovisionados nas tabelas de destino.  
-   Os índices nas colunas do tipo XML não são provisionados.  
-   As restrições de verificação não são previstas.  
-   Os gatilhos existentes nas tabelas de origem não estão provisionados.  
-   As vistas e os procedimentos armazenados não são criados na base de dados de destino.
-   ATUALIZAÇÃO AS ações CASCADE e ON DELETE CASCADE sobre restrições de chaves estrangeiras não são recriadas nas tabelas de destino.
-   Se tiver colunas decimais ou numéricas com uma precisão superior a 28, o SQL Data Sync pode encontrar um problema de transbordo de conversão durante a sincronização. Recomendamos que limite a precisão das colunas decimais ou numéricas a 28 ou menos.

#### <a name="recommendations"></a>Recomendações

-   Utilize a capacidade de auto-fornecimento de sincronização de dados SQL apenas quando estiver a experimentar o serviço.  
-   Para produção, forme o esquema da base de dados.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>Onde localizar a base de dados do hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário de empresa para nuvem

Para minimizar a latência, mantenha a base de dados do hub perto da maior concentração do tráfego de base de dados do grupo sync.

#### <a name="cloud-to-cloud-scenario"></a>Cenário cloud-to-cloud

-   Quando todas as bases de dados de um grupo de sincronização estão num centro de dados, o centro deve estar localizado no mesmo datacenter. Esta configuração reduz a latência e o custo da transferência de dados entre datacenters.
-   Quando as bases de dados de um grupo de sincronização estão em vários centros de dados, o centro deve estar localizado no mesmo datacenter que a maioria das bases de dados e tráfego de bases de dados.

#### <a name="mixed-scenarios"></a>Cenários mistos

Aplique as diretrizes anteriores a configurações complexas de grupos de sincronização, como as que são uma mistura de cenários de empresa-a-nuvem e nuvem-a-nuvem.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>Evite sincronização inicial lenta e dispendiosa

Nesta secção, discutimos a sincronização inicial de um grupo de sincronização. Aprenda a ajudar a evitar que uma sincronização inicial demore mais tempo e seja mais dispendiosa do que o necessário.

#### <a name="how-initial-sync-works"></a>Como funciona a sincronização inicial

Quando criar um grupo de sincronização, comece com dados numa única base de dados. Se tiver dados em várias bases de dados, o SQL Data Sync trata cada linha como um conflito que precisa de ser resolvido. Esta resolução de conflitos faz com que a sincronização inicial avance lentamente. Se tiver dados em várias bases de dados, a sincronização inicial pode demorar entre vários dias e vários meses, dependendo do tamanho da base de dados.

Se as bases de dados estiverem em diferentes datacenters, cada linha deve viajar entre os diferentes datacenters. Isto aumenta o custo de uma sincronização inicial.

#### <a name="recommendation"></a>Recomendação

Se possível, comece com dados numa única base de dados do grupo sync.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>Design para evitar laços de sincronização

Um laço de sincronização ocorre quando há referências circulares dentro de um grupo de sincronização. Nesse cenário, cada alteração numa base de dados é infinita mente e circularmente replicada através das bases de dados do grupo de sincronização.   

Certifique-se de que evita os ciclos de sincronização, pois causam degradação do desempenho e podem aumentar significativamente os custos.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>Alterações que não se propaguem

#### <a name="reasons-that-changes-fail-to-propagate"></a>Razões que alteram não se propagam

As alterações podem não propagar-se por uma das seguintes razões:

-   Incompatibilidade schema/datatype.
-   Inserção de nulos em colunas não nulas.
-   Violando restrições de chaves estrangeiras.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando as mudanças não se propagam?

-   O grupo Sync mostra que está em estado de **alerta.**
-   Os detalhes estão listados no portal UI log viewer.
-   Se o problema não for resolvido durante 45 dias, a base de dados fica desatualizada.

> [!NOTE]
> Estas mudanças nunca se propagam. A única forma de recuperar neste cenário é recriar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendação

Monitorize regularmente o grupo de sincronização e a saúde da base de dados através do portal e da interface de registo.


## <a name="maintenance"></a>Manutenção

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Evite bases de dados desatualizadas e grupos de sincronização

Um grupo de sincronização ou uma base de dados num grupo de sincronização podem ficar desatualizados. Quando o estado de um grupo de sincronização está **desatualizado,** deixa de funcionar. Quando o estado de uma base de dados está **desatualizado,** os dados podem perder-se. É melhor evitar este cenário em vez de tentar recuperar dele.

#### <a name="avoid-out-of-date-databases"></a>Evite bases de dados desatualizadas

O estado de uma base de dados está definido para **desatualizado** quando está offline por 45 dias ou mais. Para evitar um estado desatualizado numa base de dados, **certifique-se** de que nenhuma das bases de dados está offline durante 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evite grupos de sincronização desatualizados

O estado de um grupo de sincronização é definido para **desatualizado** quando qualquer alteração no grupo de sincronização não se propague para o resto do grupo de sincronização durante 45 dias ou mais. Para evitar um estado **desatualizado** num grupo de sincronização, verifique regularmente o registo histórico do grupo sync. Certifique-se de que todos os conflitos são resolvidos e que as alterações são propagadas com sucesso através das bases de dados do grupo sync.

Um grupo de sincronização pode não aplicar uma alteração por uma destas razões:

-   Incompatibilidade de schema entre as mesas.
-   Incompatibilidade de dados entre tabelas.
-   Inserir uma linha com um valor nulo numa coluna que não permita valores nulos.
-   Atualizar uma linha com um valor que viola uma restrição de chave estrangeira.

Para evitar grupos de sincronização desatualizados:

-   Atualize o esquema para permitir os valores contidos nas linhas falhadas.
-   Atualize os valores-chave estrangeiros para incluir os valores contidos nas linhas falhadas.
-   Atualize os valores de dados na linha falhada para que sejam compatíveis com o esquema ou chaves estrangeiras na base de dados-alvo.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>Evite questões de desprovisionamento

Em algumas circunstâncias, o desregisto de uma base de dados com um agente cliente pode causar falhas na sincronização.

#### <a name="scenario"></a>Cenário

1. O grupo Sync A foi criado utilizando uma instância de Base de Dados SQL e uma base de dados do SQL Server no local, que está associada ao agente local 1.
2. A mesma base de dados no local está registada com o agente local 2 (este agente não está associado a nenhum grupo de sincronização).
3. O desregisto da base de dados no local do agente local 2 remove as tabelas de rastreio e meta para o grupo de sincronização A para a base de dados no local.
4. As operações do grupo Sync A falham, com este erro: "A operação atual não pôde ser concluída porque a base de dados não está prevista para sincronização ou não tem permissões para as tabelas de configuração de sincronização."

#### <a name="solution"></a>Solução

Para evitar este cenário, não registe uma base de dados com mais de um agente.

Para recuperar deste cenário:

1. Retire a base de dados de cada grupo de sincronização a que pertence.  
2. Adicione a base de dados de volta em cada grupo de sincronização do que a removeu.  
3. Implementar cada grupo de sincronização afetado (esta ação prevê a base de dados).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Modificar um grupo de sincronização

Não tente remover uma base de dados de um grupo de sincronização e depois editar o grupo de sincronização sem primeiro implementar uma das alterações.

Em vez disso, primeiro remova uma base de dados de um grupo de sincronização. Em seguida, implante a mudança e aguarde a disposição para terminar. Quando a desprovisionamento estiver concluída, pode editar o grupo de sincronização e implementar as alterações.

Se tentar remover uma base de dados e depois editar um grupo de sincronização sem primeiro implementar uma das alterações, uma ou outra operação falha. A interface do portal pode tornar-se inconsistente. Se isso acontecer, refresque a página para restaurar o estado correto.

### <a name="avoid-schema-refresh-timeout"></a>Evite o tempo de atualização do esquema

Se tiver um esquema complexo para sincronizar, poderá encontrar um "tempo limite de funcionamento" durante uma atualização de esquemas se a base de dados de metadados sincronizado tiver um SKU inferior (exemplo: básico). 

#### <a name="solution"></a>Solução

Para mitigar este problema, por favor, esforce a sua base de dados de metadados de sincronização para ter um SKU mais elevado, como o S3. 

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o SQL Data Sync, consulte:

-   Visão geral - Sync dados em várias bases de dados de nuvem e no local com O Sincronizado de [Dados Azure SQL](sql-database-sync-data.md)
-   Configurar o Sincronizado de Dados
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o Servidor SQL no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](sql-database-sync-monitor-oms.md)
-   Troubleshoot - [Problemas com O Sincronizado de Dados Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação de alterações de esquemas no Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Com powerShell - [Use powerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para mais informações sobre a Base de Dados SQL, consulte:

-   [Visão geral da base de dados SQL](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
