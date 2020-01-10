---
title: Melhores práticas para a Sincronização de Dados
description: Saiba mais sobre as práticas recomendadas para configurar e executar Sincronização de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: ee929fa227cb105b73bc929c13a768aabef37ce3
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771688"
---
# <a name="best-practices-for-sql-data-sync"></a>Melhores práticas para a Sincronização de Dados SQL 

Este artigo descreve as práticas recomendadas para o Azure Sincronização de Dados SQL.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL **não oferece suporte a** instância gerenciada do banco de dados SQL do Azure no momento.

## <a name="security-and-reliability"></a>Segurança e confiabilidade

### <a name="client-agent"></a>Agente cliente

-   Instale o agente cliente usando a conta de usuário com privilégios mínimos que tem acesso ao serviço de rede.  
-   Instale o agente cliente em um computador que não seja o computador SQL Server local.  
-   Não registre um banco de dados local com mais de um agente.    
    -   Evite isso mesmo se você estiver sincronizando tabelas diferentes para grupos de sincronização diferentes.  
    -   O registro de um banco de dados local com vários agentes cliente apresenta desafios quando você exclui um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privileges"></a>Contas de banco de dados com privilégios mínimos necessários

-   **Para a configuração de sincronização**. Criar/alterar tabela; ALTER DATABASE; Criar procedimento; Selecionar/Alterar esquema; Criar tipo definido pelo usuário.

-   **Para sincronização em andamento**. Selecionar/inserir/atualizar/excluir em tabelas selecionadas para sincronização e em metadados de sincronização e tabelas de acompanhamento; Permissão EXECUTE em procedimentos armazenados criados pelo serviço; Permissão EXECUTE em tipos de tabela definidos pelo usuário.

-   **Para desprovisionamento**. Alterar na parte das tabelas da sincronização; Selecionar/Excluir em tabelas de metadados de sincronização; Controle sobre tabelas de acompanhamento de sincronização, procedimentos armazenados e tipos definidos pelo usuário.

O banco de dados SQL do Azure dá suporte apenas a um único conjunto de credenciais. Para realizar essas tarefas nessa restrição, considere as seguintes opções:

-   Altere as credenciais para diferentes fases (por exemplo, *credentials1* para instalação e *credentials2* para contínuo).  
-   Altere a permissão das credenciais (ou seja, altere a permissão depois que a sincronização estiver configurada).

## <a name="setup"></a>Configuração

### <a name="database-considerations-and-constraints"></a>Considerações e restrições de banco de dados

#### <a name="sql-database-instance-size"></a>Tamanho da instância do banco de dados SQL

Ao criar uma nova instância do banco de dados SQL, defina o tamanho máximo para que seja sempre maior do que o banco de dados implantado. Se você não definir o tamanho máximo para maior que o banco de dados implantado, a sincronização falhará. Embora Sincronização de Dados SQL não ofereça o crescimento automático, você pode executar o comando `ALTER DATABASE` para aumentar o tamanho do banco de dados depois que ele tiver sido criado. Certifique-se de permanecer dentro dos limites de tamanho da instância do banco de dados SQL.

> [!IMPORTANT]
> Sincronização de Dados SQL armazena metadados adicionais com cada banco de dados. Certifique-se de que você conta esses metadados ao calcular o espaço necessário. A quantidade de sobrecarga adicionada está relacionada à largura das tabelas (por exemplo, tabelas estreitas exigem mais sobrecarga) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a>Considerações e restrições de tabela

#### <a name="selecting-tables"></a>Selecionando tabelas

Você não precisa incluir todas as tabelas que estão em um banco de dados em um grupo de sincronização. As tabelas que você inclui em um grupo de sincronização afetam a eficiência e os custos. Inclua tabelas e as tabelas das quais elas dependem, em um grupo de sincronização somente se as necessidades comerciais exigirem.

#### <a name="primary-keys"></a>Chaves primárias

Cada tabela em um grupo de sincronização deve ter uma chave primária. O serviço de Sincronização de Dados SQL não pode sincronizar uma tabela que não tem uma chave primária.

Antes de usar Sincronização de Dados SQL em produção, teste o desempenho de sincronização inicial e em andamento.

#### <a name="empty-tables-provide-the-best-performance"></a>Tabelas vazias fornecem o melhor desempenho

Tabelas vazias fornecem o melhor desempenho no momento da inicialização. Se a tabela de destino estiver vazia, a sincronização de dados usará a inserção em massa para carregar os dados. Caso contrário, a sincronização de dados faz uma comparação de linha por linha e uma inserção para verificar se há conflitos. No entanto, se o desempenho não for uma preocupação, você poderá configurar a sincronização entre as tabelas que já contêm dados.

### <a name="provisioning-destination-databases"></a>Provisionando bancos de dados de destino

O Sincronização de Dados SQL fornece provisionamento automático de banco de dados básico.

Esta seção discute as limitações do provisionamento no Sincronização de Dados SQL.

#### <a name="autoprovisioning-limitations"></a>Limitações de provisionamento automático

Sincronização de Dados SQL tem as seguintes limitações para o provisionamento automático:

