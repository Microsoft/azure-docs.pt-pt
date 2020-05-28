---
title: Migrar do SQL Server para uma instância gerida azure SQL
description: Saiba como migrar uma base de dados do SQL Server para a Instância Gerida Azure SQL.
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
ms.openlocfilehash: 27336028fb6b141d8ece4d975b7590b3daf875f2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050962"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migração por instância do Servidor SQL para a instância gerida do Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo, você aprende sobre os métodos para migrar um SQL Server 2005 ou posterior instância de versão para [A Instância Gerida Azure SQL](sql-managed-instance-paas-overview.md). Para obter informações sobre a migração para uma única base de dados ou piscina elástica, consulte [Migrar para uma única base de dados ou agrupara-se](../database/migrate-to-database-from-sql-server.md). Para obter informações sobre migração de outras plataformas, consulte o Guia de Migração da Base de [Dados Azure.](https://datamigration.microsoft.com/)

> [!NOTE]
> Se quiser começar rapidamente e experimentar o Azure SQL Managed Instance, é melhor ir ao [guia quick-start](quickstart-content-reference-guide.md) em vez desta página.

A um nível elevado, o processo de migração da base de dados parece:

![processo de migração](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Avalie a compatibilidade da Instância Gerida SQL](#assess-sql-managed-instance-compatibility) onde deve garantir que não existem problemas de bloqueio que possam impedir as suas migrações.
  - Este passo também inclui a criação de linha de base de [desempenho](#create-performance-baseline) para determinar a utilização do recurso na sua fonte SQL Server. Este passo é necessário se pretender implementar uma instância gerida por SQL de tamanho adequado e verificar se os desempenhos após a migração não são afetados.
- [Escolha opções de conectividade de aplicativos](connect-application-instance.md)
- [Desloque-se para uma instância gerida por SQL](#deploy-to-an-optimally-sized-managed-instance) de tamanho ótimo, onde irá escolher características técnicas (número de vCores, quantidade de memória) e nível de desempenho (Business Critical, General Purpose) da sua Instância Gerida SQL.
- [Selecione o método de migração e emigra](#select-migration-method-and-migrate) onde migra as suas bases de dados utilizando a migração offline (backup/restauro nativo, importação/exportação) ou migração online (Serviço de Migração de Dados, Replicação Transacional).
- [Monitorize as aplicações](#monitor-applications) para garantir que tem um desempenho esperado.

> [!NOTE]
> Para migrar uma base de dados individual para uma única base de dados ou piscina elástica, consulte [Migrar uma base de dados do SQL Server para a Base de Dados Azure SQL](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Avaliar a compatibilidade da instância gerida pela SQL

Em primeiro lugar, determine se a Instância Gerida sQL é compatível com os requisitos da base de dados da sua aplicação. O SQL Managed Instance foi concebido para fornecer migração fácil para a maioria das aplicações existentes que utilizam o SQL Server no local ou em máquinas virtuais. No entanto, pode, por vezes, necessitar de funcionalidades ou capacidades que ainda não são suportadas e o custo de implementação de uma suticultura é demasiado elevado.

Utilize o Assistente de [Migração de Dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) para detetar potenciais problemas de compatibilidade que impactam a funcionalidade da base de dados na Base de Dados Azure SQL. Se houver alguns problemas de bloqueio relatados, poderá ter de considerar uma opção alternativa, como o [SQL Server em máquinas virtuais Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Eis alguns exemplos:

- Se necessitar de acesso direto ao sistema operativo ou sistema de ficheiros, por exemplo, para instalar agentes de terceiros ou personalizados na mesma máquina virtual com o SQL Server.
- Se tiver uma dependência estrita de funcionalidades que ainda não são suportadas, tais como FileStream /FileTable, PolyBase e transações de instâncias cruzadas.
- Se precisar absolutamente de ficar numa versão específica do SQL Server (2012, por exemplo).
- Se os seus requisitos de computação forem muito mais baixos que as ofertas de instância gerida (um vCore, por exemplo) e a consolidação da base de dados não são uma opção aceitável.

Se tiver resolvido todos os bloqueadores de migração identificados e continuar a migração para a SQL Managed Instance, note que algumas das alterações podem afetar o desempenho da sua carga de trabalho:

- O modelo de recuperação completa obrigatório e o calendário regular de backup automatizado podem ter impacto no desempenho das suas ações de carga de trabalho ou manutenção/ETL se tiver usado periodicamente um modelo simples/a granel ou parar backups a pedido.
- Configurações diferentes de nível de servidor ou base de dados, tais como trace flags ou níveis de compatibilidade
- Novas funcionalidades que está a usar, tais como encriptação de base de dados transparente (TDE) ou grupos de falha automática podem afetar o uso de CPU e IO.

A SQL Managed Instance garante uma disponibilidade de 99,99% mesmo em cenários críticos, pelo que a sobrecarga causada por estas funcionalidades não pode ser desativada. Para mais informações, consulte [as causas de raiz que podem causar diferentes desempenhos no SQL Server e na Instância Gerida Azure SQL](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Criar base de desempenho

Se precisar de comparar o desempenho da sua carga de trabalho em instância gerida com a sua carga de trabalho original em execução no SQL Server, terá de criar uma linha de base de desempenho que será usada para comparação.

A linha de base de desempenho é um conjunto de parâmetros como o uso médio/max CPU, latência média/max dI do disco, latência de IO de disco médio/max, entrada, IOPS, esperança média/má guarda, tamanho máximo médio de temperatura. Gostaria de ter parâmetros semelhantes ou ainda melhores após a migração, pelo que é importante medir e registar os valores de base para estes parâmetros. Além dos parâmetros do sistema, é necessário selecionar um conjunto de consultas representativas ou as consultas mais importantes na sua carga de trabalho e medir a duração min/média/máx, utilização de CPU para as consultas selecionadas. Estes valores permitir-lhe-iam comparar o desempenho da carga de trabalho em execução em instância gerida com os valores originais no seu Servidor SQL de origem.

Alguns dos parâmetros que precisaria de medir na sua instância do Servidor SQL são:

- Monitorize a utilização do [CPU na sua instância do Servidor SQL](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e grave a utilização média e máxima do CPU.
- [Monitorize o uso da memória na sua instância do Servidor SQL](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) e determine a quantidade de memória utilizada por diferentes componentes, tais como piscina tampão, cache de plano, piscina de loja de colunas, [OLTP em memória,](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)etc. Além disso, deve encontrar valores médios e máximos do contador de desempenho da memória da Esperança de Vida da Página.
- Monitorize a utilização do disco IO na instância de origem SQL Server utilizando [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) visualização ou contadores de [desempenho](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorize a carga de trabalho e o desempenho da consulta ou a sua instância de Servidor SQL examinando pontos de vista de gestão dinâmica ou consulta se estiver a migrar da versão SQL Server 2016+. Identifique a duração média e o uso de CPU das consultas mais importantes na sua carga de trabalho para compará-las com as consultas que estão em execução na instância gerida.

> [!Note]
> Se notar algum problema com a sua carga de trabalho no SQL Server, tais como alta utilização de CPU, pressão constante de memória, problemas de templídeo ou parametrização, deve tentar resolvê-los na sua fonte SQL Server antes de tomar a linha de base e migração. A migração conhece problemas para qualquer novo sistema que provoque resultados inesperados e invalide qualquer comparação de desempenho.

Como resultado desta atividade, deve ter documentado valores médios e de pico para cpu, memória e utilização de IO no seu sistema de origem, bem como duração média e má e utilização de CPU do dominante e das consultas mais críticas na sua carga de trabalho. Deverá utilizar estes valores mais tarde para comparar o desempenho da sua carga de trabalho em instância gerida com o desempenho base da carga de trabalho no Servidor SQL de origem.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Desdobre para uma instância gerida de tamanho ótimo

A SQL Managed Instance é adaptada para cargas de trabalho no local que estão a planear mover-se para a nuvem. Introduz um [novo modelo](../database/service-tiers-vcore.md) de compra que proporciona uma maior flexibilidade na seleção do nível certo de recursos para as suas cargas de trabalho. No mundo das instalações, você provavelmente está habituado a dimensionar estas cargas de trabalho usando núcleos físicos e largura de banda IO. O modelo de compra para instância gerida baseia-se em núcleos virtuais, ou "vCores", com armazenamento adicional e IO disponíveis separadamente. O modelo vCore é uma forma mais simples de compreender os seus requisitos de computação na nuvem contra o que usa hoje no local. Este novo modelo permite-lhe dimensionar o seu ambiente de destino na nuvem. Algumas diretrizes gerais que podem ajudá-lo a escolher o nível de serviço e características certas são descritos aqui:

- Com base no uso de CPU de base, pode fornecer uma instância gerida que corresponda ao número de núcleos que está a utilizar no SQL Server, tendo em conta que as características do CPU podem ter de ser dimensionadas para corresponder às [características vm onde a instância gerida é instalada](resource-limits.md#hardware-generation-characteristics).
- Com base no uso da memória de base, escolha [o nível de serviço que tem memória correspondente](resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser escolhida diretamente, pelo que teria de selecionar a instância gerida com a quantidade de vCores que tem memória correspondente (por exemplo 5,1 GB/vCore em Gen5).
- Com base na latência iO de base do subsistema de ficheiros, escolha entre o Propósito Geral (latência superior a 5ms) e os níveis de serviço Business Critical (latência inferior a 3 ms).
- Com base na entrada de base pré-alocar o tamanho dos dados ou ficheiros de registo para obter o desempenho esperado da OI.

Pode escolher os recursos de computação e armazenamento no momento de implantação e depois troque-o sem introduzir tempo de inatividade para a sua aplicação utilizando o [portal Azure:](../database/scale-resources.md)

![tamanho de instância gerida](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Para aprender a criar a infraestrutura VNet e um exemplo gerido, consulte [Criar uma instância gerida](instance-create-quickstart.md).

> [!IMPORTANT]
> É importante manter o seu destino VNet e subnet sempre de acordo com [os requisitos de VNet](connectivity-architecture-overview.md#network-requirements)de instância gerida . Qualquer incompatibilidade pode impedi-lo de criar novos casos ou de usar aqueles que já criou. Saiba mais sobre [a criação](virtual-network-subnet-create-arm-template.md) de redes novas e [configurando as redes existentes.](vnet-existing-add-subnet.md)

## <a name="select-migration-method-and-migrate"></a>Selecione método de migração e emigrar

A SQL Managed Instance visa cenários de utilizadores que requerem migração em massa de bases de dados a partir de instalações ou implementações de bases de dados Azure VM. São a escolha ideal quando é necessário levantar e deslocar a parte de trás das aplicações que usam regularmente funcionalidades de nível de instância e/ou de base de dados cruzadas. Se este é o seu cenário, pode mover uma instância inteira para um ambiente correspondente em Azure sem a necessidade de re-arquiteto suas aplicações.

Para mover instâncias SQL, precisa de planear cuidadosamente:

- A migração de todas as bases de dados que precisam de ser colonadas (as que estão a funcionar no mesmo caso)
- A migração de objetos de nível de instância de que a sua aplicação depende, incluindo logins, credenciais, empregos e operadores de agente SQL e gatilhos de nível de servidor.

O SQL Managed Instance é um serviço gerido que lhe permite delegar algumas das atividades regulares da DBA na plataforma à medida que estão incorporadas. Por isso, alguns dados de nível de instância não precisam de ser migrados, tais como trabalhos de manutenção para backups regulares ou sempre na configuração, uma vez que a [alta disponibilidade](../database/high-availability-sla.md) é incorporada.

A SQL Managed Instance suporta as seguintes opções de migração de bases de dados (atualmente estes são os únicos métodos de migração suportados):

- Serviço de Migração de Bases de Dados Azure - migração com tempo de paragem quase nulo,
- Nativo `RESTORE DATABASE FROM URL` - usa backups nativos do SQL Server e requer algum tempo de inatividade.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

O Serviço de Migração de Bases de [Dados Azure (DMS)](../../dms/dms-overview.md) é um serviço totalmente gerido projetado para permitir migrações perfeitas de várias fontes de base de dados para plataformas Azure Data com o mínimo de tempo de inatividade. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de terceiros e servidores SQL para o Azure. As opções de implementação na pré-visualização pública incluem bases de dados em bases de dados Azure SQL e bases de dados do Servidor SQL numa Máquina Virtual Azure. DMS é o método recomendado de migração para as suas cargas de trabalho empresariais.

Se utilizar os Serviços de Integração do Servidor SQL (SSIS) no seu Servidor SQL nas instalações, O DMS ainda não suporta o catálogo SSIS migratório (SSISDB) que armazena pacotes SSIS, mas pode fornecer o Runtime de Integração Azure-SSIS (IR) na Azure Data Factory (ADF) que irá criar um novo SSISDB numa instância gerida e depois pode redistribuir os seus pacotes para ele, ver [Create Azure-SSIS IR em ADF.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

Para saber mais sobre este cenário e etapas de configuração para DMS, consulte [Migrate a sua base de dados no local para gerir a instância usando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURO nativo a partir do URL

Restaurar as cópias de segurança nativas (ficheiros.bak) tiradas do SQL Server no local ou [do Servidor SQL em Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponível no Armazenamento [Azure,](https://azure.microsoft.com/services/storage/)é uma das principais capacidades da SQL Managed Instance que permite uma migração rápida e fácil da base de dados offline.

O diagrama seguinte fornece uma visão geral de alto nível do processo:

![fluxo migratório](./media/migrate-to-instance-from-sql-server/migration-flow.png)

A tabela seguinte fornece mais informações sobre os métodos que pode utilizar dependendo da versão Source SQL Server que está a executar:

|Passo|Motor e versão SQL|Backup / Restaurar método|
|---|---|---|
|Coloque backup no Armazenamento Azure|Prior SQL 2012 SP1 CU2|Upload .bak file diretamente para o armazenamento Azure|
||2012 SP1 CU2 - 2016|Backup direto usando [sintaxe](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) credencial depreciada|
||2016 e acima|Backup direto usando [com credential com SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurar do armazenamento azure para a instância gerida|[Restaurar DO URL com credential SAS](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Ao migrar uma base de dados protegida pela [Encriptação transparente](../database/transparent-data-encryption-tde-overview.md) de dados para uma instância gerida utilizando a opção de restauro nativo, o certificado correspondente a partir das instalações ou do Servidor Azure VM SQL precisa de ser migrado antes da restauração da base de dados. Para passos detalhados, consulte [Migrate TDE cert para instância gerida](tde-certificate-migrate.md)
> - A restauração das bases de dados do sistema não é suportada. Para migrar objetos de nível de instância (armazenados em bases de dados master ou msdb), recomendamos que os scripts T-SQL na instância de destino.

Para um início rápido, mostrando como restaurar uma cópia de segurança da base de dados para uma instância gerida utilizando uma credencial SAS, consulte [Restaurar de reserva para uma instância gerida](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorizar aplicações

Uma vez concluída a migração para a instância gerida, deve acompanhar o comportamento da aplicação e o desempenho da sua carga de trabalho. Este processo inclui as seguintes atividades:

- [Compare o desempenho da carga de trabalho em execução na Instância Gerida](#compare-performance-with-the-baseline) com a linha de base de [desempenho que criou na fonte SQL Server](#create-performance-baseline).
- [Monitorize](#monitor-performance) continuamente o desempenho da sua carga de trabalho para identificar potenciais problemas e melhorias.

### <a name="compare-performance-with-the-baseline"></a>Comparar o desempenho com a linha de base

A primeira atividade que você precisaria tomar imediatamente após uma migração bem sucedida é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho de base. O objetivo desta atividade é confirmar que o desempenho da carga de trabalho na sua instância gerida satisfaz as suas necessidades.

A migração da base de dados para a instância gerida mantém as definições da base de dados e o seu nível de compatibilidade original na maioria dos casos. As definições originais são preservadas sempre que possível para reduzir o risco de algumas degradações de desempenho em comparação com a fonte Do Servidor SQL. Se o nível de compatibilidade de uma base de dados de utilizadores fosse 100 ou superior antes da migração, permanece o mesmo após a migração. Se o nível de compatibilidade de uma base de dados de utilizador fosse de 90 antes da migração, na base de dados atualizada, o nível de compatibilidade é fixado para 100, que é o nível de compatibilidade suportado mais baixo em instância gerida. O nível de compatibilidade das bases de dados do sistema é de 140. Uma vez que a migração para a instância gerida está realmente a migrar para a versão mais recente do motor de base de dados SQL Server, deve estar ciente de que precisa de voltar a testar o desempenho da sua carga de trabalho para evitar alguns problemas de desempenho surpreendentes.

Como pré-requisito, certifique-se de que completou as seguintes atividades:

- Alinhe as suas definições em instância gerida com as definições da instância de Origem SQL Server, investigando várias instâncias, bases de dados, configurações temdb e configurações. Certifique-se de que não alterou definições como níveis de compatibilidade ou encriptação antes de executar a primeira comparação de desempenho, ou aceite o risco de algumas das novas funcionalidades que ativou poderem afetar algumas consultas. Para reduzir os riscos de migração, altere o nível de compatibilidade da base de dados apenas após a monitorização do desempenho.
- Implemente as diretrizes de [boas práticas](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) de armazenamento para fins gerais, como pré-atribuição do tamanho dos ficheiros para obter um melhor desempenho.
- Conheça as [principais diferenças ambientais que podem causar as diferenças]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) de desempenho entre a instância gerida e o SQL Server e identificar os riscos que podem afetar o desempenho.
- Certifique-se de que mantém ativada a Consulta Store e a sintonização automática na sua instância gerida. Estas funcionalidades permitem-lhe medir o desempenho da carga de trabalho e corrigir automaticamente os potenciais problemas de desempenho. Saiba como utilizar a Query Store como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da alteração do nível de compatibilidade da base de dados, como explicado no [Keep performance stability durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Depois de ter preparado o ambiente que é comparável tanto quanto possível com o seu ambiente no local, pode começar a executar a sua carga de trabalho e medir o desempenho. O processo de medição deve incluir os mesmos parâmetros que mediu enquanto cria o [desempenho básico das suas medidas de carga de trabalho na fonte SQL Server](#create-performance-baseline).
Como resultado, deve comparar parâmetros de desempenho com a linha de base e identificar diferenças críticas.

> [!NOTE]
> Em muitos casos, não seria capaz de obter um desempenho exatamente correspondente em instância gerida e SQL Server. O Azure SQL Managed Instance é um motor de base de dados SQL Server, mas a infraestrutura e a configuração de alta disponibilidade em instância gerida podem introduzir alguma diferença. Seria de esperar que algumas consultas fossem mais rápidas, enquanto outras podem ser mais lentas. O objetivo da comparação é verificar se o desempenho da carga de trabalho em instância gerida corresponde ao desempenho no SQL Server (em média), e identificar existem algumas dúvidas críticas com o desempenho que não correspondem ao seu desempenho original.

O resultado da comparação de desempenho pode ser:

- O desempenho da carga de trabalho em instância gerida está alinhado ou melhor que o desempenho da carga de trabalho no Servidor SQL. Neste caso, confirmou com sucesso que a migração é bem sucedida.
- A maioria dos parâmetros de desempenho e as consultas na carga de trabalho funcionam bem, com algumas exceções com desempenho degradado. Neste caso, teria de identificar as diferenças e a sua importância. Se houver algumas consultas importantes com desempenho degradado, deve investigar se os planos SQL subjacentes alterados ou as consultas estão a atingir alguns limites de recursos. A mitigação neste caso poderia ser aplicar algumas dicas sobre as consultas críticas (por exemplo, alterar o nível de compatibilidade, estimador de cardinalidade legado) quer diretamente ou usando guias de planos, reconstruir ou criar estatísticas e índices que possam afetar os planos.
- A maioria das consultas são mais lentas em caso gerido em comparação com o seu Servidor SQL fonte. Neste caso, tente identificar as causas da diferença, tais como [atingir algum limite]( resource-limits.md#service-tier-characteristics) de recursos como limites de IO, limite de memória, limite de taxa de registo de instâncias, etc. Se não houver limites de recursos que possam causar a diferença, tente alterar o nível de compatibilidade da base de dados ou alterar as definições de base de dados como estimativa de cardinalidade legado e reiniciar o teste. Reveja as recomendações fornecidas por instância gerida ou vistas da Consulta Store para identificar as consultas que regrediram o desempenho.

> [!IMPORTANT]
> A Instância Gerida azure SQL tem uma função de correção automática de planos incorporada que está ativada por padrão. Esta funcionalidade garante que as consultas que funcionaram bem na pasta não se degradariam no futuro. Certifique-se de que esta funcionalidade está ativada e que executou a carga de trabalho tempo suficiente com as definições antigas antes de alterar novas definições de modo a permitir que a instância gerida aprenda sobre o desempenho e planos de base.

Faça a alteração dos parâmetros ou dos níveis de serviço de upgrade para convergir para a configuração ideal até obter o desempenho da carga de trabalho que se adequa às suas necessidades.

### <a name="monitor-performance"></a>Monitorizar desempenho

O SQL Managed Instance fornece um monte de ferramentas avançadas para monitorização e resolução de problemas, e você deve usá-las para monitorizar o desempenho na sua instância. Alguns dos parâmetros que a sua necessidade de monitorizar são:

- A utilização do CPU na instância para determinar o número de vCores que você provisionou é a combinação certa para a sua carga de trabalho.
- A esperança de vida de página na sua instância gerida para determinar [se precisa de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Espere estatísticas como ou que dirão que tem problemas de IO de `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` armazenamento, especialmente no nível De Propósito Geral, onde poderá ser necessário pré-atribuir ficheiros para obter um melhor desempenho io.

## <a name="leverage-advanced-paas-features"></a>Alavancar funcionalidades avançadas do PaaS

Uma vez que você está em uma plataforma totalmente gerida e você verificou que os desempenhos de carga de trabalho estão correspondendo ao desempenho da carga de trabalho do SQL Server, aproveite as vantagens que são fornecidas automaticamente como parte do serviço.

Mesmo que não faça algumas alterações no caso gerido durante a migração, existem grandes probabilidades de ligar algumas das novas funcionalidades enquanto está a operar a sua instância para tirar partido das mais recentes melhorias no motor da base de dados. Algumas alterações só são ativadas depois de alterado o [nível](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)de compatibilidade da base de dados .

Por exemplo, não é necessário criar backups em caso de gestão - o serviço executa cópias de segurança automaticamente para si. Já não se deve preocupar com agendamento, tomada e gestão de backups. A Instância Gerida fornece-lhe a capacidade de restaurar a qualquer ponto do tempo neste período de retenção utilizando [o Point in Time Recovery (PITR)](../database/recovery-using-backups.md#point-in-time-restore). Além disso, não precisa de se preocupar em criar alta disponibilidade, uma vez que a [alta disponibilidade](../database/high-availability-sla.md) é incorporada.

Para reforçar a segurança, considere a utilização da [Autenticação de Diretório Ativo Azure,](../database/security-overview.md) [Auditoria,](auditing-configure.md) [deteção de ameaças,](../database/advanced-data-security.md) [segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)e máscara de dados [dinâmicos.](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, a instância gerida fornece um conjunto de ferramentas avançadas que podem ajudá-lo a monitorizar e afinar a [sua carga de trabalho.](../database/monitor-tune-overview.md) [A análise do Azure SQL](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) permite-lhe monitorizar um grande conjunto de instâncias geridas e centralizar a monitorização de um grande número de instâncias e bases de dados. [A sintonização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) em instância gerida monitoriza continuamente o desempenho das estatísticas de execução do seu plano SQL e corrige automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre a Instância Gerida Azure SQL, consulte [o que é a Instância Gerida azure SQL?](sql-managed-instance-paas-overview.md)
- Para um tutorial que inclui um restauro de backup, consulte [Create a Azure SQL Managed Instance](instance-create-quickstart.md).
- Para um tutorial que mostre migração utilizando DMS, consulte [Migrar a sua base de dados no local para o Azure SQL Managed Instance utilizando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).  
