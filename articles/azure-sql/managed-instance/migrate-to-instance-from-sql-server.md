---
title: Migrar do SQL Server para uma Instância Gerida Azure SQL
description: Saiba como migrar uma base de dados do SQL Server para a Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: c0b34e17c202cb060773c53aa5775343ade9c2ee
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193779"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migração de casos de servidor SQL para Azure SQL Caso Gerido
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo, você aprende sobre os métodos para migrar um SQL Server 2005 ou posterior instância de versão para [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). Para obter informações sobre migração para uma única base de dados ou piscina elástica, consulte [Migrar para uma base de dados única ou agamada.](../database/migrate-to-database-from-sql-server.md) Para obter informações sobre migração de outras plataformas, consulte [o Guia de Migração da Base de Dados Azure.](https://datamigration.microsoft.com/)

> [!NOTE]
> Se quiser começar rapidamente e experimentar a Azure SQL Managed Instance, talvez queira ir ao [guia de arranque rápido](quickstart-content-reference-guide.md) em vez desta página.

A um nível elevado, o processo de migração da base de dados parece:

![processo de migração](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Avalie a compatibilidade da SQL Managed Instance](#assess-sql-managed-instance-compatibility) onde deve garantir que não existem problemas de bloqueio que possam impedir as suas migrações.
  - Este passo também inclui a criação de linha de base de [desempenho](#create-performance-baseline) para determinar a utilização de recursos na sua origem SQL Server. Este passo é necessário se pretender implementar a sql managed instance adequadamente dimensionada e verificar se os desempenhos após a migração não são afetados.
- [Escolha opções de conectividade de aplicativos](connect-application-instance.md)
- [Implemente para uma localização idealizada sql gestuditária onde](#deploy-to-an-optimally-sized-managed-instance) escolherá características técnicas (número de vCores, quantidade de memória) e nível de desempenho (Business Critical, Final Geral) da sua SQL Managed Instance.
- [Selecione o método de migração e migrar](#select-migration-method-and-migrate) para onde migrar as suas bases de dados usando migração offline (backup/restauro nativo, importação/exportação de base de dados) ou migração on-line (Serviço de Migração de Dados, Replicação Transacional).
- [Monitorize as aplicações](#monitor-applications) para garantir que tem um desempenho esperado.

> [!NOTE]
> Para migrar uma base de dados individual para uma única base de dados ou piscina elástica, consulte [a migração de uma base de dados sql server para a Base de Dados Azure SQL](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Avaliar a compatibilidade da SQL Gestão de Instâncias

Em primeiro lugar, determine se a SQL Managed Instance é compatível com os requisitos da base de dados da sua aplicação. SQL Managed Instance é projetado para fornecer fácil migração de elevação e mudança para a maioria das aplicações existentes que usam o SQL Server. No entanto, por vezes pode exigir funcionalidades ou capacidades que ainda não estejam suportadas e o custo de implementação de uma solução alternativa é demasiado elevado.

Utilize [o Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) para detetar potenciais problemas de compatibilidade com impacto na funcionalidade da base de dados no Azure SQL Database. Se houver alguns problemas de bloqueio relatados, poderá ter de considerar uma opção alternativa, como [o SQL Server em máquinas virtuais Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Eis alguns exemplos:

- Se necessitar de acesso direto ao sistema operativo ou sistema de ficheiros, por exemplo, para instalar agentes de terceiros ou personalizados na mesma máquina virtual com o SQL Server.
- Se tiver uma dependência estrita de funcionalidades que ainda não sejam suportadas, tais como FileStream / FileTable, PolyBase e transações de instância cruzada.
- Se precisar de permanecer numa versão específica do SQL Server (2012, por exemplo).
- Se os seus requisitos de cálculo forem muito mais baixos que as ofertas de instâncias geridas (um vCore, por exemplo) e a consolidação da base de dados não é uma opção aceitável.

Se resolveu todos os bloqueadores de migração identificados e continuou a migração para a SQL Managed Instance, note que algumas das alterações podem afetar o desempenho da sua carga de trabalho:

- O modelo obrigatório de recuperação total e o calendário regular de backup automatizado podem ter impacto no desempenho das suas ações de carga de trabalho ou manutenção/ETL se tiver usado periodicamente um modelo simples/graneleiro ou parar as cópias de segurança a pedido.
- Configurações diferentes do servidor ou do nível da base de dados, tais como bandeiras de traços ou níveis de compatibilidade
- Novas funcionalidades que está a utilizar, tais como encriptação transparente de base de dados (TDE) ou grupos de falha automática podem ter impacto na utilização do CPU e do IO.

A SQL Managed Instance garante uma disponibilidade de 99,99% mesmo em cenários críticos, pelo que a sobrecarga causada por estas funcionalidades não pode ser desativada. Para obter mais informações, consulte [as causas de raiz que podem causar um desempenho diferente no SQL Server e no Azure SQL Managed Instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Criar linha de base de desempenho

Se precisar de comparar o desempenho da sua carga de trabalho em instância gerida com a sua carga de trabalho original em execução no SQL Server, terá de criar uma linha de base de desempenho que será usada para comparação.

A linha de base de desempenho é um conjunto de parâmetros tais como o uso médio/max do CPU, latência média/máx do disco IO, produção, IOPS, esperança média/máxima de vida da página, tamanho máximo médio de temperatura. Gostaria de ter parâmetros semelhantes ou ainda melhores após a migração, pelo que é importante medir e registar os valores de base para estes parâmetros. Além dos parâmetros do sistema, você precisaria selecionar um conjunto das consultas representativas ou as consultas mais importantes na sua carga de trabalho e medir a duração min/média/máxima, utilização do CPU para as consultas selecionadas. Estes valores permitir-lhe-iam comparar o desempenho da carga de trabalho em execução em instância gerida com os valores originais no seu Servidor SQL de origem.

Alguns dos parâmetros que necessitaria de medir na sua instância do SQL Server são:

- [Monitorize a utilização do CPU na sua instância sql Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registem a utilização média e máxima do CPU.
- [Monitorize](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) o uso da memória na sua instância do SQL Server e determine a quantidade de memória utilizada por diferentes componentes, tais como piscina tampão, cache de plano, piscina de loja de colunas, [OLTP in-memory,](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)etc. Além disso, deve encontrar valores médios e máximos do contador de desempenho da memória da esperança de vida da página.
- Monitorize a utilização do IO do disco na origem SQL Server exemplo usando [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) vista ou [contadores de desempenho](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorize o desempenho da carga de trabalho e consulta ou a sua instância do SQL Server examinando Vistas de Gestão Dinâmica ou Loja de Consultas se estiver a migrar da versão SQL Server 2016+. Identifique a duração média e o uso do CPU das consultas mais importantes na sua carga de trabalho para compará-las com as consultas que estão a decorrer no caso gerido.

> [!Note]
> Se notar qualquer problema com a sua carga de trabalho no SQL Server, como a utilização elevada do CPU, a pressão constante da memória, a temperatura ou os problemas de parametrização, deverá tentar resolvê-los na sua origem SQL Server antes de tomar a linha de base e a migração. Migrar sabe problemas para qualquer novo sistema migh causar resultados inesperados e invalidar qualquer comparação de desempenho.

Como resultado desta atividade, deverá ter documentado valores médios e máximos para o uso de CPU, memória e IO no seu sistema de origem, bem como a duração média e máxima e o uso do CPU das consultas dominantes e críticas na sua carga de trabalho. Deverá utilizar estes valores mais tarde para comparar o desempenho da sua carga de trabalho em instância gerida com o desempenho da linha de base da carga de trabalho no Servidor SQL de origem.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementar para um exemplo gerido de tamanho ideal

SQL Managed Instance é personalizado para cargas de trabalho no local que estão planejando mover-se para a nuvem. Introduz um [novo modelo de compra](../database/service-tiers-vcore.md) que proporciona uma maior flexibilidade na seleção do nível certo de recursos para as suas cargas de trabalho. No mundo do local, você provavelmente está habituado a dimensionar estas cargas de trabalho usando núcleos físicos e largura de banda IO. O modelo de compra para instância gerida baseia-se em núcleos virtuais, ou "vCores", com armazenamento adicional e IO disponível separadamente. O modelo vCore é uma forma mais simples de entender os seus requisitos de computação na nuvem versus o que utiliza hoje no local. Este novo modelo permite-lhe dimensionar o ambiente de destino na nuvem. Algumas diretrizes gerais que podem ajudá-lo a escolher o nível de serviço e características certos são descritos aqui:

- Com base na utilização do CPU de base, pode prever uma instância gerida que corresponda ao número de núcleos que está a utilizar no SQL Server, tendo em mente que as características da CPU podem ter de ser dimensionadas para corresponder às [características VM onde a instância gerida é instalada.](resource-limits.md#hardware-generation-characteristics)
- Com base no uso da memória de base, escolha [o nível de serviço que tenha memória correspondente](resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser escolhida diretamente, pelo que teria de selecionar a instância gerida com a quantidade de vCores que tem memória correspondente (por exemplo, 5.1 GB/vCore na Gen5).
- Com base na latência de linha de base do subsistema de ficheiros, escolha entre Os níveis de Final Geral (latência superior a 5ms) e os níveis de serviço Business Critical (latência inferior a 3 ms).
- Com base na produção de base pré-alocar o tamanho dos dados ou ficheiros de registo para obter o desempenho esperado do IO.

Pode escolher os recursos de computação e armazenamento no momento da implementação e, em seguida, alterá-lo depois sem introduzir tempo de inatividade para a sua aplicação utilizando o [portal Azure:](../database/scale-resources.md)

![tamanho de instância gerida](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Para aprender a criar a infraestrutura VNet e um caso gerido, consulte [Criar um caso gerido.](instance-create-quickstart.md)

> [!IMPORTANT]
> É importante manter o seu destino VNet e sub-redes sempre de acordo com os [requisitos de VNet de instância gerida](connectivity-architecture-overview.md#network-requirements). Qualquer incompatibilidade pode impedi-lo de criar novos casos ou usar aqueles que já criou. Saiba mais sobre [a criação](virtual-network-subnet-create-arm-template.md) de redes novas e [configuradas.](vnet-existing-add-subnet.md)

## <a name="select-migration-method-and-migrate"></a>Selecione o método de migração e migrar

A SQL Managed Instance visa cenários de utilizadores que requerem migração de bases de dados em massa a partir de implementações de bases de dados de VM ou Azure VM. São a escolha ideal quando é necessário levantar e deslocar a parte de trás das aplicações que usam regularmente o nível de instância e/ou funcionalidades de base de dados cruzadas. Se este é o seu cenário, você pode mover um caso inteiro para um ambiente correspondente em Azure sem a necessidade de recontratar as suas aplicações.

Para mover as instâncias SQL, é necessário planear cuidadosamente:

- A migração de todas as bases de dados que precisam de ser collocadas (as que estão a funcionar no mesmo caso)
- A migração de objetos de nível de instância de que a sua aplicação depende, incluindo logins, credenciais, empregos e operadores de agentes SQL e gatilhos de nível de servidor.

SQL Managed Instance é um serviço gerido que lhe permite delegar algumas das atividades regulares do DBA na plataforma à medida que são incorporadas. Portanto, alguns dados de nível de instância não precisam de ser migrados, tais como trabalhos de manutenção para cópias de segurança regulares ou configuração Always On, uma vez [que a alta disponibilidade](../database/high-availability-sla.md) é incorporada.

A SQL Managed Instance suporta as seguintes opções de migração de bases de dados (atualmente estes são os únicos métodos de migração suportados):

- Azure Database Migration Service - migração com tempo de inatividade quase nulo,
- Native `RESTORE DATABASE FROM URL` - usa backups nativos do SQL Server e requer algum tempo de inatividade.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

O [Azure Database Migration Service (DMS)](../../dms/dms-overview.md) é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de terceiros e sql server para Azure. As opções de implementação na pré-visualização pública incluem bases de dados em bases de dados Azure SQL e bases de dados do SQL Server numa Máquina Virtual Azure. DMS é o método recomendado de migração para as cargas de trabalho da sua empresa.

Se utilizar os Serviços de Integração de Servidores SQL (SSIS) no seu Servidor SQL nas instalações, A DMS ainda não suporta o catálogo SSIS migratório (SSISDB) que armazena pacotes SSIS, mas pode providenciar o tempo de execução de integração Azure-SSIS (IR) na Azure Data Factory (ADF) que criará um novo SSISDB num caso gerido e depois pode recolocar os seus pacotes para ele, ver [Create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Para saber mais sobre este cenário e etapas de configuração para DMS, consulte [a sua base de dados de informação no local para obter exemplos usando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURO nativo a partir do URL

RESTAURAR as cópias de segurança nativas (.ficheiros bak) retirados de uma instância sql Server, disponível no [Azure Storage,](https://azure.microsoft.com/services/storage/)é uma das principais capacidades da SQL Managed Instance que permite uma migração rápida e fácil da base de dados offline.

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![migração-fluxo](./media/migrate-to-instance-from-sql-server/migration-flow.png)

A tabela a seguir fornece mais informações sobre os métodos que pode utilizar dependendo da versão do SQL Server de origem que está a executar:

|Passo|Motor e versão SQL|Método de backup / restauro|
|---|---|---|
|Coloque backup no Azure Storage|Prior SQL 2012 SP1 CU2|Faça upload do ficheiro .bak diretamente para o armazenamento do Azure|
||2012 SP1 CU2 - 2016|Backup direto utilizando sintaxe despreordenada [com sintaxe CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 e acima|Backup direto utilizando [COM CREDENCIAL SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurar do armazenamento Azure para o caso gerido|[RESTAURAR A PARTIR DE URL com CREDENTIAL SAS](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Ao migrar uma base de dados protegida pela [Encriptação de Dados Transparente](../database/transparent-data-encryption-tde-overview.md) para uma instância gerida utilizando a opção de restauro nativo, o certificado correspondente a partir do local ou do Azure VM SQL Server precisa de ser migrado antes da restauração da base de dados. Para etapas detalhadas, consulte [O cert do TDE migrado para a instância gerida](tde-certificate-migrate.md)
> - A restauração das bases de dados do sistema não é suportada. Para migrar objetos de nível de instância (armazenados em bases de dados master ou msdb), recomendamos que os roteiem e execute scripts T-SQL na instância de destino.

Para obter um arranque rápido que mostre como restaurar uma cópia de segurança de uma base de dados para uma instância gerida utilizando uma credencial SAS, consulte [Restaurar de cópia de segurança para um caso gerido](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorizar aplicações

Uma vez concluída a migração para a instância gerida, deve acompanhar o comportamento da aplicação e o desempenho da sua carga de trabalho. Este processo inclui as seguintes atividades:

- [Compare o desempenho da carga de trabalho em execução na Instância Gerida](#compare-performance-with-the-baseline) com a [linha de base de desempenho que criou no servidor SQL de origem](#create-performance-baseline).
- [Monitorize](#monitor-performance) continuamente o desempenho da sua carga de trabalho para identificar potenciais problemas e melhorias.

### <a name="compare-performance-with-the-baseline"></a>Compare o desempenho com a linha de base

A primeira atividade que você precisaria tomar imediatamente após a migração bem sucedida é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho de base. O objetivo desta atividade é confirmar que o desempenho da carga de trabalho na sua instância gerida satisfaz as suas necessidades.

A migração da base de dados para casos geridos mantém as definições da base de dados e o seu nível de compatibilidade original na maioria dos casos. As definições originais são preservadas sempre que possível para reduzir o risco de algumas degradações de desempenho em comparação com o seu Servidor SQL de origem. Se o nível de compatibilidade de uma base de dados de utilizadores fosse igual ou superior a 100 antes da migração, permanece o mesmo após a migração. Se o nível de compatibilidade de uma base de dados de utilizadores fosse de 90 antes da migração, na base de dados atualizada, o nível de compatibilidade é fixado para 100, que é o nível de compatibilidade suportado mais baixo em caso gerido. O nível de compatibilidade das bases de dados do sistema é de 140. Uma vez que a migração para casos geridos está realmente a migrar para a versão mais recente do motor de base de dados sql Server, deve estar ciente de que precisa de re-testar o desempenho da sua carga de trabalho para evitar alguns problemas de desempenho surpreendentes.

Como pré-requisito, certifique-se de que completou as seguintes atividades:

- Alinhar as definições em instância gerida com as definições da origem SQL Server, investigando várias instâncias, base de dados, definições de temdb e configurações. Certifique-se de que não alterou as definições como níveis de compatibilidade ou encriptação antes de executar a primeira comparação de desempenho, ou aceite o risco de que algumas das novas funcionalidades que ativou possam afetar algumas consultas. Para reduzir os riscos de migração, altere o nível de compatibilidade da base de dados apenas após a monitorização do desempenho.
- Implementar [diretrizes de boas práticas](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) de armazenamento para fins gerais, tais como pré-alocar o tamanho dos ficheiros para obter um melhor desempenho.
- Conheça as [principais diferenças ambientais que podem causar as diferenças de desempenho entre a instância gerida e]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) o SQL Server e identificar os riscos que podem afetar o desempenho.
- Certifique-se de que mantém a loja de consultas ativada e a sintonização automática na sua instância gerida. Estas funcionalidades permitem medir o desempenho da carga de trabalho e corrigir automaticamente os potenciais problemas de desempenho. Aprenda a utilizar a Query Store como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da mudança de nível de compatibilidade da base de dados, como explicado na [estabilidade de desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Uma vez preparado o ambiente que é comparável tanto quanto possível com o seu ambiente no local, pode começar a executar a sua carga de trabalho e medir o desempenho. O processo de medição deve incluir os mesmos parâmetros que mediu [enquanto cria o desempenho de base das suas medidas de carga de trabalho no servidor SQL de origem](#create-performance-baseline).
Como resultado, deve comparar os parâmetros de desempenho com a linha de base e identificar diferenças críticas.

> [!NOTE]
> Em muitos casos, não seria capaz de obter exatamente o desempenho correspondente em instâncias geridas e SQL Server. Azure SQL Managed Instance é um motor de base de dados sql server, mas a infraestrutura e a configuração de alta disponibilidade em instâncias geridas podem introduzir alguma diferença. Pode esperar que algumas consultas sejam mais rápidas, enquanto outras podem ser mais lentas. O objetivo da comparação é verificar que o desempenho da carga de trabalho em instância gerida corresponde ao desempenho no SQL Server (em média), e identificar existem dúvidas críticas com o desempenho que não correspondam ao seu desempenho original.

O resultado da comparação de desempenho pode ser:

- O desempenho da carga de trabalho em instâncias geridas está alinhado ou melhor que o desempenho da carga de trabalho no SQL Server. Neste caso, confirmou com êxito que a migração é bem sucedida.
- A maioria dos parâmetros de desempenho e as consultas no trabalho de carga de trabalho funcionam bem, com algumas exceções com desempenho degradado. Neste caso, é necessário identificar as diferenças e a sua importância. Se houver algumas consultas importantes com desempenho degradado, você deve investigar são os planos SQL subjacentes alterados ou as consultas estão atingindo alguns limites de recursos. A mitigação neste caso poderia ser aplicar algumas dicas sobre as consultas críticas (por exemplo, alterado o nível de compatibilidade, o estimador do legado cardinalício) quer diretamente quer usando guias de planos, reconstruir ou criar estatísticas e índices que possam afetar os planos.
- A maioria das consultas são mais lentas em casos geridos em comparação com o seu servidor SQL de origem. Neste caso, tente identificar as causas da diferença, tais como atingir algum limite de [recursos,]( resource-limits.md#service-tier-characteristics) como limites de IO, limite de memória, limite de taxa de registo de casos, etc. Se não houver limites de recursos que possam causar a diferença, tente alterar o nível de compatibilidade da base de dados ou alterar as definições da base de dados, como a estimativa do legado cardinalício e reinicie o teste. Reveja as recomendações fornecidas por exemplos geridos ou vistas da Loja de Consultas para identificar as consultas que regrediram no desempenho.

> [!IMPORTANT]
> A Azure SQL Managed Instance tem uma funcionalidade de correção de plano automático incorporada que é ativada por padrão. Esta funcionalidade garante que as consultas que funcionavam bem na pasta não se degradariam no futuro. Certifique-se de que esta funcionalidade está ativada e que executou a carga de trabalho tempo suficiente com as definições antigas antes de alterar novas definições de modo a permitir que a instância gerida possa conhecer o desempenho e os planos de base.

Faça a alteração dos parâmetros ou atualize os níveis de serviço para convergir para a configuração ideal até obter o desempenho da carga de trabalho que se adequa às suas necessidades.

### <a name="monitor-performance"></a>Monitorizar desempenho

A SQL Managed Instance fornece um monte de ferramentas avançadas para monitorização e resolução de problemas, e deve usá-las para monitorizar o desempenho no seu caso. Alguns dos parâmetros que o seu precisaria de monitorizar são:

- A utilização do CPU no caso para determinar se o número de vCores que a provisionou corresponde à sua carga de trabalho.
- Esperança de vida de página no seu caso gerido para determinar [se precisa de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Aguarde estatísticas como `INSTANCE_LOG_GOVERNOR` ou que dirá se tem `PAGEIOLATCH` problemas de IO de armazenamento, especialmente no nível de Finalidade Geral, onde poderá precisar de pré-alocar ficheiros para obter um melhor desempenho de IO.

## <a name="leverage-advanced-paas-features"></a>Alavancar recursos de PaaS avançados

Uma vez que esteja numa plataforma totalmente gerida e tenha verificado que os desempenhos da carga de trabalho estão a corresponder ao desempenho da carga de trabalho do SEU SQL Server, aproveite as vantagens que são fornecidas automaticamente como parte do serviço.

Mesmo que não faça algumas alterações na sua gestão durante a migração, existem altas probabilidades de ligar algumas das novas funcionalidades enquanto estiver a operar o seu caso para tirar partido das mais recentes melhorias no motor da base de dados. Algumas alterações só são ativadas uma vez alterado o [nível de compatibilidade](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)da base de dados .

Por exemplo, não é preciso criar cópias de segurança em caso gerido - o serviço realiza cópias de segurança automaticamente para si. Já não te deves preocupar em agendar, tirar e gerir os backups. A Gestão de Instâncias proporciona-lhe a capacidade de restaurar a qualquer ponto no tempo dentro deste período de retenção utilizando [Point in Time Recovery (PITR)](../database/recovery-using-backups.md#point-in-time-restore). Além disso, não precisa de se preocupar em criar alta disponibilidade, uma vez [que a alta disponibilidade](../database/high-availability-sla.md) é incorporada.

Para reforçar a segurança, considere a utilização da autenticação do [Diretório Ativo Azure,](../database/security-overview.md) [a auditoria,](auditing-configure.md) [a deteção de ameaças,](../database/advanced-data-security.md) [a segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)e a máscara dinâmica de [dados).](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, a instância gerida fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorizar e afinar a sua carga de trabalho.](../database/monitor-tune-overview.md) [A analítica Azure SQL](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) permite-lhe monitorizar um grande conjunto de instâncias geridas e centralizar a monitorização de um grande número de casos e bases de dados. [A sintonização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) em instância gerida monitoriza continuamente o desempenho das estatísticas de execução do seu plano SQL e corrige automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a Azure SQL Managed Instance, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Para um tutorial que inclua uma restauração a partir de cópias de segurança, consulte [Criar uma Instância Gerida Azure SQL](instance-create-quickstart.md).
- Para apresentar tutoriales que mostrem migração utilizando DMS, consulte [a base de dados de migração no local para Azure SQL Managed Instance utilizando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).  