-   Selecione apenas as colunas que são criadas na tabela de destino. As colunas que não fazem parte do grupo de sincronização não são provisionadas nas tabelas de destino.
-   Os índices são criados somente para as colunas selecionadas. Se o índice da tabela de origem tiver colunas que não fazem parte do grupo de sincronização, esses índices não serão provisionados nas tabelas de destino.  
-   Índices em colunas de tipo XML não são provisionados.  
-   Restrições de verificação não são provisionadas.  
-   Os gatilhos existentes nas tabelas de origem não são provisionados.  
-   Exibições e procedimentos armazenados não são criados no banco de dados de destino.
-   EM atualizar em cascata e em excluir ações em cascata em restrições de chave estrangeira não são recriadas nas tabelas de destino.
-   Se você tiver colunas decimais ou numéricas com uma precisão maior que 28, Sincronização de Dados SQL poderá encontrar um problema de estouro de conversão durante a sincronização. Recomendamos que você limite a precisão de colunas decimais ou numéricas para 28 ou menos.

#### <a name="recommendations"></a>Recomendações

-   Use o Sincronização de Dados SQL recurso de provisionamento automático somente quando estiver experimentando o serviço.  
-   Para produção, provisione o esquema de banco de dados.

### <a name="locate-hub"></a>Onde localizar o banco de dados de Hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário de empresa para nuvem

Para minimizar a latência, mantenha o banco de dados Hub próximo à maior concentração do tráfego de banco de dados do grupo de sincronização.

#### <a name="cloud-to-cloud-scenario"></a>Cenário de nuvem para nuvem

-   Quando todos os bancos de dados em um grupo de sincronização estiverem em um datacenter, o Hub deverá estar localizado no mesmo datacenter. Essa configuração reduz a latência e o custo da transferência de dados entre data centers.
-   Quando os bancos de dados em um grupo de sincronização estiverem em vários data centers, o Hub deverá estar localizado no mesmo datacenter que a maioria dos bancos de dados e do tráfego de banco de dados.

#### <a name="mixed-scenarios"></a>Cenários mistos

Aplique as diretrizes anteriores a configurações de grupo de sincronização complexas, como aquelas que são uma combinação de cenários de empresa para nuvem e nuvem para nuvem.

## <a name="sync"></a>Sincronizar

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Evitar sincronização inicial lenta e dispendiosa

Nesta seção, discutiremos a sincronização inicial de um grupo de sincronização. Saiba como ajudar a impedir que uma sincronização inicial seja mais demorada e com mais custo do que o necessário.

#### <a name="how-initial-sync-works"></a>Como a sincronização inicial funciona

Ao criar um grupo de sincronização, comece com os dados em apenas um banco de dado. Se você tiver dados em vários bancos, Sincronização de Dados SQL tratará cada linha como um conflito que precisa ser resolvido. Essa resolução de conflitos faz com que a sincronização inicial fique lenta. Se você tiver dados em vários bancos, a sincronização inicial poderá demorar entre vários dias e vários meses, dependendo do tamanho do banco de dado.

Se os bancos de dados estiverem em data centers diferentes, cada linha deverá viajar entre os diferentes data centers. Isso aumenta o custo de uma sincronização inicial.

#### <a name="recommendation"></a>Recomendação

Se possível, comece com os dados em apenas um dos bancos de dado do grupo de sincronização.

### <a name="design-to-avoid-synchronization-loops"></a>Design para evitar loops de sincronização

Um loop de sincronização ocorre quando há referências circulares dentro de um grupo de sincronização. Nesse cenário, cada alteração em um banco de dados é reproduzida de forma menos e circular por meio dos bancos de dados no grupo de sincronização.   

Certifique-se de evitar loops de sincronização, pois eles causam degradação de desempenho e podem aumentar significativamente os custos.

### <a name="handling-changes-that-fail-to-propagate"></a>Alterações que falham na propagação

#### <a name="reasons-that-changes-fail-to-propagate"></a>Motivos pelos quais as alterações falham ao se propagar

As alterações podem falhar ao se propagar por um dos seguintes motivos:

-   Incompatibilidade de esquema/tipo de dados.
-   Inserindo nulo em colunas não anuláveis.
-   Violando restrições Foreign Key.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando as alterações falham ao se propagar?

-   O grupo de sincronização mostra que ele está em um estado de **aviso** .
-   Os detalhes são listados no Visualizador de log da interface do usuário do Portal.
-   Se o problema não for resolvido por 45 dias, o banco de dados ficará desatualizado.

> [!NOTE]
> Essas alterações nunca se propagam. A única maneira de se recuperar nesse cenário é recriar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendação

Monitore o grupo de sincronização e a integridade do banco de dados regularmente por meio do portal e da interface de log.


## <a name="maintenance"></a>Manutenção

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Evitar bancos de dados desatualizados e grupos de sincronização

Um grupo de sincronização ou um banco de dados em um grupo de sincronização pode ficar desatualizado. Quando o status de um grupo de sincronização está desatualizado, ele para **de**funcionar. Quando o status de um banco **de**dados estiver desatualizado, os dados poderão ser perdidos. É melhor evitar esse cenário em vez de tentar recuperá-lo.

