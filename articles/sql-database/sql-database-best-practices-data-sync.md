---
title: Melhores práticas para a sincronização de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre as melhores práticas para configurar e executar a sincronização de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: c4e0f30f68d778b55e89b956be2c5545041c4e6f
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214610"
---
# <a name="best-practices-for-sql-data-sync"></a>Melhores práticas para a Sincronização de Dados SQL 

Este artigo descreve as melhores práticas para a sincronização de dados SQL do Azure.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Sincronização de dados SQL do Azure faz **não** suporta a instância gerida da base de dados SQL do Azure neste momento.

## <a name="security-and-reliability"></a> Segurança e confiabilidade

### <a name="client-agent"></a>Agente do cliente

-   Instale o agente de cliente utilizando a conta de utilizador com privilégios mínimos, que tem acesso de serviço de rede.  
-   Instale o agente de cliente num computador que não seja o computador do SQL Server no local.  
-   Não se registe uma base de dados no local com mais de um agente.    
    -   Evite isso, mesmo se estiver a sincronizar tabelas diferentes para grupos de sincronização diferentes.  
    -   Registar uma base de dados no local com vários desafios de desafio de agentes de cliente, ao eliminar um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privileges"></a>Contas de base de dados com o mínimo de privilégios necessário

-   **Para a configuração de sincronização**. Criar/alterar tabela; Alterar base de dados; Criar procedimento; Selecionar / alterar esquema; Crie o tipo definido pelo utilizador.

-   **Para sincronização em curso**. Selecionar / inserir / atualizar / eliminar em tabelas que estão selecionadas para sincronização e nos metadados de sincronização e tabelas; de controlo Permissão de execução em procedimentos armazenados criados pelo serviço do; Permissão de execução em tipos de tabela definido pelo utilizador.

-   **Para efetuar o desaprovisionamento**. ALTER na parte de tabelas de sincronização; Selecionar / Eliminar em tabelas de metadados de sincronização; Controlar na sincronização controlo tabelas, procedimentos armazenados e tipos definidos pelo utilizador.

Base de dados SQL do Azure suporta apenas um único conjunto de credenciais. Para realizar essas tarefas nessa restrição, considere as seguintes opções:

-   Alterar as credenciais para diferentes fases (por exemplo, *credentials1* para a configuração e *credentials2* para em curso).  
-   Alterar a permissão das credenciais (ou seja, alterar a permissão depois de configurar a sincronização).

## <a name="setup"></a>Configurar

### <a name="database-considerations-and-constraints"></a> Considerações sobre a base de dados e restrições

#### <a name="sql-database-instance-size"></a>Tamanho da instância de base de dados SQL

Quando cria uma nova instância de base de dados SQL, defina o tamanho máximo, de modo que sempre é maior do que a base de dados a que implementar. Se não definir o tamanho máximo para maior do que a base de dados implementada, a sincronização falha. Embora a sincronização de dados SQL não oferece aumento automático, pode executar o `ALTER DATABASE` comando para aumentar o tamanho da base de dados depois de este ter sido criado. Certifique-se de que permaneça dentro dos limites de tamanho de instância de base de dados SQL.

> [!IMPORTANT]
> Sincronização de dados SQL armazena os metadados adicionais com cada base de dados. Certifique-se de que tem em conta esses metadados ao calcular o espaço necessário. A quantidade de adicionado sobrecarga está relacionado com a largura das tabelas (por exemplo, as tabelas estreitas requerem mais sobrecarga) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a> Considerações de tabela e restrições

#### <a name="selecting-tables"></a>Selecionar tabelas

Não tem de incluir todas as tabelas que estão num banco de dados num grupo de sincronização. As tabelas que incluir num grupo de sincronização afetam a eficiência e os custos. Incluem tabelas e as tabelas são dependentes, num grupo de sincronização apenas se as necessidades empresariais incluírem a ele.

#### <a name="primary-keys"></a>Chaves primárias

Cada tabela num grupo de sincronização tem de ter uma chave primária. O serviço de sincronização de dados SQL não é possível sincronizar uma tabela que não tem uma chave primária.

Antes de utilizar a sincronização de dados SQL em produção, teste o desempenho da sincronização iniciais e contínuas.

#### <a name="empty-tables-provide-the-best-performance"></a>Esvaziar tabelas fornecem o melhor desempenho

Esvaziar tabelas fornecem o melhor desempenho no momento da inicialização. Se a tabela de destino estiver vazia, a sincronização de dados utiliza inserção em massa para carregar os dados. Caso contrário, a sincronização de dados faz uma comparação de linha por linha e a inserção para verificar a existência de conflitos. No entanto, se o desempenho não for uma preocupação, pode definir a sincronização entre as tabelas que já conter dados.

### <a name="provisioning-destination-databases"></a> Aprovisionamento de bases de dados de destino

Sincronização de dados SQL fornece autoprovisioning de base de dados básica.

