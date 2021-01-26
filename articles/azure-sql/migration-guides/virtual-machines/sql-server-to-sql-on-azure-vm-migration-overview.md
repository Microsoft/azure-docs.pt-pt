---
title: Sql Server para SQL Server em Azure VM (Visão geral da migração)
description: Saiba mais sobre as diferentes estratégias de migração quando pretende migrar o seu SQL Server para o SQL Server em VMs Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 0eabb48aabcb50557b342385068807eb67a9b165
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797856"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Visão geral da migração: SQL Server para SQL Server em VMs Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Conheça as diferentes estratégias de migração para migrar o seu SQL Server para SQL Server em Azure Virtual Machines (VMs). 

Pode migrar o SQL Server a funcionar no local ou em:

- SQL Server nas Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de Base de Dados Relacional da Amazon (AWS RDS) 
- Motor compute (Plataforma Google Cloud - GCP)

Para outros cenários, consulte o [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/) 

## <a name="overview"></a>Descrição geral

Migrar para [o SQL Server em Azure Virtual Machines (VMs)](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) quando pretender utilizar o ambiente familiar do SQL Server com controlo DE SISTEMA, e quer tirar partido das funcionalidades fornecidas pela nuvem, tais como alta disponibilidade de VM incorporada, [cópias de segurança automatizadas](../../virtual-machines/windows/automated-backup.md)e [remendos automatizados.](../../virtual-machines/windows/automated-patching.md) 

Poupe os custos trazendo a sua própria licença com o [modelo de licenciamento Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) ou prolongue o suporte para o SQL Server 2008 e SQL Server 2008 R2 através da [obtenção de atualizações de segurança gratuitas.](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md) 


## <a name="choose-appropriate-target"></a>Escolha o alvo apropriado

