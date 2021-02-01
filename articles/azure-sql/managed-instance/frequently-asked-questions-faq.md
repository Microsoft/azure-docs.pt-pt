---
title: Perguntas Mais Frequentes (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Perguntas frequentes (FAQ) sobre o Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: d3f859590b30aaeeaebaed7b1f2f85e1a86a92c9
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223411"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) sobre o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as perguntas mais comuns sobre [a Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funcionalidades suportadas

**Onde posso encontrar uma lista de funcionalidades suportadas no SQL Managed Instance?**

Para obter uma lista de funcionalidades suportadas em SQL Managed Instance, consulte [as funcionalidades de Instância Gerida Azure SQL](../database/features-comparison.md).

Para diferenças de sintaxe e comportamento entre Azure SQL Managed Instance e SQL Server, consulte [as diferenças T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Especificação técnica, limites de recursos e outras limitações
 
**Onde posso encontrar características técnicas e limites de recursos para a SQL Managed Instance?**

Para obter as características de geração de hardware disponíveis, consulte [diferenças técnicas nas gerações de hardware](resource-limits.md#hardware-generation-characteristics).
Para os níveis de serviço disponíveis e as suas características, consulte [diferenças técnicas entre os níveis de serviço.](resource-limits.md#service-tier-characteristics)

**Para que nível de serviço sou elegível?**

Qualquer cliente é elegível para qualquer nível de serviço. No entanto, se pretender trocar as licenças existentes por tarifas com desconto em Azure SQL Managed Instance utilizando [o Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/hybrid-benefit/)tenha em mente que os clientes da SQL Server Enterprise Edition com Garantia de Software são elegíveis para os níveis de desempenho [da General Purpose](../database/service-tier-general-purpose.md) ou Business [Critical](../database/service-tier-business-critical.md) e os clientes SQL Server Standard Edition com Garantia de Software são elegíveis apenas para o nível de desempenho para fins gerais. Para mais detalhes, consulte [os direitos específicos da AHB.](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)

**Que tipos de subscrição são suportados para SQL Managed Instance?**

Para a lista de tipos de subscrição suportados, consulte [os tipos de subscrição suportados](resource-limits.md#supported-subscription-types). 

**Quais as regiões de Azure que são apoiadas?**

Casos geridos podem ser criados na maior parte das regiões de Azure; ver [regiões apoiadas para o SQL Managed Instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se necessitar de uma instância gerida numa região que não é suportada, [envie um pedido de apoio através do portal Azure](../database/quota-increase-request.md).

**Existem limitações de quota para implantações de instâncias geridas sql?**

A instância gerida tem dois limites por defeito: limite no número de sub-redes que pode utilizar e um limite no número de vCores que pode prever. Os limites variam entre os tipos e regiões de subscrição. Para a lista de limitações de recursos regionais por tipo de subscrição, consulte a tabela a partir da [limitação regional](resource-limits.md#regional-resource-limitations)de recursos. Estes são limites suaves que podem ser aumentados a pedido. Se precisar de providenciar casos mais geridos nas suas regiões atuais, envie um pedido de apoio para aumentar a quota utilizando o portal Azure. Para obter mais informações, consulte [os aumentos de quota de pedido para a Base de Dados Azure SQL](../database/quota-increase-request.md).

**Posso aumentar o número de bases de dados limite (100) no meu caso gerido a pedido?**

Não, e atualmente não existem planos comprometidos para aumentar o número de bases de dados em SQL Managed Instance. 

**Onde posso migrar se tenho mais de 8TB de dados?**
Pode considerar migrar para outros sabores Azure que se adequam à sua carga de trabalho: [Azure SQL Database Hyperscale](../database/service-tier-hyperscale.md) ou [SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Onde posso migrar se tiver requisitos específicos de hardware, tais como maior rácio RAM para vCore ou mais CPUs?**
Pode considerar migrar para [o SQL Server em Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ou [Azure SQL Database](../database/sql-database-paas-overview.md) memory/cpu otimizado.

## <a name="known-issues-and-defects"></a>Questões e defeitos conhecidos

**Onde posso encontrar problemas e defeitos conhecidos?**

Para defeitos de produto e problemas conhecidos, consulte [questões conhecidas](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Novas funcionalidades

**Onde posso encontrar as últimas funcionalidades e as funcionalidades na pré-visualização pública?**

Para funcionalidades novas e de pré-visualização, consulte [as notas de lançamento](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Criar, atualizar, eliminar ou mover a SQL Gestão de Instâncias

**Como posso providenciar sql Caso Gerido?**

Pode providenciar uma instância a partir do [portal Azure,](instance-create-quickstart.md) [modelos](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates) [PowerShell,](scripts/create-configure-managed-instance-powershell.md) [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) e ARM .

**Posso providenciar casos geridos numa subscrição existente?**

Sim, pode providenciar uma Instância Gerida numa subscrição existente se essa subscrição pertencer aos [tipos de subscrição suportados.](resource-limits.md#supported-subscription-types)

**Por que não posso providenciar uma Instância Gerida na sub-rede que nome começa com um dígito?**

Trata-se de uma limitação atual do componente subjacente que verifica o nome da sub-rede contra o regex ^[a-zA-Z_][^]**(<!. \\ \/ \: \* \? \" \<\> \| \` \' \^ \. \s])$. Todos os nomes que passam no regex e são nomes de sub-redes válidos são atualmente suportados.

**Como posso escalar o meu caso?**

Pode escalar a sua instância gerida a partir do [portal Azure,](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation) [Modelos PowerShell,](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell) [Azure CLI](/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update&preserve-view=true) ou [ARM](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Posso mover o meu Caso Gerido de uma região para outra?**

Sim, pode. Para obter instruções, consulte [mover recursos através das regiões.](../database/move-resources-across-regions.md)

**Como posso apagar o meu Caso Gerido?**

Pode eliminar Instâncias Geridas através do portal Azure, [PowerShell,](/powershell/module/az.sql/remove-azsqlinstance?preserve-view=true&view=azps-4.3.0) [Azure CLI](/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete&preserve-view=true) ou [Resource Manager REST APIs](/rest/api/sql/managedinstances/delete).

**Quanto tempo demora a criar ou atualizar um caso, ou a restaurar uma base de dados?**

O tempo esperado para criar uma nova instância gerida ou para alterar os níveis de serviço (vCores, armazenamento), depende de vários fatores. Ver [operações de Gestão](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Convenções de nomenclatura

**Um caso gerido pode ter o mesmo nome que um exemplo do SQL Server no local?**

A alteração de um nome de instância gerida não é suportada.

**Posso alterar o prefixo da zona DE DNS?**

Sim, A zona de DNS padrão de Instância Gerida *(database.windows.net DS) pode* ser alterada. 

Para utilizar outra zona DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use o CliConfig para definir um pseudónimo. A ferramenta é apenas um invólucro de definições de registo, para que possa ser feito usando a política de grupo ou um script também.
- Utilize *CNAME* com a opção *TrustServerCertificate=true.*

## <a name="migration-options"></a>Opções de migração

**Como posso migrar da Base de Dados Azure SQL piscina única ou elástica para SQL Managed Instance?**

A instância gerida oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que outras opções de implementação da Base de Dados Azure SQL. Se quiser consolidar dados num único caso, ou simplesmente precisar de uma funcionalidade suportada exclusivamente em caso gerido, pode migrar os seus dados utilizando a funcionalidade de exportação/importação (BACPAC). Aqui estão outras formas de considerar a migração da Base de Dados SQL para a SQL Managed Instance: 
- Utilização de [Fonte de Dados Externa](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Utilização [de SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Usando o [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Como posso migrar a minha base de dados de casos para uma única Base de Dados Azure SQL?**

Uma opção é [exportar uma base de dados para BACPAC](../database/database-export.md) e, em seguida, [importar o ficheiro BACPAC.](../database/database-import.md) Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB.

[A replicação transacional](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true) pode ser utilizada se todas as tabelas da base de dados tiverem chaves *primárias* e não existirem objetos OLTP na base de dados.

As cópias de segurança nativas COPY_ONLY retiradas de instâncias geridas não podem ser restauradas no SQL Server porque a instância gerida tem uma versão de base de dados mais alta em comparação com o SQL Server. Para obter mais detalhes, consulte [a cópia de segurança.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)

**Como posso migrar a minha instância do SQL Server para o SQL Managed Instance?**

Para migrar a sua instância sql Server, consulte [a migração de instâncias SQL Server para Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md).

**Como posso migrar de outras plataformas para a SQL Managed Instance?**

Para obter informações sobre migração de outras plataformas, consulte [o Guia de Migração da Base de Dados Azure.](https://datamigration.microsoft.com/)

## <a name="switch-hardware-generation"></a>Mudar geração de hardware 

**Posso mudar a minha geração de hardware de exemplo entre a Gen 4 e a Gen 5 online?**

A mudança on-line automatizada da Gen4 para a Gen5 é possível se o hardware da Gen5 estiver disponível na região onde a sua instância gerida é a provisionada. Neste caso, pode consultar [a página geral do modelo vCore](../database/service-tiers-vcore.md) explicando como alternar entre gerações de hardware.

Trata-se de uma operação de longa duração, uma vez que uma nova instância gerida será ausquisada em segundo plano e as bases de dados serão automaticamente transferidas entre a antiga e a nova instância com uma rápida falha no final do processo.

Nota: O hardware da Gen4 está a ser eliminado e já não está disponível para novas implementações. Todas as novas bases de dados devem ser implantadas no hardware da Gen5. A mudança da Gen5 para a Gen4 também não está disponível.

## <a name="performance"></a>Desempenho 

**Como posso comparar o desempenho da Instância Gerida com o desempenho do SQL Server?**

Para uma comparação de desempenho entre o exemplo gerido e o SQL Server, um bom ponto de partida são [as melhores práticas para comparação de desempenho entre o exemplo gerido do Azure SQL e](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) o artigo do SQL Server.

**O que causa diferenças de desempenho entre o Caso Gerido e o Servidor SQL?**

Consulte [as principais causas das diferenças de desempenho entre a instância gerida pelo SQL e o SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Para obter mais informações sobre o impacto do tamanho do ficheiro de registo no desempenho do caso gerido para fins gerais , consulte [o impacto do tamanho do ficheiro de registo na Finalidade Geral](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Como sinto o desempenho do meu caso?**

Pode otimizar o desempenho da sua instância gerida através de:
- [Afinação automática](../database/automatic-tuning-overview.md) que proporciona o desempenho máximo e cargas de trabalho estáveis através de afinação contínua do desempenho com base na IA e aprendizagem automática.
-   [Em memória, o OLTP](../in-memory-oltp-overview.md) melhora a produção e a latência nas cargas de trabalho de processamento transacional e fornece insights de negócio mais rápidos. 

Para afinar ainda mais o desempenho, considere aplicar algumas das *melhores práticas* para [aplicação e afinação de bases de dados.](../database/performance-guidance.md#tune-your-database)
Se a sua carga de trabalho consistir em muitas pequenas transações, considere [mudar o tipo de ligação de proxy para modo de redirecionamento](connection-types-overview.md#changing-connection-type) para menor latência e maior produção.

## <a name="monitoring-metrics-and-alerts"></a>Monitorização, Métricas e Alertas

**Quais são as opções de monitorização e alerta para o meu caso gerido?**

Para todas as opções possíveis para monitorizar e alertar sobre o consumo e desempenho de exemplos geridos [sql SQL, consulte o blog de monitorização de instâncias geridas Azure SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Para a monitorização de desempenho em tempo real para o SQL MI, consulte [a monitorização do desempenho em tempo real para Azure SQL DB Managed Instance](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Posso usar o PERFIL SQL para rastreio de desempenho?**

Sim, o Perfil SQL é suportado ou SQL Managed Instance. Para mais detalhes, consulte [o SqL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**O Database Advisor e o Questionry Performance Insight são suportados para bases de dados de casos geridos?**

Não, não são apoiados. Pode utilizar [DMVs](../database/monitoring-with-dmvs.md) e [Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) juntamente com o [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) e [xEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) para monitorizar as suas bases de dados.

**Posso criar alertas métricos no SQL Managed Instance?**

Yes. Para obter instruções, consulte [Criar alertas para a sql Managed Instance](alerts-create.md).

**Posso criar alertas métricos numa base de dados em casos geridos?**

Não é possível, alertando as métricas estão disponíveis apenas para casos geridos. Não estão disponíveis métricas de alerta para bases de dados individuais em casos geridos.

## <a name="storage-size"></a>Tamanho do armazenamento

**Qual é o tamanho máximo de armazenamento para SQL Managed Instance?**

O tamanho do armazenamento para sql Gestd Instance depende do nível de serviço selecionado (Final Geral ou Critical De Negócio). Para limitações de armazenamento destes níveis de serviço, consulte [as características do nível de serviço](../database/service-tiers-general-purpose-business-critical.md).

**Qual é o tamanho mínimo de armazenamento disponível para um caso gerido?**

A quantidade mínima de armazenamento disponível num caso é de 32 GB. O armazenamento pode ser adicionado em incrementos de 32 GB até ao tamanho máximo de armazenamento. Os primeiros 32GB são gratuitos.

**Posso aumentar o espaço de armazenamento atribuído a um caso, independentemente dos recursos de computação?**

Sim, pode comprar armazenamento de complemento, independentemente do cálculo, até certo ponto. Consulte *o armazenamento reservado de instância Max* na [tabela](resource-limits.md#hardware-generation-characteristics).

**Como posso otimizar o meu desempenho de armazenamento no nível de serviço de Finalidade Geral?**

Para otimizar o desempenho do armazenamento, consulte [as melhores práticas de armazenamento em Finalidade Geral](https://techcommunity.microsoft.com).

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

**O armazenamento de reserva é deduzido do armazenamento de instância gerida?**

Não, o armazenamento de cópias de segurança não é deduzido do seu espaço de armazenamento de instância gerida. O armazenamento de backup é independente do espaço de armazenamento de instância e não é limitado em tamanho. O armazenamento de backup é limitado pelo período de tempo para reter a cópia de segurança das bases de dados do seu caso, configurando até 35 dias. Para mais informações, consulte [cópias de segurança automatizadas.](../database/automated-backups-overview.md)

**Como posso ver quando as cópias de segurança automáticas são feitas no meu caso gerido?**

Para acompanhar quando foram realizadas cópias de segurança automatizadas em Caso Gerido, consulte [como rastrear a cópia de segurança automatizada para uma Instância Gerida Azure SQL](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**O apoio a pedido é apoiado?**

Sim, pode criar uma cópia-apenas cópia de backup no seu Azure Blob Storage, mas só será restaurado em Caso Gerido. Para mais informações, consulte [a cópia de segurança.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15) No entanto, a cópia de segurança só é impossível se a base de dados for encriptada pelo TDE gerido pelo serviço, uma vez que o certificado utilizado para encriptação é inacessível. Nesse caso, utilize a função de restauro ponto-a-tempo para mover a base de dados para outra SQL Managed Instance ou mudar para a chave gerida pelo cliente.

**A restauração nativa (de .bak ficheiros) para a Instância Gerida é suportada?**

Sim, é suportado e disponível para versões SQL Server 2005+.  Para utilizar a restauração nativa, faça o upload do seu ficheiro de .bak para o armazenamento de blob Azure e execute comandos T-SQL. Para mais detalhes, consulte [a restauração nativa da URL.](./migrate-to-instance-from-sql-server.md#native-restore-from-url)

## <a name="business-continuity"></a>Continuidade do negócio

**As bases de dados do meu sistema são replicadas para a segunda instância num grupo de falhanços?**

As bases de dados do sistema não são replicadas para a instância secundária num grupo de failover. Portanto, os cenários que dependem de objetos das bases de dados do sistema serão impossíveis na instância secundária, a menos que os objetos sejam criados manualmente no secundário. Para obter soluções [alternativas, consulte Ativar cenários dependentes do objeto a partir das bases de dados do sistema](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Requisitos de rede 

**Quais são as restrições atuais de entrada/saída nSG na sub-rede de Instância Gerida?**

As regras de NSG e UDR necessárias são documentadas [aqui,](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)e automaticamente definidas pelo serviço.
Por favor, tenha em mente que estas regras são apenas as que precisamos para manter o serviço. Para ligar a instâncias geridas e utilizar diferentes funcionalidades, terá de definir regras adicionais, com regras específicas, que precisa de manter.

**Como posso definir regras NSG de entrada em portos de gestão?**

A SQL Managed Instance é responsável pela definição de regras sobre portas de gestão. Isto é conseguido através da configuração da [sub-rede com o nome de serviço.](connectivity-architecture-overview.md#service-aided-subnet-configuration)
Isto é para garantir um fluxo ininterrupto do tráfego de gestão, a fim de cumprir um SLA.

**Posso obter os intervalos ip de origem que são usados para o tráfego de gestão de entrada?**

Yes. Pode analisar o tráfego que passa pelo seu grupo de segurança de redes [configurando os registos de fluxo do Observador de Rede](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Posso definir o NSG para controlar o acesso ao ponto final de dados (porta 1433)?**

Yes. Depois de ser a provisionada uma Instância Gerida, pode definir o NSG que controla o acesso à porta 1433. É aconselhável reduzir ao máximo o seu intervalo de IP.

**Posso definir a firewall NVA ou no local para filtrar o tráfego de gestão de saída com base em FQDNs?**

N.º Isto não é apoiado por várias razões:
-   O tráfego de encaminhamento que represente a resposta ao pedido de gestão de entrada seria assimétrico e não podia funcionar.
-   O tráfego de encaminhamento que vai para o armazenamento seria afetado por restrições de produção e latência para que desta forma não sejamos capazes de fornecer qualidade e disponibilidade de serviço esperada.
-   Com base na experiência, estas configurações são propensas a erros e não são reutilizáveis.

**Posso definir o NVA ou firewall para o tráfego de não gestão de saída?**

Yes. A forma mais simples de o conseguir é adicionar a regra 0/0 a uma UDR associada à sub-rede de instância gerida para encaminhar o tráfego através da NVA.
 
**Quantos endereços IP preciso para uma Instância Gerida?**

A sub-rede deve ter um número suficiente de [endereços IP](connectivity-architecture-overview.md#network-requirements)disponíveis . Para determinar o tamanho da sub-rede VNet para a ocorrência gerida do SQL, consulte [determine o tamanho e o intervalo da sub-rede necessárias para a instância gerida](./vnet-subnet-determine-size.md). 

**E se não houver endereços IP suficientes para a realização de operação de atualização de instâncias?**

Caso não existam [endereços IP suficientes](connectivity-architecture-overview.md#network-requirements) na sub-rede onde a sua instância gerida é alocada, terá de criar uma nova sub-rede e uma nova instância gerida no seu interior. Também sugerimos que a nova sub-rede seja criada com mais endereços IP alocados para que operações de atualização futuras evitem situações semelhantes. Após o fornecimento da nova instância, pode fazer o back up manual e restaurar os dados entre as instâncias antigas e novas ou efetuar [a restauração pontual](point-in-time-restore.md?tabs=azure-powershell)transversal .

**Preciso de uma sub-rede vazia para criar uma Instância Gerida?**

N.º Pode utilizar uma sub-rede vazia ou uma sub-rede que já contenha Instâncias Geridas. 

**Posso alterar o intervalo de endereços da sub-rede?**

Não se houver casos geridos dentro. Esta é uma limitação de infraestrutura de rede Azure. Só é permitido adicionar espaço adicional ao [endereço a uma sub-rede vazia.](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings) 

**Posso mudar o meu caso para outra sub-rede?**

N.º Esta é uma limitação de design de instância gerida atual. No entanto, pode prever uma nova instância noutra sub-rede e fazer retrocevesimento manual e restaurar dados entre o antigo e o novo caso ou realizar [restauro pontual](point-in-time-restore.md?tabs=azure-powershell)transversal .

**Preciso de uma rede virtual vazia para criar uma Instância Gerida?**

Isto não é necessário. Pode [criar uma rede virtual para Azure SQL Managed Instance](./virtual-network-subnet-create-arm-template.md) ou [configurar uma rede virtual existente para Azure SQL Managed Instance](./vnet-existing-add-subnet.md).

**Posso colocar uma Instância Gerida com outros serviços numa sub-rede?**

N.º Atualmente não apoiamos a colocação de Casos Geridos numa sub-rede que já contenha outros tipos de recursos.

## <a name="connectivity"></a>Conectividade 

**Posso ligar-me à minha instância gerida usando o endereço IP?**

Não, isto não é apoiado. O nome de anfitrião de uma instância gerida mapeia para o equilibrador de carga em frente ao cluster virtual da Instância Gerida. Como um cluster virtual pode hospedar várias Instâncias Geridas, uma ligação não pode ser encaminhada para a instância gerida adequada sem especificar o seu nome.
Para obter mais informações sobre a arquitetura de cluster virtual SQL Managed Instance, consulte [a arquitetura de conectividade de cluster virtual.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**O meu caso gerido pode ter um endereço IP estático?**

Atualmente, isto não é apoiado.

Em situações raras, mas necessárias, talvez tenhamos de fazer uma migração on-line de um caso gerido para um novo cluster virtual. Se necessário, esta migração deve-se a mudanças na nossa pilha de tecnologia destinadas a melhorar a segurança e a fiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que está mapeado para o nome de anfitrião de instância gerida. O serviço de instância gerida não reclama suporte a endereço IP estático e reserva-se o direito de alterá-lo sem aviso prévio como parte de ciclos de manutenção regulares.

Por esta razão, desencorajamos vivamente a imutabilidade do endereço IP, uma vez que poderia causar tempo de inatividade desnecessário.

**A Managed Instance tem um ponto final público?**

Yes. A Managed Instance tem um ponto final público que é por padrão usado apenas para Gestão de Serviços, mas um cliente pode habilitar o acesso aos dados também. Para obter mais detalhes, consulte [a Utilização sql Managed Instance com pontos finais públicos](./public-endpoint-overview.md). Para configurar o ponto final público, vá ao [ponto final público configure em SQL Managed Instance](public-endpoint-configure.md).

**Como é que a Managed Instance controla o acesso ao ponto final público?**

A Instância Gerida controla o acesso ao ponto final público tanto a nível da rede como ao nível da aplicação.

Os serviços de gestão e implantação conectam-se a uma instância gerida utilizando um [ponto final de gestão](./connectivity-architecture-overview.md#management-endpoint) que mapeia para um equilibrador de carga externo. O tráfego só é encaminhado para os nós se for recebido num conjunto de portas predefinido que apenas os componentes de gestão da instância gerida usam. Uma firewall incorporada nos nós é configurada para permitir o tráfego apenas a partir das gamas IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre componentes de gestão e o plano de gestão. Para mais detalhes, consulte [a arquitetura de conectividade para sql Managed Instance](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Posso utilizar o ponto final público para aceder aos dados nas bases de dados de Instância Gerida?**

Yes. O cliente terá de permitir o acesso de dados de ponto final público a partir do [portal Azure](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM e configurar o NSG para bloquear o acesso à porta de dados (porta número 3342). Para obter mais informações, consulte o [ponto final público Configure em Azure SQL Managed Instance](public-endpoint-configure.md) e Use [Azure SQL Managed Instance de forma segura com o ponto final público](public-endpoint-overview.md). 

**Posso especificar uma porta personalizada para o ponto final de dados SQL?**

Não, esta opção não está disponível.  Para o ponto final de dados privados, a Instância Gerida utiliza o número de porta padrão 1433 e para o ponto final de dados públicos, a Managed Instance utiliza o número de porta padrão 3342.

**Qual é a forma recomendada de ligar instâncias geridas colocadas em diferentes regiões?**

O prescêdo do circuito da Rota Expresso é a forma preferida de o fazer. O espreitamento global da rede virtual é suportado com a limitação descrita na nota abaixo.  

> [!IMPORTANT]
> [Em 22/9/2020 anunciamos a rede virtual global que procura clusters virtuais recém-criados.](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/) Isto significa que o espreitamento global da rede virtual é suportado para sql Managed Instances criados em sub-redes vazias após a data de anúncio, bem como para todos os casos geridos subsequentes criados nessas sub-redes. Para todos os outros SQL, o suporte de observação está limitado às redes da mesma região devido aos [constrangimentos da rede virtual global de observação.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Consulte também a secção relevante das [Redes Virtuais Azure frequentemente fez perguntas](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) a artigo para mais detalhes. 

Se o perspitamento do circuito de Rota Expresso e o espreitamento da rede virtual global não forem possíveis, a única outra opção é criar a ligação VPN site-to-site[(portal Azure](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI).](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="mitigate-data-exfiltration-risks"></a>Mitigar riscos de exfiltração de dados  

**Como posso mitigar os riscos de exfiltração de dados?**

Para mitigar quaisquer riscos de exfiltração de dados, recomenda-se aos clientes a aplicação de um conjunto de definições e controlos de segurança:

- Ligue [encriptação de dados transparentes (TDE)](../database/transparent-data-encryption-tde-overview.md) em todas as bases de dados.
- Desligue o tempo de execução da linguagem comum (CLR). Isto também é recomendado no local.
- Utilize apenas a autenticação do Azure Ative Directory (Azure AD).
- Aceda ao caso com uma conta DBA de baixo privilégio.
- Configure o acesso à caixa de salto JIT para a conta sysadmin.
- Ligue [a auditoria DO SQL](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integre-a com mecanismos de alerta.
- Ligue [a Deteção](../database/threat-detection-configure.md) de Ameaças do [Azure Defender para](../database/azure-defender-for-sql.md) a suite SQL.

## <a name="dns"></a>DNS

**Posso configurar um DNS personalizado para sql Managed Instance?**

Yes. Ver [como configurar um DNS personalizado para Azure SQL Managed Instance](./custom-dns-configure.md).

**Posso refrescar o DNS?**

Yes. Consulte [a configuração de servidores DNS de rede virtual sincronizada no cluster virtual SQL Managed Instance](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

A configuração do DNS é eventualmente renovada:

- Quando o contrato de arrendamento DHCP expirar.
- No upgrade da plataforma.

Como uma solução alternativa, desclasse a SQL Managed Instance para 4 vCores e atualize-a novamente depois. Isto tem um efeito colateral de refrescar a configuração do DNS.

## <a name="change-time-zone"></a>Alterar fuso horário

**Posso mudar o fuso horário para um caso gerido?**

A configuração do fuso horário pode ser definida quando uma instância gerida é prevista pela primeira vez. Mudar o fuso horário de um caso gerido existente não é suportado. Para mais informações, consulte [as limitações do fuso horário.](timezones-overview.md#limitations)

As soluções alternativas incluem a criação de uma nova instância gerida com o fuso horário adequado e, em seguida, ou executar uma cópia de segurança manual e restaurar, ou o que recomendamos, realizando uma [restauração pontual transversal no tempo](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance).


## <a name="security-and-database-encryption"></a>Encriptação de segurança e base de dados

**A função do servidor sysadmin está disponível para a SQL Managed Instance?**

Sim, os clientes podem criar logins que são membros do papel sysadmin.  Os clientes que assumem o privilégio sysadmin também assumem a responsabilidade pela exploração do caso, o que pode ter um impacto negativo no compromisso do SLA. Para adicionar login à função do servidor Sysadmin, consulte [a autenticação AD do Azure](./aad-security-configure-tutorial.md#azure-ad-authentication).

**A Encriptação de Dados Transparente é suportada para a SQL Managed Instance?**

Sim, a Encriptação de Dados Transparente é suportada para a SQL Managed Instance. Para mais detalhes, consulte [a encriptação de dados transparente para a sql Managed Instance](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Posso aproveitar o modelo "traga a sua própria chave" para o TDE?**

Sim, o Azure Key Vault para o cenário BYOK está disponível para Azure SQL Managed Instance. Para mais detalhes, consulte [a Encriptação de Dados Transparente com a chave gerida pelo cliente](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Posso migrar uma base de dados de servidor SQL encriptada?**

Sim, pode. Para migrar uma base de dados de servidor SQL encriptada, precisa exportar e importar os certificados existentes para a Gestão de Instâncias, em seguida, fazer uma cópia de segurança completa da base de dados e restaurá-la em Instância Gerida. 

Também pode utilizar [o Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) para migrar as bases de dados encriptadas TDE.

**Como posso configurar a rotação do protetor TDE para a SQL Managed Instance?**

Pode rodar o protetor TDE para Instância Gerida utilizando a Azure Cloud Shell. Para obter instruções, consulte [a encriptação de dados transparente em SQL Managed Instance utilizando a sua própria chave a partir do Cofre da Chave Azure](scripts/transparent-data-encryption-byok-powershell.md).

**Posso restaurar a minha base de dados encriptada para a SQL Managed Instance?**

Sim, não precisa de desencriptar a sua base de dados para a restaurar para a SQL Managed Instance. É necessário fornecer um certificado/chave utilizado como protetor de chave de encriptação no sistema de origem para a SQL Managed Instance para ser capaz de ler dados a partir do ficheiro de backup encriptado. Há duas maneiras possíveis de fazê-lo:

- *Faça o upload do certificado-protector para a SQL Managed Instance*. Pode ser feito apenas com o PowerShell. O [guião da amostra](./tde-certificate-migrate.md) descreve todo o processo.
- *Faça o upload assimétrico do protector-chave para o Cofre da Chave Azure e aponte para o SQL Managed Instance .* Esta abordagem assemelha-se a uma caixa de utilização TDE (BYOK) que também utiliza a integração do Key Vault para armazenar a chave de encriptação. Se não quiser utilizar a chave como protetor de chave de encriptação e apenas quiser disponibilizar a chave para a SQL Managed Instance para restaurar a(s) base de dados encriptada, siga as instruções para configurar o [BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption), e não verifique a caixa de verificação **Faça da chave selecionada o protetor TDE predefinido**.

Assim que disponibilizar o protetor de encriptação para a SQL Managed Instance, pode proceder ao procedimento de restauro da base de dados padrão.

## <a name="purchasing-models-and-benefits"></a>Modelos e benefícios de compra

**Que modelos de compra estão disponíveis para a SQL Managed Instance?**

SQL Managed Instance oferece [modelo de compra baseado em vCore.](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)

**Que benefícios de custo estão disponíveis para a SQL Managed Instance?**

Pode economizar custos com os benefícios Azure SQL das seguintes formas:
-   Maximizar os investimentos existentes em licenças no local e economizar até 55% com [a Azure Hybrid Benefit.](../azure-hybrid-benefit.md?tabs=azure-powershell) 
-   Comprometa-se a fazer uma reserva de recursos computacional e poupe até 33 por cento com [o Benefício de Instância Reservada.](../database/reserved-capacity-overview.md) Combine isto com o benefício Azure Hybrid para uma poupança até 82%. 
-   Poupe até 55% contra os preços da lista com [o Azure Dev/Test Pricing Benefit](https://azure.microsoft.com/pricing/dev-test/) que oferece tarifas com desconto para o seu desenvolvimento contínuo e teste de cargas de trabalho.

**Quem é elegível para o benefício da Instância Reservada?**

Para ser elegível para benefício de instância reservada, o seu tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para obter mais informações sobre reservas, consulte [O Benefício de Instância Reservada.](../database/reserved-capacity-overview.md) 

**É possível cancelar, trocar ou reembolsar reservas?**

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Faturação para Instância Gerida e armazenamento de backup

**Quais são as opções de preços da SQL Managed Instance?**

Para explorar as opções de preços de Exemplos Geridos, consulte [a página de preços](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Como posso rastrear o custo da faturação para o meu caso gerido?**

Pode fazê-lo utilizando a [solução Azure Cost Management](../../cost-management-billing/index.yml). Navegue para **subscrições** no [portal Azure](https://portal.azure.com) e selecione **Análise de Custos.** 

Utilize a opção **custos acumulados** e, em seguida, filtre pelo **tipo de recurso** como `microsoft.sql/managedinstances` .

**Quanto custam as cópias de segurança automatizadas?**

Obtém a mesma quantidade de espaço de armazenamento de backup gratuito que o espaço de armazenamento de dados reservado adquirido, independentemente do conjunto do período de retenção de backup. Se o seu consumo de armazenamento de backup estiver dentro do espaço de armazenamento gratuito de backup atribuído, cópias de segurança automatizadas em caso gerido não terão custos adicionais para si, pelo que serão gratuitas. Exceder o armazenamento de backup acima do espaço livre resultará em custos de cerca de $0,20 - $0,24 por GB/mês nas regiões dos EUA, ou consulte a página de preços para mais detalhes para a sua região. Para obter mais detalhes, consulte [o consumo de armazenamento de backup explicado.](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)

**Como posso monitorizar os custos de faturação para o meu consumo de armazenamento de reserva?**

Pode monitorizar o custo do armazenamento de cópias de segurança através do portal Azure. Para obter instruções, consulte [os custos do Monitor para cópias de segurança automatizadas.](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs) 

**Como posso otimizar os meus custos de armazenamento de reserva na área gerida?**

Para otimizar os custos de armazenamento de backup, consulte [afinação de backup fina em SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Casos de utilização de economia de custos

**Onde posso encontrar casos de utilização e consequentes economias de custos com a SQL Managed Instance?**

Estudos de caso de casos geridos pela SQL:

- [Rio Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Todos os escritos](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Para se compreender melhor os benefícios, custos e riscos associados à implementação do Azure SQL Managed Instance, há também um estudo da Forrester: [O Impacto Económico Total da Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Política de senha 

**Que políticas de palavra-passe são aplicadas para os logins SQL De Instância Gerida SQL?**

A política de palavra-passe de sql Managed Instance para logins SQL herda as políticas da plataforma Azure que são aplicadas aos VMs formando cluster virtual mantendo o exemplo gerido. De momento não é possível alterar nenhuma destas definições, uma vez que estas definições são definidas pelo Azure e herdadas por exemplo gerida.

 > [!IMPORTANT]
 > A plataforma Azure pode alterar os requisitos de política sem notificar os serviços que dependem dessas políticas.

**Quais são as políticas atuais da plataforma Azure?**

Cada login deve definir a sua palavra-passe no início do login e alterar a sua palavra-passe depois de atingir a idade máxima.

| **Política** | **Definição de Segurança** |
| --- | --- |
| Idade máxima da senha | 42 dias |
| Idade mínima da senha | 1 dia |
| Comprimento mínimo da palavra-passe | 10 caracteres |
| A palavra-passe deve satisfazer os requisitos de complexidade | Ativado |

**É possível desativar a complexidade e expiração da palavra-passe em SQL Managed Instance no nível de login?**

Sim, é possível controlar campos de CHECK_POLICY e CHECK_EXPIRATION ao nível de login. Pode verificar as definições atuais executando o seguinte comando T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Depois disso, pode modificar as definições de login especificadas executando:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(substituir 'teste' pelo nome de login pretendido e ajustar os valores de política e de expiração)


## <a name="service-updates"></a>Atualizações de serviço

**O que é a alteração root CA para Azure SQL Database & SQL Managed Instance?**

Consulte [a rotação do certificado para a base de dados Azure SQL & sql Caso gerido](../updates/ssl-root-certificate-expiring.md). 

**O que é um evento de manutenção planeado para a SQL Managed Instance?**

Ver [Plano para eventos de manutenção do Azure em SqL Managed Instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Feedback e suporte do Azure

**Onde posso deixar as minhas ideias para melhorias de exemplos geridos pela SQL?**

Pode votar num novo recurso de Instância Gerida ou colocar uma nova ideia de melhoria na votação no [SQL Managed Instance Feedback Forum](https://feedback.azure.com/forums/915676-sql-managed-instance). Desta forma, pode contribuir para o desenvolvimento do produto e ajudar-nos a priorizar as nossas potenciais melhorias.

**Como posso criar um pedido de apoio ao Azure?**

Para aprender a criar o pedido de suporte do Azure, consulte [Como criar o pedido de suporte do Azure.](../../azure-portal/supportability/how-to-create-azure-support-request.md)