Esta secção descreve as limitações de aprovisionamento na sincronização de dados SQL.

#### <a name="autoprovisioning-limitations"></a>Limitações de Autoprovisioning

Sincronização de dados SQL tem as seguintes limitações para autoprovisioning:

-   Selecione apenas as colunas que são criadas na tabela de destino. Todas as colunas que não fazem parte do grupo de sincronização não são aprovisionadas nas tabelas de destino.
-   Os índices são criados apenas para colunas selecionadas. Se o índice de tabela de origem tem colunas que não fazem parte do grupo de sincronização, não são aprovisionados esses índices nas tabelas de destino.  
-   Índices em colunas de tipo XML não são aprovisionados.  
-   Restrições de verificação não são aprovisionadas.  
-   Não são aprovisionados existentes acionadores em tabelas de origem.  
-   As vistas e procedimentos armazenados não são criados na base de dados de destino.
-   NO UPDATE CASCADE e CASCADE de eliminar ON ações sobre restrições de chave estrangeira não são recriadas nas tabelas de destino.
-   Se tiver colunas decimais ou numéricas com uma precisão maior que 28, sincronização de dados SQL pode encontrar um problema de capacidade excedida de conversão durante a sincronização. Recomendamos que limite a precisão das colunas decimais ou numéricas para 28 ou menos.

#### <a name="recommendations"></a>Recomendações

-   Utilize a capacidade de autoprovisioning de sincronização de dados SQL apenas quando estiver a experimentar o serviço.  
-   Para a produção, Aprovisione o esquema de banco de dados.

### <a name="locate-hub"></a> Onde localizar a base de dados de hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário empresarial para a cloud

Para minimizar a latência, manter a base de dados de hub perto a maior concentração de tráfego de base de dados do grupo de sincronização.

#### <a name="cloud-to-cloud-scenario"></a>Cenário de nuvem para a cloud

-   Quando todas as bases de dados num grupo de sincronização estão num único datacenter, o hub deve estar localizado no mesmo datacenter. Esta configuração reduz a latência e o custo de transferência de dados entre os datacenters.
-   Quando as bases de dados num grupo de sincronização em vários datacenters, o hub deve estar localizado no mesmo datacenter como a maioria das bases de dados e tráfego de base de dados.

#### <a name="mixed-scenarios"></a>Cenários de mistos

Aplica as diretrizes anteriores a configurações de grupo de sincronização complexas, tais como os que são uma combinação de cenários de empresa para a nuvem e na cloud para a cloud.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitar a sincronização inicial lenta e dispendiosa

Nesta secção, vamos abordar a sincronização inicial de um grupo de sincronização. Saiba como ajudar a evitar uma sincronização inicial de a demorar mais tempo e que está a ser mais dispendioso do que o necessário.

#### <a name="how-initial-sync-works"></a>Funciona como inicial de sincronização

Quando cria um grupo de sincronização, comece com os dados na base de dados apenas uma. Se tiver dados em várias bases de dados, a sincronização de dados SQL trata cada linha como um conflito de que precisa de ser resolvido. Esta resolução de conflitos faz com que a sincronização inicial ir lentamente. Se tiver dados em várias bases de dados, a sincronização inicial poderá demorar entre alguns dias e meses, dependendo do tamanho de base de dados.

Se as bases de dados estiverem em datacenters diferentes, cada linha tem navegam entre os datacenters diferentes. Isso aumenta o custo de uma sincronização inicial.

#### <a name="recommendation"></a>Recomendação

Se possível, comece com os dados em apenas uma das bases de dados do grupo de sincronização.

### <a name="design-to-avoid-synchronization-loops"></a> Design para evitar os ciclos de sincronização

Um loop de sincronização ocorre quando há referências circulares dentro de um grupo de sincronização. Nesse cenário, cada alteração numa base de dados é que pode dimensionar indefinidamente e forma circular as replicado por meio de bases de dados no grupo de sincronização.   

Certifique-se de que evite loops de sincronização, pois causar degradação do desempenho e poderá aumentar significativamente os custos.

### <a name="handling-changes-that-fail-to-propagate"></a> Alterações que não obedeçam a propagação

#### <a name="reasons-that-changes-fail-to-propagate"></a>Motivos que não obedeçam as alterações sejam propagadas

As alterações poderão falhar propagar para um dos seguintes motivos:

-   Incompatibilidade de esquema/tipo de dados.
-   Inserir nulos nas colunas sem valores nulos.
-   Violação de restrições de chave estrangeira.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando a ativação para propagar as alterações?

-   Sincronizar grupo mostra que está a ser um **aviso** estado.
-   Detalhes estão listados no Visualizador de log do portal da interface do Usuário.
-   Se o problema não for resolvido para 45 dias, a base de dados torna-se desatualizados.

> [!NOTE]
> Estas alterações nunca serão propagadas. A única forma de recuperar neste cenário é voltar a criar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendação

