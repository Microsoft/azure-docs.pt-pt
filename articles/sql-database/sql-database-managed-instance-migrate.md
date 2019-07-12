---
title: Migrar base de dados de instância do SQL Server para o SQL Database do Azure - instância gerida | Documentos da Microsoft
description: Saiba como migrar uma base de dados de instância do SQL Server para o SQL Database do Azure - instância gerida.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 11/07/2019
ms.openlocfilehash: 7cf54b79fac87905117e321574571890c59315e6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827067"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Instância gerida de migração de instância do SQL Server para a base de dados do Azure SQL

Neste artigo, ficará a conhecer os métodos para migrar um SQL Server 2005 ou uma instância de versão posterior [instância gerida de base de dados do Azure SQL](sql-database-managed-instance.md). Para obter informações sobre a migração para um único banco de dados ou conjunto elástico, consulte [migrar para uma base de dados individual ou agrupada](sql-database-cloud-migrate.md). Para informações de migração sobre a migração de outras plataformas, consulte [guia de migração de base de dados do Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Se quiser inicie rapidamente e experimente a instância gerida, pode querer aceda à [guia de início rápido](/sql-database-managed-instance-quickstart-guide.md) em vez de nesta página. 

Num alto nível, o processo de migração de base de dados é semelhante a:

![Processo de migração](./media/sql-database-managed-instance-migration/migration-process.png)

- [Avaliar a compatibilidade da instância gerida](#assess-managed-instance-compatibility) onde deve garantir que não existem não existem problemas de bloqueio que podem impedir que as migrações.
  - Este passo também inclui a criação de [linha de base de desempenho](#create-performance-baseline) para determinar a utilização de recursos na sua instância do SQL Server de origem. Este passo é necessário se pretender que o implementar corretamente dimensionada de instância gerida e certifique-se de que os desempenhos após a migração não são afetados.
- [Escolha as opções de conectividade da aplicação](sql-database-managed-instance-connect-app.md)
- [Implementar uma instância gerida tamanho ideal](#deploy-to-an-optimally-sized-managed-instance) onde escolherá características técnicas (número de vCores, quantidade de memória) e o escalão de desempenho (crítico para a empresa, para fins gerais) da sua instância gerida.
- [Selecione o método de migração e migrar](#select-migration-method-and-migrate) onde migra as bases de dados através de uma migração offline (cópia de segurança/restauro nativo, importe/exportação de base de dados) ou migração online (serviço de migração de dados, replicação transacional).
- [Monitorizar aplicações](#monitor-applications) para se certificar de que tem o desempenho esperado.

> [!NOTE]
> Para migrar uma base de dados individual num único banco de dados ou conjunto elástico, consulte [migrar uma base de dados do SQL Server para a base de dados do Azure SQL](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Avaliar a compatibilidade da instância gerida

Em primeiro lugar, determine se geridos instância é compatível com os requisitos da base de dados da sua aplicação. A opção de implementação de instância gerida foi concebida para proporcionar fácil migração lift- and -shift uma migração para a maioria dos aplicativos existentes que utilizam o SQL Server no local ou em máquinas virtuais. No entanto, às vezes, poderá precisar de funcionalidades ou capacidades que ainda não são suportadas e o custo da implementação de uma solução alternativa sejam demasiado altas.

Uso [Assistente de migração de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) para detetar potencial que podem afetar funcionalidade de banco de dados na base de dados do Azure SQL de problemas de compatibilidade. DMA não ainda suporte geridas instância como destino de migração, mas é recomendado que execute a avaliação na base de dados do Azure SQL e reveja com cuidado a lista de paridade de funcionalidades comunicadas e problemas de compatibilidade em relação a documentação do produto. Ver [funcionalidades de base de dados do Azure SQL](sql-database-features.md) para verificar existem relataram alguns problemas de bloqueio que não bloqueadores na instância gerida, uma vez que a maioria dos problemas de bloqueio impedir uma migração para a base de dados do Azure SQL foram removida com geridos instância. Para a instância, funcionalidades, como consultas entre bases de dados, transações entre bases de dados dentro da instância do mesmo, servidor ligado a outras tabelas de temporárias globais com origens, o CLR, de SQL, os modos de exibição de nível de instância, o Service Broker e similares estão disponíveis em instâncias geridas.

Se existirem relataram alguns problemas de bloqueio que não são removidos com a opção de implementação de instância gerida, poderá ter de considerar uma opção alternativa, tal como [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Eis alguns exemplos:

- Se necessitar de acesso direto para o sistema operativo ou o sistema de ficheiros, por exemplo, a instalação de terceiros ou agentes personalizados na mesma máquina virtual com o SQL Server.
- Se tiver dependência rígida nas funcionalidades que ainda não são suportadas, por exemplo, o FileStream / FileTable, o PolyBase e instância para várias transações.
- Se é absolutamente necessário manter-se numa versão específica do SQL Server (2012, por exemplo).
- Se os requisitos de computação são muito mais baixos do que a instância gerida oferece (um vCore, por exemplo) e consolidação de base de dados não é a opção aceitável.

Se resolver que todos identificado bloqueadores de migração e continuar a migração para a instância gerida, tenha em atenção de que algumas das alterações podem afetar o desempenho da carga de trabalho:
- Modelo de recuperação completo obrigatório e agenda de cópia de segurança automatizada regular poderão afetar o desempenho da sua carga de trabalho ou ações de manutenção/ETL se tiver utilizado o modelo simples/massa periodicamente ou paradas cópias de segurança a pedido.
- Servidor ou base de dados ao nível configurações diferentes, como sinalizadores de rastreio ou níveis de compatibilidade
- Novos recursos que está a utilizar, tais como grupos de encriptação de base de dados transparente (TDE) ou a ativação pós-falha automática poderão afetar a utilização da CPU e e/s.

Gerido 99,99% de garantia de disponibilidade mesmo em cenários críticos, da instância, pelo que não é possível desativar a sobrecarga causada por estas funcionalidades. Para obter mais informações, consulte [as causas de raiz que podem causar o desempenho de diferente no SQL Server e instância gerida](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Criar linha de base de desempenho

Se precisar de comparar o desempenho da carga de trabalho na instância gerida com a carga de trabalho original em execução no SQL Server, terá de criar uma linha de base de desempenho que será utilizada para comparação. 

Linha de base de desempenho é um conjunto de parâmetros, como a utilização da CPU média/máx, latência de e/s de disco de média/máx, débito, IOPS, expectativa de vida da página de média/máx, média de tamanho máximo de tempdb. Gostaria de ter parâmetros semelhante ou melhores, mesmo após a migração, pelo que é importante medir e registar os valores de linha de base para estes parâmetros. Além de parâmetros de sistema, terá de selecionar um conjunto de consultas representativas ou as consultas mais importantes na sua carga de trabalho e medida média/min/max a duração, a utilização da CPU para consultas selecionadas. Estes valores permitiria comparar o desempenho da carga de trabalho em execução na instância gerida para os valores originais na sua origem de SQL Server.

Alguns dos parâmetros que seria necessário medir na sua instância do SQL Server são: 
- [Monitorizar a utilização da CPU na sua instância do SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e a média de registos e picos de utilização da CPU.
- [Monitorizar a utilização de memória na sua instância do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) e determinar a quantidade de memória utilizada por diferentes componentes, tais como o pool de buffers, planejar o cache, o conjunto de arquivo de colunas, [OLTP dentro da memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017), etc. Além disso, deve encontrar valores de média e horas de pico de contador de desempenho de memória de expectativa de vida da página.
- Monitorizar a utilização de e/s de disco no uso de instância de origem do SQL Server [DM io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) vista ou [contadores de desempenho](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorizar o desempenho de consulta e carga de trabalho ou instância do SQL Server, examinando a vistas de gestão dinâmica ou Store de consulta, se estiver migrando do SQL Server 2016 + versão. Identifique a duração média e a utilização da CPU das consultas mais importantes na carga de trabalho para compará-los com as consultas que estejam a executar a instância gerida.

> [!Note]
> Se notar algum problema com a carga de trabalho no SQL Server, como elevada utilização da CPU, a pressão de memória constante, tempdb ou parametrization problemas, deve tentar resolvê-los na sua instância do SQL Server de origem antes de fazer a linha de base e a migração. Migrar sabe problemas para qualquer novo migh de sistema provocar resultados inesperados e invalidar qualquer comparação de desempenho.

Como um resultado dessa atividade deve ter documentou média e valores de pico de CPU, memória e utilização de e/s no seu sistema de origem, bem como duração máxima e média e a utilização da CPU do dominante e as consultas mais críticas em sua carga de trabalho. Deve utilizar estes valores mais tarde para comparar o desempenho da carga de trabalho na instância gerida com o desempenho de linha de base da carga de trabalho na origem do SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementar uma instância gerida dimensionada de forma otimizada

Instância gerida é ideal para cargas de trabalho no local que pretende mover para a cloud. Ele introduz um [novo modelo de compra](sql-database-service-tiers-vcore.md) que fornece maior flexibilidade para selecionar o nível certo de recursos das cargas de trabalho. No mundo no local, provavelmente está acostumado a estas cargas de trabalho de dimensionamento com núcleos físicos e largura de banda de e/s. O modelo de compra para a instância gerida baseia-se após núcleos virtuais, ou "vCores," com o armazenamento adicional e e/s disponíveis separadamente. O modelo de vCore é um simples maneira de compreender os requisitos de computação na cloud versus o que utiliza no local hoje mesmo. Esse novo modelo permite-lhe dimensionar o ambiente de destino na cloud. Algumas Diretrizes gerais que podem ajudá-lo a escolher o escalão de serviço correto e características são descritas aqui:
- Com base na linha de base a utilização da CPU pode aprovisionar uma instância gerida que corresponde ao número de núcleos que está a utilizar no SQL Server, tendo em mente que características de CPU poderá ter de ser dimensionada para corresponder ao [características da VM em que é a instância gerida instalado](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Com base na utilização de memória da linha de base escolher [o escalão de serviço que tenha memória correspondente](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). A quantidade de memória não pode ser diretamente escolhida, por isso terá de selecionar a instância gerida com a quantidade de vCores com memória correspondente (por exemplo 5.1 GB/vCore Gen5). 
- Com base na linha de base e/s latência do subsistema de ficheiro de escolher entre (latência superior a 5ms) para fins gerais e crítico para a empresa escalões de serviço (latência inferior a 3 ms).
- Com base no débito de linha de base previamente alocar o tamanho dos dados ou ficheiros de registo para obter esperado o desempenho de e/s.

É possível a computação e recursos de armazenamento na implementação de tempo e, em seguida, alteração-la posteriormente a sem introduzir tempo de inatividade do seu aplicativo com o [portal do Azure](sql-database-scale-resources.md):

![dimensionamento de instância gerida](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Para saber como criar a infraestrutura VNet e uma instância gerida, veja [criar uma instância gerida](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> É importante manter o seu destino VNet e sub-rede sempre em acordo com o [geridos requisitos de VNet de instância](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Qualquer incompatibilidade pode impedi-lo de criar novas instâncias ou utilizando os que já criou. Saiba mais sobre [criar nova](sql-database-managed-instance-create-vnet-subnet.md) e [configurar existente](sql-database-managed-instance-configure-vnet-subnet.md) redes.

## <a name="select-migration-method-and-migrate"></a>Selecione o método de migração e migrar

Os instância gerida opção destinos utilizador cenários de implementação que exija migração de base de dados em massa de IaaS ou no local da base de implementações. Eles são a escolha ideal quando precisar de lift- and -shift de back-end das aplicações que regularmente utilizar o nível de instância e / ou entre bases de dados funcionalidades. Se este for o cenário, pode mover uma instância completa para um ambiente correspondente no Azure sem a necessidade de Reelaborar a seus aplicativos.

Para mover instâncias do SQL, precisa planejar com cuidado:

- A migração de todas as bases de dados que precisam ser colocalizadas (aqueles em execução na mesma instância)
- A migração de objetos de nível de instância que seu aplicativo depende, incluindo inícios de sessão, as credenciais, tarefas de agente do SQL e operadores e acionadores ao nível do servidor.

A instância gerida é um serviço gerido que permite-lhe delegar algumas das atividades DBA regulares para a plataforma, como eles são feitos. Portanto, alguns dados de nível de instância não precisam de ser migrado, por exemplo, tarefas de manutenção de cópias de segurança regulares ou configuração Always On, como [elevada disponibilidade](sql-database-high-availability.md) baseia-se.

Instância gerida suporta as seguintes opções de migração de base de dados (atualmente estes são os métodos de migração suportados apenas):

- Serviço de migração de base de dados do Azure - migração com tempo de inatividade quase inexistente,
- Nativo `RESTORE DATABASE FROM URL` - utiliza cópias de segurança nativas do SQL Server e requer algum período de indisponibilidade.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

O [do Azure Database Migration Service (DMS)](../dms/dms-overview.md) é um serviço completamente gerido criado para ativar migrações totalmente integradas de várias origens de base de dados para plataformas de dados do Azure com o período de indisponibilidade mínimo. Este serviço simplifica as tarefas necessárias para mover de terceiros existente e bases de dados do SQL Server para o Azure. Opções de implementação em pré-visualização pública incluem bases de dados na base de dados do Azure SQL e o SQL Server bases de dados na máquina Virtual do Azure. O DMS é o método de migração recomendado para cargas de trabalho empresariais.

Se utilizar o SQL Server Integration Services (SSIS) no SQL Server no local, DMS ainda não suporta migração catálogo SSIS (SSISDB) que armazena os pacotes do SSIS, mas pode aprovisionar o Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF) que será criar um novo SSISDB numa instância gerida e, em seguida, pode voltar a implementar os pacotes, consulte [criar IR do Azure-SSIS no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Para saber mais sobre estes passos do cenário e a configuração para o DMS, consulte [migrar a sua base de dados no local para a instância gerida com o DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Nativo RESTAURO a partir do URL

RESTAURO de cópias de segurança nativas (ficheiros. bak) retiradas do SQL Server no local ou [SQL Server em máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponível no [armazenamento do Azure](https://azure.microsoft.com/services/storage/), é uma das principais capacidades da implementação da instância gerida opção que permite a migração de base de dados offline rápida e fácil.

O diagrama seguinte fornece uma visão geral do processo:

![fluxo de migração](./media/sql-database-managed-instance-migration/migration-flow.png)

A tabela seguinte fornece mais informações sobre os métodos que pode utilizar dependendo da versão do SQL Server de origem estiver a executar:

|Passo|Motor de SQL e versão|Cópia de segurança / restaurar o método|
|---|---|---|
|Coloque a cópia de segurança para armazenamento do Azure|CU2 de SP1 de 2012 SQL anterior|Carregar o ficheiro. bak diretamente ao armazenamento do Azure|
||2012 SP1 CU2 - 2016|Utilizar cópias de segurança do direto preterida [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) sintaxe|
||2016 e acima|A utilizar a cópia de segurança direto [com CREDENCIAIS de SAS](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurar a partir de armazenamento do Azure para a instância gerida|[Restaurar de URL com a CREDENCIAL de SAS](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Ao migrar uma base de dados protegido pelo [encriptação de dados transparente](transparent-data-encryption-azure-sql.md) para uma instância com a opção Restaurar nativo, o certificado correspondente do IaaS SQL Server ou no local tem de ser migradas antes da base de dados Restaure. Para obter passos detalhados, consulte [cert TDE de migrar para a instância gerida](sql-database-managed-instance-migrate-tde-certificate.md)
> - Restauro de bases de dados do sistema não é suportado. Para migrar objetos do nível de instância (armazenados nas bases de dados mestra ou msdb), recomendamos que o script-os e executar scripts T-SQL na instância de destino.

Para um início rápido que mostra como restaurar uma cópia de segurança da base de dados para uma instância gerida utilizando uma credencial SAS, consulte [restaurar a partir de cópia de segurança para uma instância gerida](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorizar aplicações

Depois de concluir a migração para a instância gerida, deve controlar o comportamento do aplicativo e o desempenho da sua carga de trabalho. Este processo inclui as seguintes atividades:
- [Comparar o desempenho da carga de trabalho em execução na instância gerida](#compare-performance-with-the-baseline) com o [linha de base de desempenho que criou na origem do SQL Server](#create-performance-baseline).
- Continuamente [monitorizar o desempenho da carga de trabalho](#monitor-performance) para identificar potenciais problemas e a melhoria.

### <a name="compare-performance-with-the-baseline"></a>Comparar o desempenho com a linha de base

A primeira atividade, que terá de tomar imediatamente após a migração com êxito é comparar o desempenho da carga de trabalho com o desempenho da carga de trabalho de linha de base. O objetivo desta atividade é confirmar que o desempenho da carga de trabalho na sua instância gerida atende às suas necessidades. 

Migração de base de dados para a instância gerida mantém as definições de base de dados e seu nível de compatibilidade original na maioria dos casos. As configurações originais são preservadas sempre que possível para reduzir o risco de alguns degradações de desempenho em comparação comparada a sua origem de SQL Server. Se o nível de compatibilidade de uma base de dados do utilizador foi 100 ou superior antes da migração, permanece igual após a migração. Se o nível de compatibilidade de uma base de dados do utilizador foi 90 antes da migração, na base de dados atualizada, o nível de compatibilidade é definido para 100, que é o nível mais baixo suportado compatibilidade na instância gerida. Nível de compatibilidade de bases de dados do sistema é 140. Uma vez que a migração para a instância gerida, na verdade, está a migrar para a versão mais recente do motor de base de dados do SQL Server, deve estar ciente de que precisa testar novamente o desempenho da carga de trabalho para evitar alguns problemas de desempenho surpreendente.

Como pré-requisito, certifique-se de que concluiu as seguintes atividades:
- Alinhe as definições na instância gerida com as definições da instância do SQL Server de origem investigando vários instância, da base de dados, temdb definições e configurações. Certifique-se de que não tenha alterado as definições, como níveis de compatibilidade ou encriptação antes de executar a comparação de desempenho de primeira ou aceitar o risco de que alguns dos novos recursos que ativou podem afetar algumas consultas. Para reduzir riscos de migração, altere o nível de compatibilidade de base de dados apenas depois de monitorização do desempenho.
- Implemente [diretrizes práticas recomendadas de armazenamento para fins gerais](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) como alocar previamente o tamanho dos ficheiros para obter o melhor desempenho.
- Saiba mais sobre o [diferenças de ambiente que podem fazer com que as diferenças de desempenho entre a instância gerida e o SQL Server da chave]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) e identificar os riscos que podem afetar o desempenho.
- Certifique-se de que tenha ativado Store de consulta e a otimização automática na sua instância gerida. Estas funcionalidades permitem-lhe medir o desempenho da carga de trabalho e corrigir automaticamente os potenciais problemas de desempenho. Saiba como utilizar o Query Store como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da alteração de nível de compatibilidade da base de dados, conforme explicado na [manter a estabilidade de desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Depois de preparar o ambiente comparável tanto quanto possível com o seu ambiente no local, pode iniciar a execução de sua carga de trabalho e medir o desempenho. Processo de medida deve incluir os mesmos parâmetros, que é medido [enquanto cria o desempenho de linha de base das suas medidas de carga de trabalho na origem do SQL Server](#create-performance-baseline).
Como resultado, deve comparar os parâmetros de desempenho com a linha de base e identificar diferenças críticas.

> [!NOTE]
> Em muitos casos, não seria capaz de obter um desempenho exatamente correspondente na instância gerida e o SQL Server. A instância gerida é um motor de base de dados do SQL Server, mas infraestrutura e a configuração de elevada disponibilidade na instância gerida, podem introduzir alguns diferença. Pode esperar que algumas consultas seria mais rápidas durante algumas outras poderá ser mais lenta. O objetivo de comparação é verificar se o desempenho da carga de trabalho na instância gerida corresponde ao desempenho no SQL Server (em média) e identificar existem quaisquer consultas críticas com o desempenho, que não correspondem ao seu desempenho original.

O resultado da comparação de desempenho pode ser:
- Desempenho da carga de trabalho na instância gerida está alinhado ou melhor que o desempenho da carga de trabalho no SQL Server. Neste caso com êxito tiver confirmado que a migração é concluída com êxito.
- Maioria dos parâmetros de desempenho e as consultas o trabalho de carga de trabalho muito bem, com algumas exceções com degradação do desempenho. Neste caso, seria necessário identificar as diferenças e sua importância. Se existirem algumas importantes consultas com diminuição do desempenho, deve investigar são alterados os planos SQL subjacentes ou as consultas estiverem sendo executados em alguns limites de recursos. Atenuação nesse caso seria aplicam-se algumas dicas sobre as consultas críticas (por exemplo nível de compatibilidade alterados, avaliador de cardinalidade herdado) ou diretamente ou através de guias de plano, reconstruir ou criar estatísticas e índices que podem afetar os planos. 
- A maioria das consultas é mais lenta numa instância gerida em comparação com a sua origem de SQL Server. Neste caso tente identificar as causas de raiz da diferença, como [atingir o limite de alguns recursos]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) como limites de e/s, limite de memória, limite de taxa de registo de instância, etc. Se não existirem não existem limites de recursos que podem causar a diferença, tente alterar o nível de compatibilidade da base de dados ou alterar as definições de base de dados, como a estimativa de cardinalidade herdada e voltar a iniciar o teste. Reveja as recomendações fornecidas pelas vistas de instância gerida ou Store de consulta para identificar as consultas que regredidom desempenho.

> [!IMPORTANT]
> A instância gerida tem a funcionalidade de correção de planos automática incorporada que está ativada por predefinição. Esta funcionalidade garante que a consultas que funcionavam bem no colar não seriam degradar no futuro. Certifique-se de que esta funcionalidade está ativada e que tenha executado a carga de trabalho tempo suficiente com as definições antigas antes de alterar as novas definições para ativar a instância gerida saber mais sobre o desempenho de linha de base e planos.

Faça a alteração dos parâmetros ou atualizar os escalões de serviço para convergir para a configuração ideal, até que obtém o desempenho da carga de trabalho adequada às suas necessidades.

### <a name="monitor-performance"></a>Monitorizar desempenho

Instância gerida fornece muitas ferramentas avançadas de monitorização e resolução de problemas e deve usá-los para monitorizar o desempenho na sua instância. Alguns dos parâmetros que sua seria necessário monitorizar são:
- Utilização da CPU na instância para determinar faz o número de vCores que aprovisionou o é a correspondência correta para sua carga de trabalho.
- Expectativa de vida de página na sua instância gerida para determinar [necessita de memória adicional](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Estatísticas como de espera `INSTANCE_LOG_GOVERNOR` ou `PAGEIOLATCH` informará que se tiver problemas de e/s de armazenamento, especialmente no escalão fins gerais em que poderá ter de previamente alocar ficheiros para obter um melhor desempenho de e/s.

## <a name="leverage-advanced-paas-features"></a>Tirar partido das funcionalidades avançadas de PaaS

Assim que estiver a utilizar uma plataforma totalmente gerida e ter confirmado que os desempenhos de carga de trabalho são correspondentes, desempenho de carga de trabalho do SQL Server, tome as vantagens que são fornecidas automaticamente como parte integrante do serviço de base de dados SQL. 

Mesmo que não faça algumas alterações na instância gerida durante a migração, há chances de alta que se voltaria em alguns dos novos recursos enquanto estiver a utilizar a instância para aproveitar as melhorias de motor de base de dados mais recentes. Algumas alterações só são uma vez ativadas o [nível de compatibilidade de base de dados foi alterado](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Por exemplo, não precisa de criar cópias de segurança na instância gerida - o serviço faz cópias de segurança para automaticamente. Já não deve se preocupar sobre agendar, levando e gerir cópias de segurança. Instância gerida fornece-lhe a capacidade de restaurar para qualquer ponto anterior no tempo dentro com do período de retenção [ponto no tempo de recuperação (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Além disso, não é necessário se preocupar sobre como configurar a elevada disponibilidade como [elevada disponibilidade](sql-database-high-availability.md) baseia-se.

Para reforçar a segurança, considere usar [Azure Active Directory Authentication](sql-database-security-overview.md), [auditoria](sql-database-managed-instance-auditing.md), [deteção de ameaças](sql-database-advanced-data-security.md), [desegurançaemníveldelinha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), e [máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Além da gestão avançada e recursos de segurança, a instância gerida oferece um conjunto de ferramentas avançadas que podem ajudá-lo para [monitorizar e otimizar a sua carga de trabalho](sql-database-monitor-tune-overview.md). [Análise SQL do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) permite-lhe monitorizar um grande conjunto de instâncias geridas e centralizar a monitorização de um grande número de instâncias e bases de dados. [A otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) na instância gerida monitorizar continuamente o desempenho, as estatísticas de execução do plano SQL e corrigir automaticamente os problemas de desempenho identificados.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre as instâncias geridas, consulte [o que é uma instância gerida?](sql-database-managed-instance.md).
- Para obter um tutorial que inclua um restauro da cópia de segurança, consulte [criar uma instância gerida](sql-database-managed-instance-get-started.md).
- Para a migração do tutorial de apresentação com o DMS, consulte [migrar a sua base de dados no local para a instância gerida com o DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
