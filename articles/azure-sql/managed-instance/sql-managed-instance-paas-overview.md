---
title: O que é Azure SQL Managed Instance?
description: Saiba como a Azure SQL Managed Instance fornece quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server (Enterprise Edition)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 01/14/2021
ms.openlocfilehash: 5d49a5b57ff4b59005461f2bb13451822723b039
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644978"
---
# <a name="what-is-azure-sql-managed-instance"></a>O que é Azure SQL Managed Instance?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance é o serviço inteligente e escalável de base de dados de nuvem que combina a compatibilidade mais ampla do motor de base de dados sql server com todos os benefícios de uma plataforma totalmente gerida e verde como um serviço. A SQL Managed Instance tem quase 100% de compatibilidade com o mais recente motor de base de dados SQL Server (Enterprise Edition), fornecendo uma implementação de rede virtual nativa [(VNet)](../../virtual-network/virtual-networks-overview.md) que aborda preocupações de segurança comuns, e um modelo de [negócio](https://azure.microsoft.com/pricing/details/sql-database/) favorável aos clientes existentes do SQL Server. A SQL Managed Instance permite que os clientes existentes do SQL Server levantem e transloquem as suas aplicações no local para a nuvem com alterações mínimas de aplicação e de base de dados. Ao mesmo tempo, a SQL Managed Instance preserva todas as capacidades paaS (remendos automáticos e atualizações de versão, [backups automatizados,](../database/automated-backups-overview.md) [alta disponibilidade](../database/high-availability-sla.md)) que reduzem drasticamente a sobrecarga de gestão e tCO.