#### <a name="avoid-out-of-date-databases"></a>Evitar bancos de dados desatualizados

O status de um banco de dados é **definido como desatualizado quando** ele ficou offline por 45 dias ou mais. Para evitar um status desatualizado em um **banco de dados** , certifique-se de que nenhum deles esteja offline por 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar grupos de sincronização desatualizados

O **status de um** grupo de sincronização é definido como desatualizado quando qualquer alteração no grupo de sincronização não é propagada para o restante do grupo de sincronização por 45 dias ou mais. Para evitar um status desatualizado em um grupo **de** sincronização, verifique regularmente o log do histórico do grupo de sincronização. Verifique se todos os conflitos foram resolvidos e se as alterações foram propagadas com êxito em todos os bancos de dados do grupo de sincronização.

Um grupo de sincronização pode falhar ao aplicar uma alteração por um destes motivos:

-   Incompatibilidade de esquema entre tabelas.
-   Incompatibilidade de dados entre tabelas.
-   Inserindo uma linha com um valor nulo em uma coluna que não permite valores nulos.
-   Atualizando uma linha com um valor que viola uma restrição FOREIGN KEY.

Para evitar grupos de sincronização desatualizados:

-   Atualize o esquema para permitir os valores contidos nas linhas com falha.
-   Atualize os valores de chave estrangeira para incluir os valores contidos nas linhas com falha.
-   Atualize os valores de dados na linha com falha para que eles sejam compatíveis com o esquema ou as chaves estrangeiras no banco de dados de destino.

### <a name="avoid-deprovisioning-issues"></a>Evitar problemas de desprovisionamento

Em algumas circunstâncias, o cancelamento do registro de um banco de dados com um agente cliente pode causar falha na sincronização.

#### <a name="scenario"></a>Cenário

1. O grupo de sincronização A foi criado usando uma instância do banco de dados SQL e um banco de dados SQL Server local, que está associado ao agente local 1.
2. O mesmo banco de dados no local é registrado com o agente local 2 (esse agente não está associado a nenhum grupo de sincronização).
3. O cancelamento do registro do banco de dados local do agente 2 remove as tabelas de acompanhamento e de meta do grupo de sincronização A para o banco de dados local.
4. Falha nas operações do grupo de sincronização A, com este erro: "a operação atual não pôde ser concluída porque o banco de dados não está provisionado para sincronização ou você não tem permissões para as tabelas de configuração de sincronização".

#### <a name="solution"></a>Solução

Para evitar esse cenário, não registre um banco de dados com mais de um agente.

Para se recuperar deste cenário:

1. Remova o banco de dados de cada grupo de sincronização ao qual ele pertence.  
2. Adicione o banco de dados de volta em cada grupo de sincronização do qual você o removeu.  
3. Implante cada grupo de sincronização afetado (essa ação provisiona o banco de dados).  

### <a name="modifying-your-sync-group"></a>Modificando um grupo de sincronização

Não tente remover um banco de dados de um grupo de sincronização e edite o grupo de sincronização sem primeiro implantar uma das alterações.

Em vez disso, primeiro remova um banco de dados de um grupo de sincronização. Em seguida, implante a alteração e aguarde a conclusão do desprovisionamento. Quando o desprovisionamento for concluído, você poderá editar o grupo de sincronização e implantar as alterações.

Se você tentar remover um banco de dados e editar um grupo de sincronização sem primeiro implantar uma das alterações, uma ou outra operação falhará. A interface do portal pode se tornar inconsistente. Se isso acontecer, atualize a página para restaurar o estado correto.

### <a name="avoid-schema-refresh-timeout"></a>Evitar tempo limite de atualização do esquema

Se você tiver um esquema complexo para sincronizar, poderá encontrar um "tempo limite de operação" durante uma atualização de esquema se o banco de dados de metadados de sincronização tiver um SKU inferior (exemplo: básico). 

#### <a name="solution"></a>Solução

Para atenuar esse problema, escale verticalmente seu banco de dados de metadados de sincronização para ter uma SKU superior, como S3. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre Sincronização de Dados SQL, consulte:

-   Visão geral – [sincronizar dados entre vários bancos de dados locais e de nuvem com o Azure sincronização de dados SQL](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal- [tutorial: configurar sincronização de dados SQL para sincronizar os dados entre o Azure SQL Database e o SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](sql-database-data-sync-agent.md)
-   Monitorar [sincronização de dados SQL monitorar com Azure monitor logs](sql-database-sync-monitor-oms.md)
-   Solucionar problemas- [solucionar problema com o Azure sincronização de dados SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL – [Automatize a replicação de alterações de esquema no Azure sincronização de dados SQL](sql-database-update-sync-schema.md)
    -   Com o PowerShell- [use o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre o banco de dados SQL, consulte:

-   [Visão geral do banco de dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento do ciclo de vida do banco](https://msdn.microsoft.com/library/jj907294.aspx)
