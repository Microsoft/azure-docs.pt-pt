---
title: Migrar do SQL Server para Azure SQL Gestded Instance
description: Saiba como migrar uma base de dados do SQL Server para a Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: 49d37a5537ada260eae453bbb5f81716d42657a5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565828"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migração de casos de servidor SQL para Azure SQL Caso Gerido
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo, você aprende sobre os métodos para migrar um SQL Server 2005 ou posterior instância de versão para [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). Para obter informações sobre migração para uma única base de dados ou piscina elástica, consulte [a visão geral da migração: SQL Server to SQL Database](../migration-guides/database/sql-server-to-sql-database-overview.md). Para obter informações sobre migração de outras plataformas e orientação sobre ferramentas e opções, consulte [Migração para Azure SQL](../migration-guides/index.yml).

> [!NOTE]
> Se quiser começar rapidamente e experimentar a Azure SQL Managed Instance, talvez queira ir ao [guia de arranque rápido](quickstart-content-reference-guide.md) em vez desta página.

A um nível elevado, o processo de migração da base de dados parece:

![Processo de migração](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Avalie a compatibilidade da SQL Managed Instance](#assess-sql-managed-instance-compatibility) onde deve garantir que não existem problemas de bloqueio que possam impedir as suas migrações.
  
  Este passo também inclui a criação de uma linha de base de [desempenho](#create-a-performance-baseline) para determinar a utilização de recursos na sua origem SQL Server. Este passo é necessário se pretender implementar uma instância gerida de tamanho adequado e verificar se o desempenho após a migração não é afetado.
- [Escolha opções de conectividade de aplicativos.](connect-application-instance.md)
- [Implemente para um caso gerido de tamanho ideal](#deploy-to-an-optimally-sized-managed-instance) onde escolherá características técnicas (número de vCores, quantidade de memória) e nível de desempenho (Business Critical, Final Geral) da sua instância gerida.
- [Selecione o método de migração e migrar](#select-a-migration-method-and-migrate) para onde migrar as suas bases de dados usando migração offline (backup/restauro nativo, importação/exportação de base de dados) ou migração on-line (Azure Data Migration Service, replicação transacional).
- [Monitorize as aplicações](#monitor-applications) para garantir que tem um desempenho esperado.

> [!NOTE]
> Para migrar uma base de dados individual para uma única base de dados ou uma piscina elástica, consulte [a base de dados do SQL Server para a Base de Dados Azure SQL](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Avaliar a compatibilidade da SQL Gestão de Instâncias

Em primeiro lugar, determine se a SQL Managed Instance é compatível com os requisitos da base de dados da sua aplicação. SQL Managed Instance é projetado para fornecer fácil migração de elevação e mudança para a maioria das aplicações existentes que usam o SQL Server. No entanto, por vezes pode exigir funcionalidades ou capacidades que ainda não estejam suportadas e o custo de implementação de uma solução alternativa é demasiado elevado.

Utilize [o Assistente de Migração de Dados](/sql/dma/dma-overview) para detetar potenciais problemas de compatibilidade com impacto na funcionalidade da base de dados no Azure SQL Database. Se houver alguns problemas de bloqueio relatados, poderá ter de considerar uma opção alternativa, como [o SQL Server em Azure VM](https://azure.microsoft.com/services/virtual-machines/sql-server/). Eis alguns exemplos:

- Se necessitar de acesso direto ao sistema operativo ou sistema de ficheiros, por exemplo, para instalar agentes de terceiros ou personalizados na mesma máquina virtual com o SQL Server.
- Se tiver uma dependência estrita de funcionalidades que ainda não sejam suportadas, como FileStream/FileTable, PolyBase e transações de instâncias cruzadas.
- Se precisar de ficar numa versão específica do SQL Server (2012, por exemplo).
- Se os seus requisitos de cálculo forem muito inferiores às ofertas de instância geridas (um vCore, por exemplo), e a consolidação da base de dados não é uma opção aceitável.

Se resolveu todos os bloqueadores de migração identificados e continua a migração para a SQL Managed Instance, note que algumas das alterações podem afetar o desempenho da sua carga de trabalho:

- O modelo obrigatório de recuperação total e o calendário regular de backup automatizado podem ter impacto no desempenho das suas ações de carga de trabalho ou manutenção/ETL se tiver usado periodicamente um modelo simples/graneleiro ou parar as cópias de segurança a pedido.
- Configurações diferentes do servidor ou do nível da base de dados, tais como bandeiras de traços ou níveis de compatibilidade.
- Novas funcionalidades que está a utilizar, tais como encriptação transparente de base de dados (TDE) ou grupos de falha automática podem ter impacto na utilização do CPU e do IO.

A SQL Managed Instance garante uma disponibilidade de 99,99% mesmo em cenários críticos, pelo que a sobrecarga causada por estas funcionalidades não pode ser desativada. Para obter mais informações, consulte [as causas de raiz que podem causar um desempenho diferente no SQL Server e no Azure SQL Managed Instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabelas otimizadas pela memória)

O SQL Server fornece In-Memory capacidade OLTP que permite o uso de tabelas otimizadas pela memória, tipos de mesa otimizados pela memória e módulos SQL compilados de forma nativa para executar cargas de trabalho com elevado rendimento e baixos requisitos de processamento de transações de latência. 

> [!IMPORTANT]
> In-Memory OLTP só é suportado no nível Business Critical em Azure SQL Managed Instance (e não suportado no nível final de propósito geral).

Se tiver tabelas otimizadas para a memória ou tipos de mesa otimizados para a memória no seu SqL Server no local e pretende migrar para a Azure SQL Managed Instance, deve:

- Escolha o nível Critical de Negócios para o seu target Azure SQL Managed Instance que suporta In-Memory OLTP, ou
- Se pretender migrar para o nível de Finalidade Geral em Azure SQL Managed Instance, remova as tabelas otimizadas pela memória, os tipos de mesa otimizados pela memória e os módulos SQL compilados de forma nativa que interagem com objetos otimizados para a memória antes de migrar a sua base de dados. A seguinte consulta T-SQL pode ser usada para identificar todos os objetos que precisam de ser removidos antes da migração para o nível final:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Para saber mais sobre as tecnologias de memória, consulte [o desempenho otimizar utilizando tecnologias de memória na Base de Dados Azure SQL e na Azure SQL Managed Instance](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview)

### <a name="create-a-performance-baseline"></a>Criar uma linha de base de desempenho

Se precisar de comparar o desempenho da sua carga de trabalho numa instância gerida com a sua carga de trabalho original a funcionar no SQL Server, terá de criar uma linha de base de desempenho que será usada para comparação.

A linha de base de desempenho é um conjunto de parâmetros tais como o uso médio/max do CPU, a latência média/máx do disco IO, a produção, o IOPS, a esperança média/máxima da vida da página e o tamanho máximo médio de temperatura. Gostaria de ter parâmetros semelhantes ou ainda melhores após a migração, pelo que é importante medir e registar os valores de base para estes parâmetros. Além dos parâmetros do sistema, você precisaria selecionar um conjunto das consultas representativas ou as consultas mais importantes na sua carga de trabalho e medir a duração min/média/máxima e a utilização do CPU para as consultas selecionadas. Estes valores permitir-lhe-iam comparar o desempenho da carga de trabalho em execução na instância gerida com os valores originais na sua origem SQL Server.

Alguns dos parâmetros que necessitaria de medir na sua instância do SQL Server são:

- [Monitorize a utilização do CPU na sua instância sql Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registem a utilização média e máxima do CPU.
- [Monitorize](/sql/relational-databases/performance-monitor/monitor-memory-usage) o uso da memória na sua instância do SQL Server e determine a quantidade de memória utilizada por diferentes componentes, tais como piscina tampão, cache de plano, piscina de loja de colunas, [OLTP in-memory,](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)etc. Além disso, deve encontrar valores médios e máximos do contador de desempenho da memória da esperança de vida da página.
- Monitorize a utilização do IO do disco na origem SQL Server, utilizando [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) visualização ou [contadores de desempenho](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorize o desempenho da carga de trabalho e consulta ou a sua instância sql Server examinando Vistas de Gestão Dinâmica ou Loja de Consultas se estiver a migrar de uma versão SQL Server 2016+. Identifique a duração média e o uso do CPU das consultas mais importantes na sua carga de trabalho para compará-las com as consultas que estão a decorrer no caso gerido.

> [!Note]
> Se notar qualquer problema com a sua carga de trabalho no SQL Server, como a utilização elevada do CPU, a pressão constante da memória, ou problemas temporários ou de parametrização, deverá tentar resolvê-los na sua origem SQL Server antes de tomar a linha de base e a migração. Migrar problemas conhecidos para qualquer novo sistema pode causar resultados inesperados e invalidar qualquer comparação de desempenho.

Como resultado desta atividade, deverá ter documentado valores médios e máximos para o uso de CPU, memória e IO no seu sistema de origem, bem como a duração média e máxima e o uso do CPU das consultas dominantes e críticas na sua carga de trabalho. Deverá utilizar estes valores mais tarde para comparar o desempenho da sua carga de trabalho numa instância gerida com o desempenho da linha de base da carga de trabalho na instância do SqL Server de origem.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementar para um exemplo gerido de tamanho ideal

SQL Managed Instance é personalizado para cargas de trabalho no local que estão planejando mover-se para a nuvem. Introduz um [novo modelo de compra](../database/service-tiers-vcore.md) que proporciona uma maior flexibilidade na seleção do nível certo de recursos para as suas cargas de trabalho. No mundo do local, você provavelmente está habituado a dimensionar estas cargas de trabalho usando núcleos físicos e largura de banda IO. O modelo de compra para instância gerida baseia-se em núcleos virtuais, ou "vCores", com armazenamento adicional e IO disponível separadamente. O modelo vCore é uma forma mais simples de entender os seus requisitos de computação na nuvem versus o que utiliza hoje no local. Este novo modelo permite-lhe dimensionar o ambiente de destino na nuvem. Algumas diretrizes gerais que podem ajudá-lo a escolher o nível de serviço e características certos são descritos aqui:

- Com base na utilização do CPU de base, pode providenciar uma instância gerida que corresponda ao número de núcleos que está a utilizar no SQL Server, tendo em mente que as características da CPU podem ter de ser dimensionadas para corresponder às [características VM onde a instância gerida está instalada.](resource-limits.md#hardware-generation-characteristics)
- Com base na utilização da memória de base, escolha [o nível de serviço que tenha memória correspondente](resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser escolhida diretamente, pelo que seria necessário selecionar a instância gerida com a quantidade de vCores que tem memória correspondente (por exemplo, 5.1 GB/vCore na Gen5).
- Com base na latência de linha de base do subsistema de ficheiros, escolha entre os níveis de serviço De Finalidade Geral (latência superior a 5 ms) e Business Critical (latência inferior a 3 ms).
- Com base no resultado da linha de base, pré-aloque o tamanho dos dados ou registo de ficheiros para obter o desempenho esperado do IO.

Pode escolher os recursos de computação e armazenamento no momento da implementação e, em seguida, alterá-lo depois sem introduzir tempo de inatividade para a sua aplicação utilizando o [portal Azure:](../database/scale-resources.md)

![Dimensionamento de casos geridos](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Para aprender a criar a infraestrutura VNet e um caso gerido, consulte [Criar um caso gerido.](instance-create-quickstart.md)

> [!IMPORTANT]
> É importante manter o seu destino VNet e sub-rede de acordo com os [requisitos de VNet de instância gerida](connectivity-architecture-overview.md#network-requirements). Qualquer incompatibilidade pode impedi-lo de criar novos casos ou usar aqueles que já criou. Saiba mais sobre [a criação](virtual-network-subnet-create-arm-template.md) de redes novas e [configuradas.](vnet-existing-add-subnet.md)

## <a name="select-a-migration-method-and-migrate"></a>Selecione um método de migração e migrar

A SQL Managed Instance visa cenários de utilizadores que requerem migração de bases de dados em massa a partir de implementações de bases de dados de VM ou Azure VM. São a escolha ideal quando é necessário levantar e deslocar a parte de trás das aplicações que usam regularmente as funcionalidades de nível de instância e/ou de base de dados cruzadas. Se este é o seu cenário, você pode mover um caso inteiro para um ambiente correspondente em Azure sem a necessidade de recontratar as suas aplicações.

Para mover as instâncias SQL, é necessário planear cuidadosamente:

- A migração de todas as bases de dados que precisam de ser collocadas (as que estão a funcionar no mesmo caso).
- A migração de objetos de nível de instância de que a sua aplicação depende, incluindo logins, credenciais, empregos e operadores de agentes SQL e gatilhos de nível de servidor.

SQL Managed Instance é um serviço gerido que lhe permite delegar algumas das atividades regulares do DBA na plataforma à medida que são incorporadas. Portanto, alguns dados de nível de instância não precisam de ser migrados, tais como trabalhos de manutenção para cópias de segurança regulares ou configuração Always On, uma vez [que a alta disponibilidade](../database/high-availability-sla.md) é incorporada.

A SQL Managed Instance suporta as seguintes opções de migração de bases de dados (atualmente estes são os únicos métodos de migração suportados):

- Azure Database Migration Service - migração com tempo de inatividade quase nulo.
- Native `RESTORE DATABASE FROM URL` - usa backups nativos do SQL Server e requer algum tempo de inatividade.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[O Azure Database Migration Service](../../dms/dms-overview.md) é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de terceiros e SQL Server para Azure. As opções de implementação na pré-visualização pública incluem bases de dados em bases de dados Azure SQL e bases de dados do SQL Server numa máquina virtual Azure. O Serviço de Migração de Bases de Dados é o método recomendado de migração para as cargas de trabalho da sua empresa.

Se utilizar os Serviços de Integração de Servidores SQL (SSIS) no SQL Server nas instalações, o Serviço de Migração de Bases de Dados ainda não suporta a migração do catálogo SSIS (SSISDB) que armazena pacotes SSIS, mas pode providenciar o Tempo de Execução de Integração Azure-SSIS (IR) na Azure Data Factory, que criará um novo SSISDB num caso gerido para que possa reenviar os seus pacotes. Ver [Create Azure-SSIS IR na Azure Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Para saber mais sobre este cenário e etapas de configuração para o Serviço de Migração de Bases [de Dados, consulte a base de dados "Migrar" no local para gerir a sua 20.000 horas através do Serviço de Migração de Bases de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURO nativo a partir do URL

RESTAURAR as cópias de segurança nativas (.bak ficheiros) retirados de uma instância do SQL Server, disponível no [Azure Storage,](https://azure.microsoft.com/services/storage/)é uma das principais capacidades da SQL Managed Instance que permite uma migração rápida e fácil da base de dados offline.

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![O diagrama mostra o SQL Server com uma seta marcada como BACKUP / Upload para URL fluindo para O Armazenamento Azure e uma segunda seta marcada RESTAURADA do URL que flui do Armazenamento Azure para uma Instância Gerida de SQL.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

A tabela a seguir fornece mais informações sobre os métodos que pode utilizar dependendo da versão do SQL Server de origem que está a executar:

|Passo|Motor e versão SQL|Método de backup/restauro|
|---|---|---|
|Coloque backup no Azure Storage|Antes de 2012 SP1 CU2|Faça o upload .bak ficheiro diretamente para o Azure Storage|
||2012 SP1 CU2 - 2016|Backup direto utilizando sintaxe despreordenada [com sintaxe CREDENCIAL](/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 e acima|Backup direto utilizando [COM CREDENCIAL SAS](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurar do Azure Storage para um caso gerido|[RESTAURAR A PARTIR DE URL com CREDENTIAL SAS](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Quando está a migrar uma base de dados protegida pela [Encriptação de Dados Transparente](../database/transparent-data-encryption-tde-overview.md) para uma instância gerida utilizando a opção de restauro nativo, o certificado correspondente a partir do local ou do Azure VM SQL Server precisa de ser migrado antes da restauração da base de dados. Para obter etapas detalhadas, consulte [Migrar um certificado TDE para um caso gerido](tde-certificate-migrate.md).
> - A restauração das bases de dados do sistema não é suportada. Para migrar objetos de nível de instância (armazenados em bases de dados master ou msdb), recomendamos que os roteiem e execute scripts T-SQL na instância de destino.

Para obter um arranque rápido que mostre como restaurar uma cópia de segurança de uma base de dados para uma instância gerida utilizando uma credencial SAS, consulte [Restaurar de cópia de segurança para um caso gerido](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorizar aplicações

Uma vez concluída a migração para um caso gerido, deve acompanhar o comportamento da aplicação e o desempenho da sua carga de trabalho. Este processo inclui as seguintes atividades:

- [Compare o desempenho da carga de trabalho em execução na instância gerida](#compare-performance-with-the-baseline) com a [linha de base de desempenho que criou na instância do Servidor SQL de origem](#create-a-performance-baseline).
- [Monitorize](#monitor-performance) continuamente o desempenho da sua carga de trabalho para identificar potenciais problemas e melhorias.

### <a name="compare-performance-with-the-baseline"></a>Compare o desempenho com a linha de base

A primeira atividade que você precisaria tomar imediatamente após a migração bem sucedida é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho de base. O objetivo desta atividade é confirmar que o desempenho da carga de trabalho na sua instância gerida satisfaz as suas necessidades.

A migração da base de dados para um caso gerido mantém as definições da base de dados e o seu nível de compatibilidade original na maioria dos casos. As definições originais são preservadas sempre que possível para reduzir o risco de algumas degradações de desempenho em comparação com a sua origem SQL Server. Se o nível de compatibilidade de uma base de dados de utilizadores fosse igual ou superior a 100 antes da migração, permanece o mesmo após a migração. Se o nível de compatibilidade de uma base de dados de utilizadores fosse de 90 antes da migração, na base de dados atualizada, o nível de compatibilidade é fixado para 100, que é o nível de compatibilidade suportado mais baixo num caso gerido. O nível de compatibilidade das bases de dados do sistema é de 140. Uma vez que a migração para um caso gerido está realmente a migrar para a versão mais recente do motor de base de dados SQL Server, deve estar ciente de que precisa de voltar a testar o desempenho da sua carga de trabalho para evitar alguns problemas de desempenho surpreendentes.

Como pré-requisito, certifique-se de que completou as seguintes atividades:

- Alinhar as definições na instância gerida com as definições da origem SQL Server, investigando várias instâncias, base de dados, definições temporárias e configurações. Certifique-se de que não alterou as definições como níveis de compatibilidade ou encriptação antes de executar a primeira comparação de desempenho, ou aceite o risco de que algumas das novas funcionalidades que ativou possam afetar algumas consultas. Para reduzir os riscos de migração, altere o nível de compatibilidade da base de dados apenas após a monitorização do desempenho.
- Implementar [diretrizes de boas práticas de armazenamento para fins gerais,](https://techcommunity.microsoft.com)tais como pré-alocar o tamanho dos ficheiros para obter um melhor desempenho.
- Conheça as [principais diferenças ambientais que podem causar as diferenças de desempenho entre um caso gerido e o SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/), e identifique os riscos que podem afetar o desempenho.
- Certifique-se de que mantém a Loja de Consultas ativada e a sintonização automática na sua instância gerida. Estas funcionalidades permitem medir o desempenho da carga de trabalho e corrigir automaticamente os potenciais problemas de desempenho. Aprenda a utilizar a Query Store como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da mudança de nível de compatibilidade da base de dados, como explicado na [estabilidade de desempenho durante a atualização para uma versão mais recente do SQL Server](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Uma vez preparado o ambiente que é comparável tanto quanto possível com o seu ambiente no local, pode começar a executar a sua carga de trabalho e medir o desempenho. O processo de medição deve incluir os mesmos parâmetros que mediu [enquanto criou o desempenho de base das suas medidas de carga de trabalho na origem SQL Server .](#create-a-performance-baseline)
Como resultado, deve comparar os parâmetros de desempenho com a linha de base e identificar diferenças críticas.

> [!NOTE]
> Em muitos casos, não seria capaz de obter exatamente o desempenho correspondente na instância gerida e SQL Server. Azure SQL Managed Instance é um motor de base de dados SQL Server, mas a infraestrutura e a configuração de alta disponibilidade num caso gerido podem introduzir algumas diferenças. Seria de esperar que algumas consultas fossem mais rápidas, enquanto outras poderiam ser mais lentas. O objetivo da comparação é verificar que o desempenho da carga de trabalho no caso gerido corresponde ao desempenho no SQL Server (em média), e identificar quaisquer consultas críticas com o desempenho que não correspondam ao seu desempenho original.

O resultado da comparação de desempenho pode ser:

- O desempenho da carga de trabalho na instância gerida está alinhado ou melhor do que o desempenho da carga de trabalho no SQL Server. Neste caso, confirmou com êxito que a migração é bem sucedida.
- A maioria dos parâmetros de desempenho e as consultas no trabalho de carga de trabalho funcionam bem, com algumas exceções com desempenho degradado. Neste caso, é necessário identificar as diferenças e a sua importância. Se houver algumas consultas importantes com desempenho degradado, você deve investigar se os planos SQL subjacentes mudaram ou as consultas estão atingindo alguns limites de recursos. A mitigação neste caso poderia ser aplicar algumas dicas sobre as consultas críticas (por exemplo, alterado o nível de compatibilidade, o estimador do legado cardinalício) quer diretamente quer usando guias de planos, reconstruir ou criar estatísticas e índices que possam afetar os planos.
- A maioria das consultas são mais lentas numa instância gerida em comparação com a sua origem SQL Server. Neste caso, tente identificar as causas da diferença, tais como [atingir algum limite](resource-limits.md#service-tier-characteristics) de recursos como limites de IO, limite de memória, limite de taxa de registo de casos, etc. Se não houver limites de recursos que possam causar a diferença, tente alterar o nível de compatibilidade da base de dados ou alterar as definições da base de dados, como a estimativa do legado cardinalício e reinicie o teste. Reveja as recomendações fornecidas pela instância gerida ou pontos de vista da Loja de Consultas para identificar as consultas que regrediram no desempenho.

> [!IMPORTANT]
> A Azure SQL Managed Instance tem uma funcionalidade de correção de plano automático incorporada que é ativada por padrão. Esta funcionalidade garante que as consultas que funcionavam bem na pasta não se degradariam no futuro. Certifique-se de que esta funcionalidade está ativada e que executou a carga de trabalho tempo suficiente com as definições antigas antes de alterar novas definições de modo a permitir que a instância gerida possa conhecer o desempenho e os planos de base.

Faça a alteração dos parâmetros ou atualize os níveis de serviço para convergir para a configuração ideal até obter o desempenho da carga de trabalho que se adequa às suas necessidades.

### <a name="monitor-performance"></a>Monitorizar desempenho

A SQL Managed Instance fornece um monte de ferramentas avançadas para monitorização e resolução de problemas, e deve usá-las para monitorizar o desempenho no seu caso. Alguns dos parâmetros que necessitaria de monitorizar são:

- Utilização do CPU no caso para determinar se o número de vCores que a provisionou é a combinação certa para a sua carga de trabalho.
- Esperança de vida de página no seu caso gerido para determinar [se precisa de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Estatísticas como `INSTANCE_LOG_GOVERNOR` ou `PAGEIOLATCH` que dirão se você tem problemas de IO de armazenamento, especialmente no nível de Finalidade Geral, onde você pode precisar pré-alocar ficheiros para obter um melhor desempenho IO.

## <a name="leverage-advanced-paas-features"></a>Alavancar recursos de PaaS avançados

Uma vez que esteja numa plataforma totalmente gerida e tenha verificado que os desempenhos da carga de trabalho estão a corresponder ao desempenho da carga de trabalho do seu SQL Server, utilize vantagens que são fornecidas automaticamente como parte do serviço.

Mesmo que não faça algumas alterações na sua gestão durante a migração, existem altas probabilidades de ligar algumas das novas funcionalidades enquanto estiver a operar o seu caso para tirar partido das mais recentes melhorias no motor da base de dados. Algumas alterações só são ativadas uma vez alterado o [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)da base de dados .

Por exemplo, não é preciso criar cópias de segurança em caso gerido - o serviço realiza cópias de segurança automaticamente para si. Já não te deves preocupar em agendar, tirar e gerir os backups. A SQL Managed Instance proporciona-lhe a capacidade de restaurar a qualquer ponto no tempo dentro deste período de retenção utilizando [Point in Time Recovery (PITR)](../database/recovery-using-backups.md#point-in-time-restore). Além disso, não precisa de se preocupar em criar alta disponibilidade, uma vez que a [alta disponibilidade](../database/high-availability-sla.md) é incorporada.

Para reforçar a segurança, considere a utilização da [Autenticação do Diretório Ativo Azure,](../database/security-overview.md) [a auditoria, a](auditing-configure.md) [deteção de ameaças,](../database/azure-defender-for-sql.md) [a segurança ao nível da linha](/sql/relational-databases/security/row-level-security)e a máscara dinâmica de [dados.](/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, um caso gerido fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorizar e afinar a sua carga de trabalho.](../database/monitor-tune-overview.md) [O Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) permite-lhe monitorizar um grande conjunto de casos geridos e centralizar a monitorização de um grande número de casos e bases de dados. [A sintonização automática](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) em instâncias geridas monitoriza continuamente o desempenho das estatísticas de execução do seu plano SQL e corrige automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a Azure SQL Managed Instance, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Para um tutorial que inclua uma restauração a partir de cópia de segurança, consulte [Criar uma instância gerida.](instance-create-quickstart.md)
- Para apresentar tutoriales que mostrem migração utilizando o Serviço de Migração de Bases [de Dados, consulte a base de dados de migração do Azure SQL usando o Serviço de Migração de Bases de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).