As Máquinas Virtuais Azure funcionam em muitas regiões diferentes do Azure e também oferecem uma variedade de tamanhos de [máquinas](../../../virtual-machines/sizes.md) e [opções de armazenamento.](../../../virtual-machines/disks-types.md) Ao determinar o tamanho correto de VM e Armazenamento para a carga de trabalho do seu Servidor SQL, consulte as Diretrizes de Desempenho do [SQL Server em Máquinas Virtuais Azure.](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance) Para determinar o tamanho e os requisitos de armazenamento em VM para a sua carga de trabalho. recomenda-se que estes sejam dimensionados através de uma avaliação Performance-Based [Azure Migrate .](../../../migrate/concepts-assessment-calculation.md#types-of-assessments) Se esta não for uma opção disponível, consulte o seguinte artigo sobre a criação da sua própria [linha de base para desempenho](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Deve também ser feita uma análise da correta instalação e configuração do SQL Server num VM. Recomenda-se a utilização da [galeria de imagens de máquinas virtuais Azure SQL,](../../virtual-machines/windows/create-sql-vm-portal.md) pois isto permite-lhe criar um SQL Server VM com a versão, edição e sistema operativo certos. Isto também registará automaticamente o Azure VM com o [Fornecedor de Recursos](../../virtual-machines/windows/create-sql-vm-portal.md) do Servidor SQL, permitindo funcionalidades como backups automatizados e patching automatizado.

## <a name="migration-strategies"></a>Estratégias de migração

Existem duas estratégias de migração para migrar as bases de dados dos seus utilizadores para um exemplo do SQL Server em VMs Azure: **migrar,** **levantar e deslocar.** 

A abordagem adequada para o seu negócio normalmente depende dos seguintes fatores: 

- Tamanho e escala de migração
- Velocidade de migração
- Suporte de aplicação para alteração de código
- Precisa alterar a versão do servidor SQL, o sistema operativo ou ambos.
- Ciclo de vida de suporte dos seus produtos existentes
- Janela para tempo de inatividade de aplicação durante a migração

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="tempo de inatividade da migração da máquina virtual":::

O quadro que se segue descreve diferenças nas duas estratégias de migração:
<br />

| **Estratégia de migração** | **Descrição** | **Quando utilizar** |
| --- | --- | --- |
| **Mudança de & de elevador** | Utilize a estratégia de migração de elevação e mudança para mover todo o Servidor SQL físico ou virtual da sua localização atual para uma instância do SQL Server em Azure VM sem alterações no sistema operativo, ou versão SQL Server. Para completar uma migração de elevador e mudança, consulte [Azure Migrate.](../../../migrate/migrate-services-overview.md) <br /><br /> O servidor de origem permanece on-line e os pedidos de serviços enquanto o servidor de origem e destino sincronizam os dados permitindo uma migração quase perfeita. | Use para migrações individuais a muito largas, mesmo aplicáveis a cenários como a saída do data center. <br /><br /> Alterações mínimas a nenhuma das alterações de código necessárias às bases de dados ou aplicações SQL do utilizador, permitindo migrações globais mais rápidas. <br /><br />Não são necessárias medidas adicionais para a migração dos serviços de Business Intelligence, tais como [SSIS,](/sql/integration-services/sql-server-integration-services) [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)e [SSAS.](/analysis-services/analysis-services-overview) |
|**Migrate** | Utilize uma estratégia de migração quando pretender atualizar o servidor sql alvo e/ou a versão do sistema operativo. <br /> <br /> Selecione um Azure VM do Azure Marketplace ou uma imagem preparada do SQL Server que corresponda à versão source SQL Server. | Utilize quando houver um requisito ou desejo de usar funcionalidades disponíveis em versões mais recentes do SQL Server, ou se houver um requisito para atualizar as versões legacy SQL Server e/ou OS que já não estão no suporte.  <br /> <br /> Pode exigir algumas alterações de aplicação ou de base de dados de utilizador para suportar a atualização do SQL Server. <br /><br />Pode haver considerações adicionais para os serviços de [Inteligência Empresarial](#business-intelligence) migratórios se no âmbito da migração. |


## <a name="lift-and-shift"></a>Migração lift-and-shift  

A tabela a seguir detalha o método disponível para a estratégia de migração **de elevadores e mudanças** para migrar a sua base de dados SQL Server para SQL Server em VMs Azure:
<br />

|**Método** | **Versão de origem mínima** | **Versão-alvo mínima** | **Restrição do tamanho da cópia de segurança de origem** |  **Notas** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Limite de armazenamento Azure VM](../../../index.yml) |  O servidor SQL existente a ser movido como é para o exemplo do SQL Server num Azure VM. Pode escalar cargas de carga de migração de até 35.000 VMs. <br /><br /> Os servidores de origem permanecem on-line e os pedidos de manutenção durante a sincronização dos dados do servidor, minimizando o tempo de inatividade. <br /><br /> **Automação & scripts**: Scripts de recuperação do local de [Azure](../../../migrate/how-to-migrate-at-scale.md) e [exemplo de migração e planeamento em escala para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

Devido à facilidade de configuração, a abordagem de migração recomendada é fazer uma cópia de [segurança](/sql/t-sql/statements/backup-transact-sql) do SQL Server nativa localmente e, em seguida, copiar o ficheiro para Azure. Este método suporta bases de dados maiores (>1 TB) para todas as versões do SQL Server a partir de 2008 e cópias de dados maiores (>1 TB). No entanto, para as bases de dados a partir do SQL Server 2014, que são menores do que 1 TB, e que têm uma boa conectividade com o Azure, então a [cópia de segurança do SQL Server para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) é a melhor abordagem. 

Ao migrar as bases de dados do SQL Server para um exemplo do SQL Server em VMs Azure, é importante escolher uma abordagem que se adeque quando precisa de cortar para o servidor alvo, uma vez que isso afeta a janela de tempo de inatividade da aplicação.

Os seguintes dados da tabela detalham todos os métodos disponíveis para migrar a sua base de dados SQL Server para SQL Server em VMs Azure:
<br />

|**Método** | **Versão de origem mínima** | **Versão-alvo mínima** | **Restrição do tamanho da cópia de segurança de origem** | **Notas** |
| --- | --- | --- | --- | --- |
| **[Backup para um ficheiro](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Limite de armazenamento Azure VM](../../../index.yml) |  Esta é uma técnica simples e bem testada para mover bases de dados através de máquinas. Utilize a compressão para minimizar o tamanho da cópia de segurança para transferência. <br /><br /> **Scripts de & de automação**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) e [AzCopy para o armazenamento blob](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12.8 TB para SQL Server 2016, caso contrário 1 TB | Uma forma alternativa de mover o ficheiro de reserva para o VM utilizando o armazenamento Azure. Utilize a compressão para minimizar o tamanho da cópia de segurança para transferência. <br /><br /> **Automação & scripting**:  [T-SQL ou plano de manutenção](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Assistente de Migração de Bases de Dados (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Limite de armazenamento Azure VM](../../../index.yml) |  O [DMA](/sql/dma/dma-overview) avalia o SQL Server no local e, em seguida, atualiza perfeitamente para versões posteriores do SQL Server ou migra para o SQL Server em VMs Azure, Base de Dados SQL Azure ou Azure SQL Managed Instance. <br /><br /> Não deve ser utilizado nas bases de dados de utilizadores ativadas por ficheiros.<br /><br /> O DMA também inclui capacidade para migrar [logins SQL e Windows](/sql/dma/dma-migrateserverlogins) e avaliar [pacotes SSIS.](/sql/dma/dma-assess-ssis) <br /><br /> **Scripting de & de automação**: [Interface de linha de comando](/sql/dma/dma-commandline) |
| **[Separar e prender](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Limite de armazenamento Azure VM](../../../index.yml) | Utilize este método quando planeia [armazenar estes ficheiros utilizando o serviço de armazenamento Azure Blob](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) e anexá-los a uma instância do SQL Server num VM Azure, particularmente útil com bases de dados muito grandes ou quando o tempo de backup e restauro for demasiado longo. <br /><br /> **Script de & de automação**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) e [AzCopy para armazenamento blob](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Envio de registo](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (Apenas Windows) | SQL Server 2008 SP4 (Apenas Windows) | [Limite de armazenamento Azure VM](../../../index.yml) | O envio de registos replica ficheiros de registo transacional a partir do local para uma instância do SQL Server num Azure VM. <br /><br /> Isto proporciona um tempo mínimo de inatividade durante o failover e tem menos configuração sobrecarga do que a configuração de um grupo de disponibilidade Always On. <br /><br /> **Script de & de automação**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Grupo de disponibilidade distribuído](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limite de armazenamento Azure VM](../../../index.yml) |  Um [grupo de disponibilidade distribuído](/sql/database-engine/availability-groups/windows/distributed-availability-groups) é um tipo especial de grupo de disponibilidade que abrange dois grupos de disponibilidade separados. Os grupos de disponibilidade que participam num grupo de disponibilidade distribuído não precisam de estar no mesmo local e incluem suporte de domínio cruzado. <br /><br /> Este método minimiza o tempo de inatividade, utiliza-se quando tem um grupo de disponibilidade configurado no local. <br /><br /> **Script de & de automação**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> Para grandes transferências de dados sem opções de rede, consulte [Grandes transferências de dados com conectividade limitada.](../../../storage/common/storage-solution-large-dataset-low-network.md)
> 

### <a name="considerations"></a>Considerações

Segue-se uma lista de pontos-chave a ter em conta na análise dos métodos de migração:

- Para obter um desempenho ótimo de transferência de dados, migrar bases de dados e ficheiros para uma instância do SQL Server em Azure VM utilizando um ficheiro de cópia de segurança comprimido. Para bases de dados maiores, para além da compressão, [divida o ficheiro de backup em ficheiros menores](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) para um desempenho aumentado durante a cópia de segurança e transferência. 
- Se migrar do SQL Server 2014 ou superior, considere [encriptar as cópias de segurança](/sql/relational-databases/backup-restore/backup-encryption) para proteger os dados durante a transferência de rede.
- Para minimizar o tempo de inatividade durante a migração da base de dados, utilize a opção grupo de disponibilidade Always On. 
- Para minimizar o tempo de inatividade sem a sobrecarga de configurar um grupo de disponibilidade, utilize a opção de envio de registos. 
- Para não ter opções de rede, utilize métodos de migração offline, tais como serviços de backup e restauro, ou [serviços de transferência de discos](../../../storage/common/storage-solution-large-dataset-low-network.md) disponíveis no Azure.
- Para alterar também a versão do SQL Server num SqL Server em Azure VM, consulte [a edição do SQL Server](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Business Intelligence 

Pode haver considerações adicionais ao migrar serviços de Inteligência Empresarial do SQL Server fora do âmbito das migrações da base de dados dos utilizadores. 

Estes serviços incluem:

- [**O SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**Serviços de reporte de servidores SQL (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Versões suportadas

Enquanto se prepara para migrar as bases de dados do SQL Server para o SQL Server em VMs Azure, certifique-se de considerar as versões do SQL Server que são suportadas. Para obter uma lista das versões atuais do SQL Server suportadas em VMs Azure, consulte [o SQL Server em VMs Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

## <a name="migration-assets"></a>Ativos de migração 

Para assistência adicional, consulte os seguintes recursos que foram desenvolvidos para projetos de migração no mundo real.

|Recurso  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo.|
|[Automação de recolha de dados perfmon usando Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Uma ferramenta que recolhe dados de realização para entender o desempenho de base que ajuda na recomendação do alvo de migração. Esta ferramenta que utiliza logman.exe para criar o comando que irá criar, iniciar, parar e eliminar contadores de desempenho definidos num servidor SQL remoto.|
|[Implementação do servidor SQL em Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/SQL%20Server%20Deployment%20in%20Azure%20.pdf)|Esta orientação em branco ajuda na revisão de várias opções para mover as cargas de trabalho do seu SQL Server para Azure, incluindo comparação de funcionalidades, alta disponibilidade e considerações de backup/armazenamento. |
|[Servidor SQL no local para a máquina virtual Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/OnPremise%20SQL%20Server%20to%20Azure%20VM.pdf)|Este whitepaper descreve os passos para fazer backup e restaurar bases de dados do SQL Server para o SQL Server na máquina virtual Azure utilizando scripts de amostra.|
|[Multiple-SQL-VM-VNet-ILB](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/ARM%20Templates/Multiple-SQL-VM-VNet-ILB)|Este whitepaper descreve os passos para configurar várias máquinas virtuais Azure numa configuração do Grupo SQL Sempre No Grupo de Disponibilidade.|
|[Máquinas virtuais Azure que suportam Ultra SSD por Região](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Find%20Azure%20VMs%20supporting%20Ultra%20SSD)|Estes scripts PowerShell fornecem uma opção programática para recuperar a lista de regiões que suportam máquinas virtuais Azure que suportam Ultra SSDs.|

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.

## <a name="next-steps"></a>Próximos passos

Para começar a migrar as bases de dados do SQL Server para o SQL Server em VMs Azure, consulte o [guia de migração individual](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)da base de dados . 

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de migração de dados e dados, bem como tarefas especiais, consulte o serviço de [artigos e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre o Azure SQL consulte:
   - [Opções de implementação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server nas VMs do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para obter informações sobre licenciamento, consulte
   - [Traga a sua própria licença com o Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte alargado gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).