Monitorar a integridade de grupo e a base de dados de sincronização regularmente através da interface do portal e de registo.


## <a name="maintenance"></a>Manutenção

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Evitar bases de dados desatualizados e grupos de sincronização

Um grupo de sincronização ou uma base de dados num grupo de sincronização pode ficar desatualizada. Quando o estado de um grupo de sincronização for **desatualizados**, deixar de funcionar. Quando o estado de uma base de dados for **desatualizados**, dados poderão perder-se. É melhor evitar este cenário, em vez de tentar recuperar a partir do mesmo.

#### <a name="avoid-out-of-date-databases"></a>Evitar bases de dados desatualizados

Estado de uma base de dados está definido como **desatualizados** quando ele estiver offline durante 45 dias ou mais. Para evitar uma **desatualizados** estado numa base de dados, certifique-se de que nenhuma das bases de dados está offline para 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar a grupos de sincronização desatualizados

Estado de um grupo de sincronização é definido como **desatualizados** quando qualquer alteração no grupo de sincronização falha a ser propagada para o restante do grupo de sincronização para 45 dias ou mais. Para evitar uma **desatualizados** estado num grupo de sincronização, verificar regularmente o registro de histórico do grupo de sincronização. Certifique-se de que todos os conflitos são resolvidos e que as alterações são com êxito propagadas as bases de dados do grupo de sincronização.

Um grupo de sincronização poderão falhar aplicar uma alteração para um dos seguintes motivos:

-   Incompatibilidade de esquema entre tabelas.
-   Incompatibilidade de dados entre tabelas.
-   Inserir uma linha com um valor nulo numa coluna que não permite valores nulos.
-   A atualizar uma linha com um valor que viola uma restrição de chave externa.

Para impedir que os grupos de sincronização desatualizados:

-   Atualize o esquema para permitir que os valores que estão contidos nas linhas com falhas.
-   Atualize os valores de chave estrangeiros para incluir os valores que estão contidos nas linhas com falhas.
-   Atualize os valores de dados na linha com falha, para que sejam compatíveis com o esquema ou chaves externas numa base de dados de destino.

### <a name="avoid-deprovisioning-issues"></a> Evitar problemas de desaprovisionamento

Em algumas circunstâncias, anular o registo de uma base de dados com um agente de cliente pode fazer com que a sincronização falha.

#### <a name="scenario"></a>Cenário

1. Grupo de sincronização foi criado utilizando uma instância de base de dados SQL e no local do SQL Server da base de dados, que está associada a 1 de agente local.
2. O mesmo banco de dados no local está registado com o agente local 2 (este agente não está associado a qualquer grupo de sincronização).
3. A anulação do registo da base de dados no local do agente local 2 remove o controlo e tabelas de metadados para sincronizar o grupo A para a base de dados no local.
4. Operações de um grupo de sincronização falharem com este erro: "A operação atual não foi possível concluir uma vez que a base de dados não está aprovisionada para sincronização ou não tem permissões para as tabelas de configuração de sincronização."

#### <a name="solution"></a>Solução

Para evitar este cenário, não se registe uma base de dados com mais de um agente.

Recuperar este cenário:

1. Remova a base de dados de cada grupo de sincronização que pertence.  
2. Adicione a base de dados novamente para cada grupo de sincronização que removeu-lo a partir.  
3. Implemente a cada grupo de sincronização afetado (esta ação Aprovisiona a base de dados).  

### <a name="modifying-your-sync-group"></a> Modificar um grupo de sincronização

Não tente remover uma base de dados de um grupo de sincronização e, em seguida, editar o grupo de sincronização sem primeiro uma implementação das alterações.

Em vez disso, remova primeiro uma base de dados de um grupo de sincronização. Em seguida, implemente a alteração e aguarde pela desaprovisionamento para concluir. Quando o desaprovisionamento estiver concluído, pode editar o grupo de sincronização e implementar as alterações.

Se tentar remover uma base de dados e, em seguida, editar um grupo de sincronização sem primeiro uma implementação das alterações, um ou a outra operação falha. Interface do portal pode se tornar inconsistente. Se isto acontecer, atualize a página para restaurar o estado correto.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a sincronização de dados SQL, consulte:

-   Descrição geral - [sincronizar dados em várias bases de dados na cloud e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal - [Tutorial: Configurar a sincronização de dados SQL para sincronizar dados entre a base de dados do Azure SQL e SQL Server no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](sql-database-data-sync-agent.md)
-   Monitor de - [registos de monitorizar a sincronização de dados de SQL com o Azure Monitor](sql-database-sync-monitor-oms.md)
-   Resolução de problemas - [solucionar problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL - [automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure](sql-database-update-sync-schema.md)
    -   Com o PowerShell - [utilize o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a base de dados SQL, consulte:

-   [Descrição geral da base de dados SQL](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de bases de dados](https://msdn.microsoft.com/library/jj907294.aspx)