Se você é novo em Azure SQL Managed Instance, confira o vídeo *Azure SQL Managed Instance* da nossa série de [vídeo Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> Para obter uma lista das regiões onde o SQL Managed Instance está atualmente disponível, consulte [as regiões apoiadas.](resource-limits.md#supported-regions)

O seguinte diagrama descreve as principais características da SQL Managed Instance:

![Principais funcionalidades](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed Instance é projetado para clientes que procuram migrar um grande número de aplicações de um iaas no local ou IaaS, auto-construído, ou ISV forneceu ambiente para um ambiente de nuvem PaaS totalmente gerido, com o mínimo de esforço de migração possível. Utilizando o [Serviço de Migração de Dados Azure](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)totalmente automatizado, os clientes podem levantar e transferir a sua instância existente do SQL Server para a SQL Managed Instance, que oferece compatibilidade com o SQL Server e isolamento total de instâncias de clientes com suporte nativo VNet. Para obter mais informações sobre opções e ferramentas de [migração, consulte a visão geral da migração: SQL Server to Azure SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).</br> Com a Garantia de Software, pode trocar as licenças existentes por tarifas com desconto em SQL Managed Instance utilizando o [Benefício Híbrido Azure para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL Managed Instance é o melhor destino de migração na nuvem para casos de SQL Server que requerem alta segurança e uma rica superfície de programabilidade.

> [!TIP]
> Como podemos tornar o Azure SQL melhor? [Faça o levantamento.](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456)

## <a name="key-features-and-capabilities"></a>Principais funcionalidades e capacidades

SQL Managed Instance combina as melhores funcionalidades que estão disponíveis tanto na Base de Dados Azure SQL como no motor de base de dados sql Server.

> [!IMPORTANT]
> A SQL Managed Instance funciona com todas as funcionalidades da versão mais recente do SQL Server, incluindo operações online, correções automáticas de planos e outras melhorias no desempenho da empresa. Uma comparação das funcionalidades disponíveis é explicada na [comparação de recursos: Azure SQL Managed Instance versus SQL Server](../database/features-comparison.md).

| **Benefícios paaS** | **Continuidade do negócio** |
| --- | --- |
|Sem compra e gestão de hardware <br>Sem despesas gerais de gestão para a gestão de infraestruturas subjacentes <br>Dimensionamento rápido do fornecimento e do serviço <br>Patching automatizado e atualização de versão <br>Integração com outros serviços de dados PaaS |99,99% uptime SLA  <br>Alta [disponibilidade](../database/high-availability-sla.md) incorporada <br>Dados protegidos com [cópias de segurança automatizadas](../database/automated-backups-overview.md) <br>Período de retenção de backup configurável do cliente <br>[Backups iniciados](/sql/t-sql/statements/backup-transact-sql?preserve-view=true&view=azuresqldb-mi-current) pelo utilizador <br>[Capacidade de restauro da base de dados pontual](../database/recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gestão**|
|Ambiente isolado[(integração VNet,](connectivity-architecture-overview.md)serviço de inquilino único, computação e armazenamento dedicados) <br>[Encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação do Azure Ative Directory (Azure AD),](../database/authentication-aad-overview.md)suporte único de inscrição <br> [Principais do servidor AD do Azure (logins)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>Adere aos padrões de conformidade como a Base de Dados Azure SQL <br>[Auditoria SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |A API gestor de recursos Azure para automatizar o fornecimento e dimensionamento de serviços <br>Funcionalidade do portal Azure para o fornecimento e dimensionamento de serviços manuais <br>Serviço de Migração de Dados

> [!IMPORTANT]
> A Azure SQL Managed Instance foi certificada contra uma série de normas de conformidade. Para mais informações, consulte as Ofertas de Conformidade do [Microsoft Azure,](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)onde pode encontrar a lista mais atual de certificações de conformidade de instâncias geridas SQL, listadas na **Base de Dados SQL**.

As principais características da SQL Managed Instance são mostradas na tabela seguinte:

|Funcionalidade | Descrição|
|---|---|
| Versão/construção do SQL Server | Motor de base de dados SQL Server (mais recente estável) |
| Backups automatizados geridos | Yes |
| Caso incorporado e monitorização e métricas de bases de dados | Yes |
| Patching automático de software | Yes |
| As mais recentes funcionalidades do motor da base de dados | Yes |
| Número de ficheiros de dados (ROWS) por base de dados | Vários |
| Número de ficheiros de registo (LOG) por base de dados | 1 |
| VNet - Implementação do Gestor de Recursos Azure | Yes |
| VNet - Modelo de implementação clássico | No |
| Suporte ao portal | Yes|
| Serviço de Integração Incorporada (SSIS) | No - SSIS faz parte da [Azure Data Factory PaaS](../../data-factory/tutorial-deploy-ssis-packages-azure.md) |
| Serviço de Análise Incorporada (SSAS) | No - SSAS é [paaS](../../analysis-services/analysis-services-overview.md) separado |
| Serviço de Reporte Incorporado (SSRS) | Não - utilize [relatórios paginados power BI](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) ou hospedeiro SSRS num Azure VM. Embora a SQL Managed Instance não possa executar o SSRS como um serviço, pode hospedar [bases de dados de catálogo SSRS](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) para um servidor de relatório instalado na Máquina Virtual Azure, utilizando a autenticação do SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O modelo de compra baseado em vCore para SQL Managed Instance [dá-lhe](../database/service-tiers-vcore.md) flexibilidade, controlo, transparência e uma forma simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite-lhe alterar o cálculo, a memória e o armazenamento com base nas suas necessidades de carga de trabalho. O modelo vCore também é elegível para uma poupança de até 55% com o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para o SQL Server.

No modelo vCore, pode escolher entre gerações de hardware.

- Os CPUs lógicos **da Gen4** são baseados em processadores Intel &reg; E5-2673 v3 (Haswell) 2.4 GHz, SSD anexados, núcleos físicos, 7 GB de RAM por núcleo e tamanhos de cálculo entre 8 e 24 vCores.
- Os CPUs lógicos **da Gen5** baseiam-se em processadores Intel &reg; E5-2673 v4 (Broadwell) 2.3 GHz, Intel &reg; SP-8160 (Skylake) e Intel &reg; 8272CL (Cascade Lake) 2,5 GHz processadores, rápidoS NVMe SSD, núcleo lógico hiper-roscado e tamanhos computativos entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre gerações de hardware nos [limites de recursos sql Managed Instance](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Escalões de serviço

SQL Managed Instance está disponível em dois níveis de serviço:

- **Finalidade geral**: Concebido para aplicações com requisitos típicos de desempenho e latência de I/O.
- **Crítica de negócios**: Concebido para aplicações com baixos requisitos de latência de E/S e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambos os níveis de serviço garantem uma disponibilidade de 99,99% e permitem selecionar independentemente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade do Azure SQL Managed Instance, consulte [Alta disponibilidade e Azure SQL Managed Instance](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Nível de serviço para fins gerais

A lista que se segue descreve as características-chave do nível de serviço para fins gerais:

- Projetado para a maioria das aplicações empresariais com requisitos típicos de desempenho
- Armazenamento Azure Blob de alto desempenho (8 TB)
- Alta [disponibilidade](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) incorporada com base no armazenamento fiável do Azure Blob e no [Tecido de Serviço Azure](../../service-fabric/service-fabric-overview.md)

Para obter mais informações, consulte [a camada de armazenamento no nível de finalidade geral](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e nas [melhores práticas e considerações para o SQL Managed Instance (Finalidade Geral)](/archive/blogs/sqlcat/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose).

Encontre mais informações sobre a diferença entre os níveis de serviço nos [limites de recursos sql Managed Instance](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Nível de serviço critical de negócios

O nível de serviço Business Critical é construído para aplicações com elevados requisitos de E/S. Oferece a maior resiliência às falhas usando várias réplicas isoladas.

A lista que se segue descreve as características-chave do nível de serviço Business Critical:

- Projetado para aplicações empresariais com maior desempenho e requisitos de HA
- Vem com armazenamento SSD local super rápido (até 1 TB na Gen4 e até 4 TB na Gen5)
- Alta [disponibilidade](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) incorporada com base em [grupos de disponibilidade e](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) tecido de serviço [Azure](../../service-fabric/service-fabric-overview.md)
- Réplica de [base de dados](../database/read-scale-out.md) adicional incorporada apenas para leitura que pode ser usada para relatórios e outras cargas de trabalho apenas de leitura
- [OLTP em memória](../in-memory-oltp-overview.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre as diferenças entre os níveis de serviço nos [limites de recursos sql Managed Instance](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operações de gestão

A Azure SQL Managed Instance fornece operações de gestão que pode utilizar para implementar automaticamente novos casos geridos, atualizar propriedades de instância e apagar casos quando já não é necessário. Uma explicação detalhada das operações de gestão pode ser encontrada na página geral de [operações de gestão de instâncias geridas.](management-operations-overview.md)

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

SQL Managed Instance vem com funcionalidades de segurança avançadas fornecidas pela plataforma Azure e pelo motor de base de dados SQL Server.

### <a name="security-isolation"></a>Isolamento de segurança

A SQL Managed Instance proporciona um isolamento adicional de segurança de outros inquilinos na plataforma Azure. O isolamento de segurança inclui:

- [Implementação de rede virtual nativa](connectivity-architecture-overview.md) e conectividade com o seu ambiente no local usando Azure ExpressRoute ou VPN Gateway.
- Numa implementação predefinida, o ponto final SQL é exposto apenas através de um endereço IP privado, permitindo uma conectividade segura a partir de redes privadas Azure ou híbridas.
- Inquilino único com infraestruturas subjacentes dedicadas (computação, armazenamento).

O diagrama a seguir descreve várias opções de conectividade para as suas aplicações:

![Elevada disponibilidade](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Para obter mais detalhes sobre a integração do VNet e a aplicação de políticas de networking ao nível da sub-rede, consulte a [arquitetura VNet para instâncias geridas](connectivity-architecture-overview.md) e [Ligue a sua aplicação a um exemplo gerido.](connect-application-instance.md)

> [!IMPORTANT]
> Coloque várias instâncias geridas na mesma sub-rede, onde quer que isso seja permitido pelos seus requisitos de segurança, pois isso lhe trará benefícios adicionais. A co-localização de casos na mesma sub-rede simplificará significativamente a manutenção das infraestruturas de ligação em rede e reduzirá o tempo de provisionamento de instâncias, uma vez que uma longa duração do provisionamento está associada ao custo de implantação da primeira instância gerida numa sub-rede.

### <a name="security-features"></a>Funcionalidades de segurança

A azure SQL Managed Instance fornece um conjunto de funcionalidades avançadas de segurança que podem ser usadas para proteger os seus dados.

- [A auditoria da SQL Managed Instance](auditing-configure.md) rastreia eventos de base de dados e escreve-os para um ficheiro de registo de auditoria colocado na sua conta de armazenamento Azure. A auditoria pode ajudá-lo a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam traduzir preocupações comerciais ou suspeitas de violações de segurança.
- Encriptação de dados em movimento - SQL Managed Instance protege os seus dados fornecendo encriptação para dados em movimento usando a Segurança da Camada de Transporte. Além da Segurança da Camada de Transporte, a SQL Managed Instance oferece proteção de dados sensíveis em voo, em repouso e durante o processamento de consultas com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Sempre encriptado oferece segurança de dados contra violações envolvendo o roubo de dados críticos. Por exemplo, com Always Encrypted, os números de cartão de crédito são armazenados encriptados na base de dados sempre, mesmo durante o processamento de consultas, permitindo a desencriptação no ponto de utilização por pessoal autorizado ou aplicações que precisam de processar esses dados.
- [Advanced Threat Protection](threat-detection-configure.md) complementa [a auditoria](auditing-configure.md) fornecendo uma camada adicional de inteligência de segurança incorporada no serviço que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados. Você é alertado sobre atividades suspeitas, potenciais vulnerabilidades e ataques de injeção DE SQL, bem como padrões de acesso a bases de dados anómalas. Alertas avançados de proteção contra ameaças podem ser vistos a partir do Centro de [Segurança Azure](https://azure.microsoft.com/services/security-center/). Fornecem detalhes de atividades suspeitas e recomendam medidas sobre como investigar e mitigar a ameaça.  
- [A mascaração dinâmica de dados](/sql/relational-databases/security/dynamic-data-masking) limita a exposição sensível aos dados, mascarando-os a utilizadores não privilegiados. A mascaração dinâmica de dados ajuda a impedir o acesso não autorizado a dados sensíveis, permitindo-lhe designar a quantidade de dados sensíveis a revelar com o mínimo impacto na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que esconde os dados sensíveis no conjunto de resultados de uma consulta sobre os campos de base de dados designados, enquanto os dados na base de dados não são alterados.
- [A segurança ao nível da linha](/sql/relational-databases/security/row-level-security) (RLS) permite controlar o acesso a linhas numa tabela de bases de dados com base nas características do utilizador executar uma consulta (por exemplo, por membro do grupo ou contexto de execução). O RLS simplifica o design e codificação da segurança na sua aplicação. O RLS permite-lhe implementar restrições ao acesso à linha de dados. Por exemplo, garantir que os trabalhadores só podem aceder às linhas de dados que são pertinentes para o seu departamento, ou restringir o acesso de dados apenas aos dados relevantes.
- [A encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) encripta ficheiros de dados de casos geridos SQL, conhecidos como dados encriptados em repouso. O TDE executa encriptação e desencriptação em tempo real dos dados e registos. A encriptação utiliza uma chave de encriptação de base de dados (DEK), que é armazenada no registo de arranque da base de dados para disponibilidade durante a recuperação. Pode proteger todas as suas bases de dados num caso gerido com encriptação de dados transparente. O TDE é uma tecnologia comprovada de encriptação em repouso no SQL Server que é exigida por muitos padrões de conformidade para proteger contra o roubo de meios de armazenamento.

A migração de uma base de dados encriptada para SQL Managed Instance é suportada através do Azure Database Migration Service ou restauro nativo. Se planeia migrar uma base de dados encriptada utilizando a restauração nativa, a migração do certificado TDE existente da instância SQL Server para a SQL Managed Instance é um passo necessário. Para obter mais informações sobre as opções de migração, consulte [a migração do SQL Server para o SQL Managed Instance](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integração do Azure Active Directory

SQL Managed Instance suporta logins e logins tradicionais do sql server integrados com Azure AD. Os principais servidores AD (logins)**(pré-visualização pública)** são uma versão em nuvem Azure de logins de bases de dados no local que está a utilizar no seu ambiente no local. Os principais dos servidores AD do Azure (logins) permitem especificar utilizadores e grupos do seu inquilino AD Azure como verdadeiros principais de instâncias, capazes de realizar qualquer operação ao nível de instância, incluindo consultas de base de dados cruzadas dentro da mesma instância gerida.

É introduzida uma nova sintaxe para criar os principais servidores AD do Azure (logins), **DO FORNECEDOR EXTERNO**. Para obter mais informações sobre a sintaxe, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)e reveja a [Provisão de um administrador do Azure Ative Directory para](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) o artigo sql Managed Instance.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e a autenticação multifator

A SQL Managed Instance permite-lhe gerir centralmente identidades de utilizadores de bases de dados e outros serviços da Microsoft com [integração do Azure Ative Directory](../database/authentication-aad-overview.md). Esta capacidade simplifica a gestão de permissões e melhora a segurança. O Azure Ative Directory suporta a [autenticação de vários fatores](../database/authentication-mfa-ssms-configure.md) para aumentar a segurança de dados e aplicações, suportando um único processo de inscrição.

### <a name="authentication"></a>Autenticação

A autenticação sql Managed Instance refere-se à forma como os utilizadores comprovam a sua identidade ao ligarem-se à base de dados. Sql Managed Instance suporta dois tipos de autenticação:  

- **Autenticação SQL:**

  Este método de autenticação utiliza um nome de utilizador e uma palavra-passe.
- **Autenticação do Diretório Ativo Azure:**

  Este método de autenticação utiliza identidades geridas pelo Azure Ative Directory e é suportado para domínios geridos e integrados. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

A autorização refere-se ao que um utilizador pode fazer dentro de uma base de dados em Azure SQL Managed Instance, e é controlada pelos membros da sua conta de utilizador e permissões de nível de objeto. A SQL Managed Instance tem as mesmas capacidades de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de bases de dados

SQL Managed Instance visa cenários de utilizadores com migração de bases de dados em massa a partir de implementações de bases de dados no local ou de dados de iaaS. A SQL Managed Instance suporta várias opções de migração de bases de dados que são discutidas nos guias de migração. Consulte [a visão geral da migração: SQL Server to Azure SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md) para obter mais informações.

### <a name="backup-and-restore"></a>Cópia de segurança e restauro  

A abordagem de migração aproveita os backups do SQL para o armazenamento da Azure Blob. As cópias de segurança armazenadas numa bolha de armazenamento Azure podem ser diretamente restauradas numa instância gerida utilizando o [comando T-SQL RESTORE](/sql/t-sql/statements/restore-statements-transact-sql?preserve-view=true&view=azuresqldb-mi-current).

- Para obter um arranque rápido que mostre como restaurar o ficheiro de backup da base de dados Standard World Importers - Standard database, consulte [Restaurar um ficheiro de backup para uma instância gerida](restore-sample-database-quickstart.md). Este quickstart mostra que você tem que carregar um ficheiro de backup para o armazenamento Azure Blob e fixá-lo usando uma chave de assinatura de acesso compartilhado (SAS).
- Para obter informações sobre a restauração do URL, consulte [Native RESTORE from URL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md#backup-and-restore).

> [!IMPORTANT]
> Cópias de segurança de uma instância gerida só podem ser restauradas para outra instância gerida. Não podem ser restaurados numa instância do SQL Server ou na Base de Dados Azure SQL.

### <a name="database-migration-service"></a>Database Migration Service

O Azure Database Migration Service é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo. Este serviço simplifica as tarefas necessárias para mover as bases de dados existentes de servidores de terceiros e SQL para Azure SQL Database, Azure SQL Managed Instance e SQL Server em Azure VM. Veja [como migrar a sua base de dados no local para SQL Managed Instance usando o Serviço de Migração de Bases de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).

## <a name="sql-features-supported"></a>Funcionalidades SQL suportadas

A SQL Managed Instance tem como objetivo fornecer compatibilidade de área de superfície de cerca de 100% com a versão mais recente do SQL Server através de um plano de lançamento encenado. Para uma lista de funcionalidades e comparação, consulte a comparação de [funcionalidades de instância gerida sql,](../database/features-comparison.md)e para uma lista de diferenças T-SQL em SQL Managed Instance versus SQL Server, consulte [as diferenças de T-SQL de instância gerida sql do SqL Server](transact-sql-tsql-differences-sql-server.md).

SQL Managed Instance suporta compatibilidade retrógrada nas bases de dados do SQL Server 2008. A migração direta dos servidores de base de dados SQL Server 2005 é suportada, e o nível de compatibilidade das bases de dados de SQL Server 2005 migradas é atualizado para o SQL Server 2008.
  
O diagrama que se segue descreve a compatibilidade da área da superfície em SQL Managed Instance:  

![compatibilidade na área da superfície](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Principais diferenças entre o SQL Server no local e o SQL Managed Instance

A SQL Managed Instance beneficia de estar sempre atualizada na nuvem, o que significa que algumas funcionalidades no SQL Server podem ser obsoletas, ser aposentadas ou ter alternativas. Existem casos específicos em que as ferramentas precisam reconhecer que uma determinada característica funciona de uma forma ligeiramente diferente ou que o serviço está a funcionar num ambiente que não controla totalmente.

Algumas diferenças fundamentais:

- A alta disponibilidade é incorporada e pré-configurada utilizando tecnologia semelhante a [always on grupos de disponibilidade](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Existem apenas cópias de segurança automatizadas e restauro pontual. Os clientes podem iniciar `copy-only` cópias de segurança que não interferem com a cadeia automática de backup.
- Especificar os caminhos físicos completos não é suportado, por isso todos os cenários correspondentes têm de ser suportados de forma diferente: O RESTORE DB não suporta com movimento, o CREATE DB não permite caminhos físicos, o BULK INSERT funciona apenas com bolhas Azure, etc.
- SQL Managed Instance suporta [a autenticação AZure AD](../database/authentication-aad-overview.md) como uma alternativa em nuvem à autenticação do Windows.
- A SQL Managed Instance gere automaticamente grupos de ficheiros XTP e ficheiros para bases de dados que contenham In-Memory objetos OLTP.
- A SQL Managed Instance suporta os Serviços de Integração de Servidores SQL (SSIS) e pode hospedar um catálogo SSIS (SSISDB) que armazena pacotes SSIS, mas são executados num runtime gerido de integração Azure-SSIS (IR) na Azure Data Factory. Ver [Create Azure-SSIS IR na Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md). Para comparar as funcionalidades SSIS, consulte [compare a Base de Dados SQL com a SQL Managed Instance](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Características da administração

A SQL Managed Instance permite que os administradores de sistemas gastem menos tempo em tarefas administrativas porque o serviço ou as executa para si ou simplifica essas tarefas. Por exemplo, [instalação e remendação de OS/RDBMS,](../database/high-availability-sla.md) [redimensionamento e configuração de instâncias dinâmicas,](../database/single-database-scale.md) [backups,](../database/automated-backups-overview.md) [replicação de bases de dados](replication-between-two-instances-configure-tutorial.md) (incluindo bases de dados do sistema), [configuração](../database/high-availability-sla.md)de alta disponibilidade , e configuração de fluxos de dados de monitorização de saúde e [desempenho.](../../azure-monitor/insights/azure-sql.md)

Para obter mais informações, consulte [uma lista de funcionalidades de Instância Gerida SQL suportadas e não suportadas,](../database/features-comparison.md)e [diferenças de T-SQL entre o SQL Managed Instance e o SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identificar programáticamente um caso gerido

A tabela seguinte mostra várias propriedades, acessíveis através da Transact-SQL, que pode utilizar para detetar que a sua aplicação está a trabalhar com a SQL Managed Instance e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Este valor é o mesmo que na Base de Dados SQL. Isto **não** indica a versão 12 do motor SQL (SQL Server 2014). A SQL Managed Instance executa sempre a mais recente versão estável do motor SQL, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Este valor é o mesmo que na Base de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Este valor identifica exclusivamente um caso gerido.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS de instância completa no seguinte formato: `<instanceName>` `<dnsPrefix>` . . database.windows.net, onde `<instanceName>` é o nome fornecido pelo cliente, enquanto é `<dnsPrefix>` autogerido parte do nome que garante a singularidade global do nome DNS ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar o seu primeiro exemplo gerido, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns sql](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)
- Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada para resolução de problemas, consulte [o Monitor Azure SQL Managed Instance utilizando a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [os preços da Base de Dados SQL.](https://azure.microsoft.com/pricing/details/sql-database/managed/)