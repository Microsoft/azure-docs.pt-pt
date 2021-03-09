---
title: Sql Server para SQL Server em VMs Azure (guia de migração)
description: Siga este guia para migrar as bases de dados individuais do SQL Server para o SQL Server em Azure Virtual Machines (VMs).
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: d95da29b732e2d520b3413628c9b4a1c403abed6
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488265"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Guia de migração: SQL Server para SQL Server em VMs Azure 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Este guia de migração ensina-o a **descobrir,** **avaliar** e **migrar** as bases de dados dos seus utilizadores do SQL Server para uma instância do SQL Server em Azure Virtual Machines (VMs) utilizando o envio de cópias de segurança e restauro e registo utilizando o [Assistente de Migração da Base de Dados (DMA)](/sql/dma/dma-overview) para avaliação. 

Pode migrar o SQL Server a funcionar no local ou em:

- SQL Server nas Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de Base de Dados Relacional da Amazon (AWS RDS) 
- Motor compute (Plataforma Google Cloud - GCP)

Para obter informações sobre estratégias de migração adicionais, consulte a visão geral da [migração do SQL Server VM](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Fluxo de processo de migração":::

## <a name="prerequisites"></a>Pré-requisitos

Migrar para o SQL Server em VMs Azure requer o seguinte: 

- [Assistente de Migração de Bases de Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- Um [projeto Azure Migrate.](../../../migrate/create-manage-projects.md)
- Um sql server de alvo preparado [no Azure VM](../../virtual-machines/windows/create-sql-vm-portal.md) que é a mesma versão ou maior do que o sql server de origem.
- [Conectividade entre Azure e no local.](/azure/architecture/reference-architectures/hybrid-networking)
- [Escolher uma estratégia de migração adequada.](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)

## <a name="pre-migration"></a>Pré-migração

Antes de iniciar a sua migração, descubra a topologia do seu ambiente SQL e avalie a viabilidade da sua migração pretendida. 

### <a name="discover"></a>Detetar

A Azure Migrate avalia a adequação da migração de computadores no local, realiza dimensionamento baseado no desempenho e fornece estimativas de custos para correr no local. Para planear a migração, utilize o Azure Migrate para [identificar as fontes de dados existentes e detalhes sobre as funcionalidades que as suas](../../../migrate/concepts-assessment-calculation.md) instâncias do SQL Server utilizam. Este processo envolve a digitalização da rede para identificar todas as suas instâncias do SQL Server na sua organização com a versão e funcionalidades em uso. 

> [!IMPORTANT]
> Ao escolher uma máquina virtual Azure alvo para a sua instância SQL Server, não se esqueça de considerar [as diretrizes de desempenho do SQL Server em VMs Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Para obter ferramentas adicionais de descoberta, consulte [serviços e ferramentas](../../../dms/dms-tools-matrix.md#business-justification-phase) disponíveis para cenários de migração de dados.


### <a name="assess"></a>Avaliação

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Depois de ter descoberto todas as fontes de dados, utilize o [Data Migration Assistant (DMA)](/sql/dma/dma-overview) para avaliar as instâncias do SQL Server no local, migrando para um caso de SQL Server em Azure VM para entender as lacunas entre a origem e os casos-alvo. 


> [!NOTE]
> Se _não_ estiver a atualizar a versão do SQL Server, salte este passo e mude-se para a secção [de migração.](#migrate) 


#### <a name="assess-user-databases"></a>Avaliar bases de dados de utilizadores 

O Data Migration Assistant (DMA) ajuda a sua migração para uma plataforma de dados moderna, detetando problemas de compatibilidade que podem ter impacto na funcionalidade da base de dados na sua nova versão do SQL Server. O DMA recomenda melhorias de desempenho e fiabilidade para o seu ambiente alvo e também permite mover os seus objetos de esquema, dados e login do seu servidor de origem para o servidor alvo.

Consulte a [avaliação](/sql/dma/dma-migrateonpremsql) para saber mais. 


> [!IMPORTANT]
>Com base no tipo de avaliação, as permissões necessárias no servidor SQL de origem podem ser diferentes. 
   > - Para o orientador de **paridade de funcionalidades,** as credenciais fornecidas para ligar à base de dados do SQL Server de origem devem ser um membro da função do servidor *sysadmin.*
   > - Para o consultor de questões de compatibilidade, as credenciais fornecidas devem ter, pelo `CONNECT SQL` menos, `VIEW SERVER STATE` e `VIEW ANY DEFINITION` permissões.
   > - A DMA irá destacar as permissões necessárias para o conselheiro escolhido antes de executar a avaliação.


#### <a name="assess-applications"></a>Avaliar pedidos

Normalmente, uma camada de aplicação acede às bases de dados dos utilizadores para persistir e modificar dados.  A DMA pode avaliar a camada de acesso a dados de uma aplicação de duas formas: 

- Utilizando [eventos estendidos capturados](/sql/relational-databases/extended-events/extended-events) ou vestígios de perfis de [servidor SQL ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) das bases de dados dos seus utilizadores. Também pode utilizar o [Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-capture-trace) para criar um registo de vestígios que também pode ser utilizado para testes A/B.

- Utilizando o [Data Access Migration Toolkit (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT), que fornece a descoberta e avaliação das consultas SQL dentro do código e é usado para migrar código fonte de aplicação de uma plataforma de base de dados para outra. Esta ferramenta suporta uma variedade de tipos de ficheiros populares, incluindo C# e Java, XML e Plaint Text. Para um guia sobre como realizar uma avaliação DAMT consulte o blog [Use DMAT.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430)

Utilize o DMA para [importar](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) ficheiros de vestígios capturados ou ficheiros DAMT durante a avaliação das bases de dados dos utilizadores. 


#### <a name="scale-assessments"></a>Avaliações de escala

Se tiver vários servidores que necessitem de uma avaliação de DMA, pode automatizar o processo através da interface da linha de [comando](/sql/dma/dma-commandline). Utilizando a interface, pode preparar os comandos de avaliação com antecedência para cada instância do SQL Server no âmbito de migração. 

Para relatórios sumários em grandes propriedades, as avaliações do Data Migration Assistant (DMA) podem agora ser [consolidadas em Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-dma"></a>Bases de dados do refactor com DMA

Com base nos resultados da avaliação do DMA, poderá ter uma série de recomendações para garantir que a sua base de dados de utilizador funciona e funciona corretamente após a migração. A DMA fornece detalhes sobre os objetos impactados, bem como recursos para como resolver cada problema. Recomenda-se que todas as mudanças de rutura e mudanças de comportamento sejam resolvidas antes da migração da produção.

Para funcionalidades precotadas, pode optar por executar a sua(s) base de dados de utilizador(s) no modo de [compatibilidade](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) original, caso deseje evitar efetuar estas alterações e acelerar a migração. No entanto, isto evitará [a atualização da sua compatibilidade na base de dados](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) até que os itens precotados sejam resolvidos.

Recomenda-se vivamente que todas as correções de DMA sejam escritas e aplicadas na base de dados do Servidor SQL alvo durante [a pós-migração](#post-migration).

> [!CAUTION]
> Nem todas as versões SQL Server suportam todos os modos de compatibilidade. Verifique se a [versão do seu servidor SQL alvo](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) suporta a compatibilidade da base de dados escolhida. Por exemplo, o SQL Server 2019 não suporta bases de dados com compatibilidade de nível 90 (que é SQL Server 2005). Estas bases de dados exigiriam, pelo menos, uma atualização para o nível de compatibilidade 100.
>

## <a name="migrate"></a>Migrate

Depois de ter concluído as etapas de pré-migração, está pronto para migrar as bases de dados e componentes do utilizador. Migrar as suas bases de dados utilizando o seu método de [migração](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)preferido.  

O seguinte fornece passos para a realização de uma migração usando backup e restauro, ou uma migração mínima de tempo de inatividade usando backup e restaurar juntamente com o envio de registos. 

### <a name="backup-and-restore"></a>Cópia de segurança e restauro

Para realizar uma migração padrão usando backup e restaurar, siga estes passos: 

1. Confiússe a conectividade com o servidor SQL alvo no Azure VM, com base nos seus requisitos. Consulte [a Ligação a uma máquina virtual do servidor SQL em Azure (Gestor de Recursos)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Faça uma pausa/pare quaisquer aplicações que utilizem bases de dados destinadas à migração. 
1. Certifique-se de que a base de dados do utilizador está inativa utilizando [o modo de utilizador único](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Execute uma cópia de segurança completa da base de dados para um local no local.
1. Copie os ficheiros de backup no local para o seu VM utilizando o desktop remoto, [o Azure Data Explorer](/azure/data-explorer/data-explorer-overview)ou o utilitário da linha de comando [AZCopy](../../../storage/common/storage-use-azcopy-v10.md) (> cópias de segurança recomendadas por 2-TB).
1. Restaurar a cópia de segurança completa da base de dados para o SQL Server em Azure VM.

### <a name="log-shipping--minimize-downtime"></a>Envio de registo (minimizar tempo de inatividade)

Para realizar uma migração mínima de tempo de inatividade utilizando backup, restauro e envio de registo, siga estes passos: 

1. Confiússe a conectividade para direcionar o SQL Server no Azure VM, com base nos seus requisitos. Consulte [a Ligação a uma máquina virtual do servidor SQL em Azure (Gestor de Recursos)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Certifique-se de que a base de dados do utilizador no local a migrar está em modelo de recuperação completo ou a granel.
1. Execute uma cópia de segurança completa da base de dados para um local no local e modifique quaisquer trabalhos de backups de bases de dados existentes para usar [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) palavra-chave para preservar a cadeia de registos.
1. Copie os ficheiros de backup no local para o seu VM utilizando o desktop remoto, [o Azure Data Explorer](/azure/data-explorer/data-explorer-overview)ou o utilitário da linha de comando [AZCopy](../../../storage/common/storage-use-azcopy-v10.md) (>cópias de segurança recomendadas por 1-TB).
1. Restaurar a cópia de segurança completa da base de dados no SqL Server em Azure VM.
1. Confiúva [de envio](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) de registo entre a base de dados no local e o servidor SQL alvo no Azure VM. Certifique-se de que não reinicia a base de dados, uma vez que esta já foi concluída nos passos anteriores.
1. **Corte** para o servidor alvo. 
   1. Faça uma pausa/paragem de aplicações utilizando bases de dados a migrar. 
   1. Certifique-se de que a base de dados do utilizador está inativa utilizando [o modo de utilizador único](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. Quando estiver pronto, execute uma [falha de](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) envio de registo controlada da base de dados no local para atingir o SQL Server em Azure VM.



### <a name="migrating-objects-outside-user-databases"></a>Objetos migratórios fora da base de dados dos utilizadores

Pode haver objetos adicionais do SQL Server que são necessários para o funcionamento perfeito das bases de dados dos seus utilizadores após a migração. 

O quadro que se segue fornece uma lista de componentes e métodos de migração recomendados que podem ser concluídos antes ou depois da migração das bases de dados do utilizador: 


| **Funcionalidade** | **Componente** | **Métodos de Migração** |
| --- | --- | --- |
| **Bases de dados** | Modelação  | Script com SQL Server Management Studio |
|| TempDB | Planeie mover o TempDB para [o disco temporário Azure VM (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)para melhor desempenho. Certifique-se de escolher um tamanho VM que tenha um SSD local suficiente para acomodar o seu TempDB. |
|| Bases de dados de utilizadores com Filestream |  Utilize o [Backup e restaure](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) os métodos de migração. A DMA não suporta bases de dados com o Filestream. |
| **Segurança** | Logins de Servidor SQL e Windows | Utilize o DMA para [migrar os logins dos utilizadores](/sql/dma/dma-migrateserverlogins). |
|| Funções do Servidor SQL | Script com SQL Server Management Studio |
|| Fornecedores criptográficos | Recomenda [a conversão para utilizar o Serviço de Cofre de Chave Azure](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Este procedimento utiliza o [fornecedor de recursos SQL VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Objetos de servidor** | Dispositivos de backup | Substitua-o por cópia de segurança de base utilizando [o Serviço de Backup Azure](../../../backup/backup-sql-server-database-azure-vms.md) ou escreva cópias de segurança para o [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). Este procedimento utiliza o [fornecedor de recursos SQL VM](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Servidores Ligados | Script com SQL Server Management Studio. |
|| Desencadeia os gatilhos do servidor | Script com SQL Server Management Studio. |
| **Replicação** | Publicações Locais | Script com SQL Server Management Studio. |
|| Assinantes Locais | Script com SQL Server Management Studio. |
| **Polybase** | Polybase | Script com SQL Server Management Studio. |
| **Gestão** | Correio de Base de Dados | Script com SQL Server Management Studio. |
| **Agente do SQL Server** | Tarefas | Script com SQL Server Management Studio. |
|| Alertas | Script com SQL Server Management Studio. |
|| Operadores | Script com SQL Server Management Studio. |
|| Proxies | Script com SQL Server Management Studio. |
| **Sistema operativo** | Ficheiros, ações de ficheiros | Tome nota de quaisquer ficheiros adicionais ou ações de ficheiros que sejam utilizados pelos seus Servidores SQL e se reproduzam no alvo Azure VM. |


## <a name="post-migration"></a>Pós-migração

Depois de ter concluído com sucesso a fase de migração, passe por uma série de tarefas pós-migração para garantir que tudo esteja funcionando da forma mais suave e eficiente possível.

### <a name="remediate-applications"></a>Aplicações corretivas

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. Realizar isto pode, em alguns casos, exigir alterações nas aplicações.

Aplicar todas as correções recomendadas do Assistente de Migração da Base de Dados na base de dados do utilizador. Recomenda-se que estes sejam scriptados para garantir a consistência e permitir a automação.

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio para a migração da base de dados consiste na realização das seguintes atividades:

1. **Desenvolver testes de validação.**  Utilize consultas SQL para testar migrações de bases de dados. Crie consultas de validação para correr contra as bases de dados de origem e alvo. As suas consultas de validação devem abranger o âmbito que definiu.
2. **Crie ambiente de teste.**  O ambiente de teste deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.
3. **Fazer testes de validação.**  Executar os testes de validação contra a fonte e o alvo e, em seguida, analisar os resultados.
4. **Fazer testes de desempenho.**  Executar o teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

> [!TIP]
> Utilize o [Assistente de Experimentação da Base de Dados (DEA)](/sql/dea/database-experimentation-assistant-overview) para ajudar a avaliar o desempenho do servidor SQL alvo.
>

### <a name="optimize"></a>Otimização

A fase de migração pós-migração é crucial para conciliar quaisquer problemas com precisão e completude de dados, bem como para abordar potenciais problemas de desempenho com a carga de trabalho.

Para obter mais informações sobre estas questões e medidas específicas para os mitigar, consulte os seguintes recursos:

- [Guia de Validação e Otimização pós-migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Desempenho de sintonização em Máquinas Virtuais Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Centro de otimização de custos Azure](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Passos seguintes

- Para verificar a disponibilidade de serviços aplicáveis ao SQL Server consulte o centro de [infraestruturas Azure Global](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

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