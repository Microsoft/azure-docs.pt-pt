---
title: 'Servidor SQL para SQL Server em Azure Virtual Machines: Guia de migração'
description: Neste guia, aprende a migrar as bases de dados individuais do SQL Server para o SQL Server em Azure Virtual Machines.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550902"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Guia de migração: SQL Server para SQL Server em Azure Virtual Machines

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Neste guia, aprende a *descobrir,* *avaliar* e *migrar* as bases de dados dos seus utilizadores do SQL Server para uma instância do SQL Server em Azure Virtual Machines, utilizando o envio de cópias de segurança e restauro e registo que utiliza [o Data Migration Assistant](/sql/dma/dma-overview) para avaliação.

Pode migrar o SQL Server a funcionar no local ou em:

- SQL Server em máquinas virtuais (VMs).
- Amazon Web Services (AWS) EC2.
- Serviço de Base de Dados Relacional da Amazon (AWS RDS).
- Motor compute (Plataforma Google Cloud [GCP]).

Para obter informações sobre estratégias de migração extra, consulte a visão geral da [migração do SQL Server VM](sql-server-to-sql-on-azure-vm-migration-overview.md). Para outros guias de migração, consulte os [Guias de Migração da Base de Dados Azure.](https://docs.microsoft.com/data-migration)

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagrama que mostra um fluxo de processo de migração.":::

## <a name="prerequisites"></a>Pré-requisitos

Migrar para o SQL Server em Azure Virtual Machines requer os seguintes recursos:

- [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595).
- Um [projeto Azure Migrate.](../../../migrate/create-manage-projects.md)
- Um alvo preparado [SQL Server em Azure Virtual Machines](../../virtual-machines/windows/create-sql-vm-portal.md) caso que é a mesma versão ou maior do que a fonte do SQL Server.
- [Conectividade entre Azure e no local.](/azure/architecture/reference-architectures/hybrid-networking)
- [Escolher uma estratégia de migração adequada.](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)

## <a name="pre-migration"></a>Pré-migração

Antes de iniciar a sua migração, precisa descobrir a topologia do seu ambiente SQL e avaliar a viabilidade da sua migração pretendida.

### <a name="discover"></a>Detetar

A Azure Migrate avalia a adequação da migração de computadores no local, realiza dimensionamento baseado no desempenho e fornece estimativas de custos para correr no local. Para planear a migração, utilize o Azure Migrate para [identificar as fontes de dados existentes e detalhes sobre as funcionalidades que as suas](../../../migrate/concepts-assessment-calculation.md) instâncias do SQL Server utilizam. Este processo envolve a digitalização da rede para identificar todas as suas instâncias do SQL Server na sua organização com a versão e funcionalidades em uso.

> [!IMPORTANT]
> Ao escolher uma máquina virtual Azure alvo para a sua instância SQL Server, não se esqueça de considerar [as diretrizes de desempenho do SQL Server em Azure Virtual Machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Para obter mais ferramentas de descoberta, consulte os [serviços e ferramentas](../../../dms/dms-tools-matrix.md#business-justification-phase) disponíveis para cenários de migração de dados.

### <a name="assess"></a>Avaliação

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Depois de ter descoberto todas as fontes de dados, utilize o [Data Migration Assistant](/sql/dma/dma-overview) para avaliar as instâncias do SQL Server que migram para um caso de SQL Server em Azure Virtual Machines para entender as lacunas entre a origem e as instâncias-alvo.

> [!NOTE]
> Se _não_ estiver a atualizar a versão do SQL Server, salte este passo e mude-se para a secção [Migração.](#migrate)

#### <a name="assess-user-databases"></a>Avaliar bases de dados de utilizadores

O Data Migration Assistant ajuda a sua migração para uma plataforma de dados moderna, detetando problemas de compatibilidade que podem afetar a funcionalidade da base de dados na sua nova versão do SQL Server. O Data Migration Assistant recomenda melhorias de desempenho e fiabilidade para o seu ambiente alvo e também permite mover o seu esquema, dados e objetos de login do seu servidor de origem para o seu servidor alvo.

Para saber mais, consulte [a Avaliação.](/sql/dma/dma-migrateonpremsql)

> [!IMPORTANT]
>Com base no tipo de avaliação, as permissões exigidas no servidor SQL de origem podem ser diferentes:
   > - Para o orientador de *paridade de recursos,* as credenciais fornecidas para ligar à base de dados do SQL Server de origem devem ser um membro da função do servidor *sysadmin.*
   > - Para o consultor *de questões de compatibilidade,* as credenciais fornecidas devem ter, pelo `CONNECT SQL` `VIEW SERVER STATE` menos, e `VIEW ANY DEFINITION` permissões.
   > - O Assistente de Migração de Dados irá destacar as permissões necessárias para o conselheiro escolhido antes de executar a avaliação.

#### <a name="assess-the-applications"></a>Avaliar os pedidos

Normalmente, uma camada de aplicação acede às bases de dados dos utilizadores para persistir e modificar dados. O Data Migration Assistant pode avaliar a camada de acesso a dados de uma aplicação de duas formas:

- Utilizando [eventos estendidos capturados](/sql/relational-databases/extended-events/extended-events) ou vestígios de perfis de [servidor SQL](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) das bases de dados dos seus utilizadores. Também pode utilizar o [Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-capture-trace) para criar um registo de vestígios que também pode ser utilizado para testes A/B.
- Utilizando o [Data Access Migration Toolkit (pré-visualização),](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)que fornece a descoberta e avaliação das consultas SQL dentro do código e é utilizado para migrar código fonte de aplicação de uma plataforma de base de dados para outra. Esta ferramenta suporta tipos de ficheiros populares como C#, Java, XML e texto simples. Para obter um guia sobre como realizar uma avaliação do Data Access Migration Toolkit, consulte o post de blog [do Access Data Migration Assistant.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430)

Durante a avaliação das bases de dados dos utilizadores, utilize o Data Migration Assistant para [importar](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) ficheiros de rastreios capturados ou ficheiros de Ferramentas de Migração de Acesso a Dados.

#### <a name="assessments-at-scale"></a>Avaliações à escala

Se tiver vários servidores que necessitem de uma avaliação do Data Migration Assistant, pode automatizar o processo utilizando a [interface de linha de comando](/sql/dma/dma-commandline). Utilizando a interface, pode preparar os comandos de avaliação com antecedência para cada instância do SQL Server no âmbito de migração.

Para relatórios sumários em grandes propriedades, as avaliações do Data Migration Assistant podem agora ser [consolidadas em Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Bases de dados do refactor com Assistente de Migração de Dados

Com base nos resultados da avaliação do Data Migration Assistant, poderá ter uma série de recomendações para garantir que as bases de dados dos seus utilizadores funcionam e funcionam corretamente após a migração. O Data Migration Assistant fornece detalhes sobre os objetos e recursos impactados para como resolver cada problema. Certifique-se de resolver todas as mudanças de rutura e de comportamento antes de iniciar a migração de produção.

Para funcionalidades precotadas, pode optar por executar as bases de dados dos seus utilizadores no seu modo de [compatibilidade](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) original se quiser evitar efetuar estas alterações e acelerar a migração. Esta ação evitará [atualizar a sua compatibilidade na base de dados](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) até que os itens precotados sejam resolvidos.

Tem de scriptar todas as correções do Data Migration Assistant e aplicá-las na base de dados do SqL Server alvo durante a fase [pós-migração.](#post-migration)

> [!CAUTION]
> Nem todas as versões SQL Server suportam todos os modos de compatibilidade. Verifique se a [versão do seu servidor SQL alvo](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) suporta a compatibilidade da base de dados escolhida. Por exemplo, o SQL Server 2019 não suporta bases de dados com compatibilidade de nível 90 (que é SQL Server 2005). Estas bases de dados exigiriam, pelo menos, uma atualização para o nível de compatibilidade 100.
>

## <a name="migrate"></a>Migrate

Depois de ter concluído os passos de pré-migração, está pronto para migrar as bases de dados e componentes dos utilizadores. Migrar as suas bases de dados utilizando o seu método de [migração](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)preferido.

As secções seguintes fornecem passos para a realização de uma migração utilizando cópias de segurança e restauro ou uma migração mínima de tempo de inatividade utilizando backup e restauro juntamente com o envio de registos.

### <a name="backup-and-restore"></a>Cópia de segurança e restauro

Para realizar uma migração padrão utilizando cópia de segurança e restauro:

1. Confiússe a conectividade com o SQL Server em Azure Virtual Machines com base nos seus requisitos. Para obter mais informações, consulte [Connect to a SQL Server virtual machine on Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Faça uma pausa ou pare quaisquer aplicações que utilizem bases de dados destinadas à migração.
1. Certifique-se de que as bases de dados dos utilizadores estão inativas utilizando [o modo de utilizador único](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Execute uma cópia de segurança completa da base de dados para um local no local.
1. Copie os ficheiros de reserva no local para o seu VM utilizando um ambiente de trabalho remoto, [O Explorador de Dados Azure](/azure/data-explorer/data-explorer-overview)ou o [utilitário da linha de comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md). (Recomenda-se uma cópia de segurança superior a 2-TB.)
1. Restaurar cópias de dados completas para o SQL Server em Azure Virtual Machines.

### <a name="log-shipping-minimize-downtime"></a>Envio de registo (minimizar tempo de inatividade)

Para realizar uma migração mínima de tempo de inatividade utilizando backup e restaurar e registar o envio:

1. Confiem a conectividade com o SQL Server em Azure Virtual Machines com base nos seus requisitos. Para obter mais informações, consulte [Connect to a SQL Server virtual machine on Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Certifique-se de que as bases de dados dos utilizadores a migrar estão em modelo de recuperação total ou a granel.
1. Execute uma cópia de segurança completa da base de dados para um local no local e modifique quaisquer trabalhos de backups de bases de dados completos existentes para utilizar a palavra-chave [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) para preservar a cadeia de registos.
1. Copie os ficheiros de reserva no local para o seu VM utilizando um ambiente de trabalho remoto, [O Explorador de Dados Azure](/azure/data-explorer/data-explorer-overview)ou o [utilitário da linha de comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md). (Recomenda-se uma cópia de segurança superior a 1-TB.)
1. Restaurar cópias de dados completas no SQL Server em Azure Virtual Machines.
1. Confiúdo [de envio](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) de registo entre a base de dados no local e o SQL Server em Azure Virtual Machines. Certifique-se de não reiniciar as bases de dados porque esta tarefa já estava concluída nos passos anteriores.
1. Corte para o servidor alvo.
   1. Faça uma pausa ou pare as aplicações utilizando bases de dados para migrar.
   1. Certifique-se de que as bases de dados dos utilizadores estão inativas utilizando [o modo de utilizador único](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Quando estiver pronto, execute uma [falha controlada](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) por envio de registos de bases de dados no local para o SQL Server em Azure Virtual Machines.

### <a name="migrate-objects-outside-user-databases"></a>Migrar objetos fora das bases de dados dos utilizadores

Poderão ser necessários mais objetos SQL Server para o funcionamento perfeito das bases de dados dos seus utilizadores após a migração.

A tabela a seguir fornece uma lista de componentes e métodos de migração recomendados que podem ser concluídos antes ou depois da migração das bases de dados dos seus utilizadores.

| **Funcionalidade** | **Componente** | **Métodos de migração** |
| --- | --- | --- |
| **Bases de dados** | Modelação | Script com SQL Server Management Studio. |
|| TempDB | Planeie mover o tempDB para [o disco temporário Azure VM (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)para melhor desempenho. Certifique-se de escolher um tamanho VM que tenha um SSD local suficiente para acomodar o seu tempDB. |
|| Bases de dados do utilizador com FileStream | Utilize o [Backup e restaure](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) os métodos de migração. O Assistente de Migração de Dados não suporta bases de dados com o FileStream. |
| **Segurança** | Logins do SQL Server e Windows | Utilize o Assistente de Migração de Dados para [migrar os logins dos utilizadores](/sql/dma/dma-migrateserverlogins). |
|| Funções do Servidor SQL | Script com SQL Server Management Studio. |
|| Fornecedores criptográficos | Recomendar [a conversão para utilizar o Cofre da Chave Azure](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Este procedimento utiliza o [fornecedor de recursos SQL VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Objetos de servidor** | Dispositivos de backup | Substitua-o por cópia de segurança da base de dados utilizando [o Azure Backup,](../../../backup/backup-sql-server-database-azure-vms.md)ou escreva cópias de segurança para [o Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). Este procedimento utiliza o [fornecedor de recursos SQL VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Servidores ligados | Script com SQL Server Management Studio. |
|| Aciona o servidor | Script com SQL Server Management Studio. |
| **Replicação** | Publicações locais | Script com SQL Server Management Studio. |
|| Assinantes locais | Script com SQL Server Management Studio. |
| **PoliBase** | PolyBase | Script com SQL Server Management Studio. |
| **Gestão** | Correio de base de dados | Script com SQL Server Management Studio. |
| **Agente do SQL Server** | Tarefas | Script com SQL Server Management Studio. |
|| Alertas | Script com SQL Server Management Studio. |
|| Operadores | Script com SQL Server Management Studio. |
|| Proxies | Script com SQL Server Management Studio. |
| **Sistema operativo** | Ficheiros, ações de ficheiros | Tome nota de quaisquer outros ficheiros ou partilhas de ficheiros que sejam utilizados pelos seus servidores SQL e se reproduzam no alvo Azure Virtual Machines. |

## <a name="post-migration"></a>Pós-migração

Depois de ter concluído com sucesso a fase de migração, precisa de completar uma série de tarefas pós-migração para garantir que tudo está a funcionar da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. Realizar esta tarefa pode requerer alterações nas aplicações em alguns casos.

Aplique quaisquer correções recomendadas pelo Data Migration Assistant nas bases de dados dos utilizadores. É necessário escrever estas correções para garantir a consistência e permitir a automatização.

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio da migração da base de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração da base de dados, é necessário utilizar consultas SQL. Crie consultas de validação para correr contra as bases de dados de origem e alvo. As suas consultas de validação devem cobrir o âmbito que definiu.
1. **Configurar um ambiente de ensaio**: O ambiente de ensaio deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Escorram testes de validação com a fonte e o alvo e analisem os resultados.
1. **Executar testes de desempenho**: Executar testes de desempenho com a fonte e o alvo e, em seguida, analisar e comparar os resultados.

> [!TIP]
> Utilize o [Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview) para ajudar a avaliar o desempenho do servidor SQL alvo.

### <a name="optimize"></a>Otimização

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados, verificar a completude e abordar potenciais problemas de desempenho com a carga de trabalho.

Para obter mais informações sobre estas questões e os passos para os mitigar, consulte:

- [Guia de validação e otimização pós-migração](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Desempenho de sintonização em máquinas virtuais Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Centro de otimização de custos Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Passos seguintes

- Para verificar a disponibilidade de serviços que se aplicam ao SQL Server, consulte o [centro de infraestruturas global Azure.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)
- Para uma matriz de serviços e ferramentas de terceiros da Microsoft que estão disponíveis para o ajudar com vários cenários de migração de dados e dados e tarefas especiais, consulte [Serviços e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)
- Para saber mais sobre o Azure SQL, consulte:
   - [Opções de implementação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calculadora Azure Total Cost of Ownership (TCO)](https://azure.microsoft.com/pricing/tco/calculator/)

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   - [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Melhores práticas para custos e dimensionamento de cargas de trabalho para a migração para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Para obter informações sobre o licenciamento, consulte:
   - [Traga a sua própria licença com o Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte alargado gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Para avaliar a camada de acesso à aplicação, consulte o [Data Access Migration Toolkit (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para obter informações sobre como realizar testes A/B para a camada de acesso a dados, consulte [a visão geral do Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
