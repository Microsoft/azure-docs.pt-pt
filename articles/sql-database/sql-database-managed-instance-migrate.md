---
title: Migrar a instância do SQL Server para instância gerida da base de dados do Azure SQL | Documentos da Microsoft
description: Saiba como migrar uma instância do SQL Server para instância gerida da base de dados do Azure SQL.
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
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315580"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Instância gerida de migração de instância do SQL Server para a base de dados do Azure SQL

Neste artigo, ficará a conhecer os métodos para migrar um SQL Server 2005 ou uma instância de versão posterior [instância gerida de base de dados do Azure SQL](sql-database-managed-instance.md). Para obter informações sobre a migração para um único banco de dados ou conjunto elástico, consulte [migrar para uma base de dados individual ou agrupada](sql-database-cloud-migrate.md). Para informações de migração sobre a migração de outras plataformas, consulte [guia de migração de base de dados do Azure](https://datamigration.microsoft.com/).

Num alto nível, o processo de migração de base de dados é semelhante a:

![Processo de migração](./media/sql-database-managed-instance-migration/migration-process.png)

- [Avaliar a compatibilidade da instância gerida](#assess-managed-instance-compatibility)
- [Escolha a opção de conectividade da aplicação](sql-database-managed-instance-connect-app.md)
- [Implementar uma instância gerida de forma ideal de tamanho](#deploy-to-an-optimally-sized-managed-instance)
- [Selecione o método de migração e migrar](#select-migration-method-and-migrate)
- [Monitorizar aplicações](#monitor-applications)

> [!NOTE]
> Para migrar uma base de dados individual num único banco de dados ou conjunto elástico, consulte [migrar uma base de dados do SQL Server para a base de dados do Azure SQL](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Avaliar a compatibilidade da instância gerida

Em primeiro lugar, determine se geridos instância é compatível com os requisitos da base de dados da sua aplicação. A opção de implementação de instância gerida foi concebida para proporcionar fácil migração lift- and -shift uma migração para a maioria dos aplicativos existentes que utilizam o SQL Server no local ou em máquinas virtuais. No entanto, às vezes, poderá precisar de funcionalidades ou capacidades que ainda não são suportadas e o custo da implementação de uma solução alternativa sejam demasiado altas.

Uso [Assistente de migração de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) para detetar potencial que podem afetar funcionalidade de banco de dados na base de dados do Azure SQL de problemas de compatibilidade. DMA não ainda suporte geridas instância como destino de migração, mas é recomendado que execute a avaliação na base de dados do Azure SQL e reveja com cuidado a lista de paridade de funcionalidades comunicadas e problemas de compatibilidade em relação a documentação do produto. Ver [funcionalidades de base de dados do Azure SQL](sql-database-features.md) para verificar existem relataram alguns problemas de bloqueio que não bloqueadores na instância gerida, uma vez que a maioria dos problemas de bloqueio impedir uma migração para a base de dados do Azure SQL foram removida com geridos instância. Para a instância, funcionalidades, como consultas entre bases de dados, transações entre bases de dados dentro da instância do mesmo, servidor ligado a outras tabelas de temporárias globais com origens, o CLR, de SQL, os modos de exibição de nível de instância, o Service Broker e similares estão disponíveis em instâncias geridas.

Se existirem relataram alguns problemas de bloqueio que não são removidos com a opção de implementação de instância gerida, poderá ter de considerar uma opção alternativa, tal como [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Eis alguns exemplos:

- Se necessitar de acesso direto para o sistema operativo ou o sistema de ficheiros, por exemplo, a instalação de terceiros ou agentes personalizados na mesma máquina virtual com o SQL Server.
- Se tiver dependência rígida nas funcionalidades que ainda não são suportadas, por exemplo, o FileStream / FileTable, o PolyBase e instância para várias transações.
- Se absolutamente necessárias para permanecer numa versão específica do SQL Server (2012, por exemplo).
- Se os requisitos de computação são muito mais baixos do que a instância gerida oferece (um vCore, por exemplo) e consolidação de base de dados não é a opção aceitável.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementar uma instância gerida dimensionada de forma otimizada

Instância gerida é ideal para cargas de trabalho no local que pretende mover para a cloud. Ele introduz um [novo modelo de compra](sql-database-service-tiers-vcore.md) que fornece maior flexibilidade para selecionar o nível certo de recursos das cargas de trabalho. No mundo no local, provavelmente está acostumado a estas cargas de trabalho de dimensionamento com núcleos físicos e largura de banda de e/s. O modelo de compra para a instância gerida baseia-se após núcleos virtuais, ou "vCores," com o armazenamento adicional e e/s disponíveis separadamente. O modelo de vCore é um simples maneira de compreender os requisitos de computação na cloud versus o que utiliza no local hoje mesmo. Esse novo modelo permite-lhe dimensionar o ambiente de destino na cloud.

Pode selecionar a computação e recursos de armazenamento na implementação de tempo e, em seguida, altere-a posteriormente sem introduzir tempo de inatividade do seu aplicativo com o [portal do Azure](sql-database-scale-resources.md):

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

Controlar o comportamento do aplicativo e o desempenho após a migração. Na instância gerida, algumas alterações são ativadas apenas uma vez a [nível de compatibilidade de base de dados foi alterado](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Migração de base de dados para o Azure SQL Database mantém seu nível de compatibilidade original na maioria dos casos. Se o nível de compatibilidade de uma base de dados do utilizador foi 100 ou superior antes da migração, permanece igual após a migração. Se o nível de compatibilidade de uma base de dados do utilizador foi 90 antes da migração, na base de dados atualizada, o nível de compatibilidade é definido para 100, que é o nível mais baixo suportado compatibilidade na instância gerida. Nível de compatibilidade de bases de dados do sistema é 140.

Para reduzir riscos de migração, altere o nível de compatibilidade de base de dados apenas depois de monitorização do desempenho. Store de consulta de utilização como uma ferramenta ideal para obter informações sobre o desempenho da carga de trabalho antes e depois da alteração de nível de compatibilidade da base de dados, conforme explicado [manter a estabilidade de desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Assim que estiver numa plataforma totalmente gerida, demorar vantagens que são fornecidas automaticamente como parte integrante do serviço de base de dados SQL. Por exemplo, não precisa de criar cópias de segurança na instância gerida - o serviço faz cópias de segurança para automaticamente. Já não deve se preocupar sobre agendar, levando e gerir cópias de segurança. Instância gerida fornece-lhe a capacidade de restaurar para qualquer ponto anterior no tempo dentro com do período de retenção [ponto no tempo de recuperação (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Além disso, não é necessário se preocupar sobre como configurar a elevada disponibilidade como [elevada disponibilidade](sql-database-high-availability.md) baseia-se.

Para reforçar a segurança, considere a utilização de algumas das funcionalidades que estão disponíveis:

- O Azure ao nível da base de dados a autenticação do Active Directory
- Uso [funcionalidades de segurança avançadas](sql-database-security-overview.md) como [auditoria](sql-database-managed-instance-auditing.md), [deteção de ameaças](sql-database-advanced-data-security.md), [segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), e [dinâmico máscara de dados](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ) para proteger a sua instância.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre as instâncias geridas, consulte [o que é uma instância gerida?](sql-database-managed-instance.md).
- Para obter um tutorial que inclua um restauro da cópia de segurança, consulte [criar uma instância gerida](sql-database-managed-instance-get-started.md).
- Para a migração do tutorial de apresentação com o DMS, consulte [migrar a sua base de dados no local para a instância gerida com o DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
