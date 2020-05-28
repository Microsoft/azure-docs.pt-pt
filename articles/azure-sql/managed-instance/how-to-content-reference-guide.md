---
title: Configure & gerir a referência do conteúdo
titleSuffix: Azure SQL Managed Instance
description: Um guia de referência de conteúdo que o ensina a configurar e gerir a sua Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: bc0c30e234310a4ee1d013d7a11ca556edd071c6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041117"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Referência de conteúdo de instância gerida Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo pode encontrar uma referência de conteúdo a vários guias, scripts e explicações que o ajudam a gerir e configurar a sua Instância Gerida Azure SQL.

## <a name="load-data"></a>Carregar dados

- [Migrar para um Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md) – Saiba mais sobre o processo de migração recomendado e ferramentas para a migração para um Caso Gerido Azure SQL.
- Migrar o TDE para uma instância gerida pelo [Azure SQL](tde-certificate-migrate.md) – Se a sua base de dados do SQL Server estiver protegida com encriptação de dados transparente (TDE), terá de migrar o certificado que uma Instância Gerida SQL pode usar para desencriptar a cópia de segurança que pretende restaurar em Azure.
- [Importar uma BD de um BACPAC](../database/database-import.md)
- [Exportar uma BD de um BACPAC](../database/database-export.md)
- [Carregar dados com o BCP](../load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Configuração da rede

- Determinar o [tamanho da subnet](vnet-subnet-determine-size.md) Uma vez que a sub-rede não pode ser redimensionada após a implementação da Instância Gerida SQL, é necessário calcular a gama de endereços IP necessária para o número e tipos de Instâncias Geridas SQL que planeia implantar para a sub-rede. 
- [Criar nova VNet e subnet](virtual-network-subnet-create-arm-template.md) Configure a rede virtual e a sub-rede de acordo com os requisitos de [rede aqui descritos](connectivity-architecture-overview.md#network-requirements) 
- [Configure vnet e sub-rede existentes](vnet-existing-add-subnet.md) Verifique os requisitos de rede e configure a sua rede virtual e sub-rede existentes para implementar a Instância Gerida SQL. 
- [Configure DNS personalizados](custom-dns-configure.md) Configure dNS personalizados para conceder acesso a recursos externos a domínios personalizados a partir da sua Instância Gerida SQL através de um servidor ligado de perfis de correio db. 
- [Configuração da rede Sync](azure-app-sync-network-configuration.md) Refresque o plano de configuração de rede se não conseguir estabelecer uma ligação depois de [integrar a sua aplicação com uma Rede Virtual Azure](../../app-service/web-sites-integrate-with-vnet.md)
- [Encontre o endereço IP do ponto final](management-endpoint-find-ip-address.md) da gestão Determine o ponto final público que a Instância Gerida SQL está a usar para fins de gestão. 
- [Verifique a proteção contra firewall incorporada](management-endpoint-verify-built-in-firewall.md) Verifique se a Instância Gerida sQL permite o tráfego apenas em portas necessárias, e outras regras de firewall incorporadas. 
- [Conectar aplicações](connect-application-instance.md) Conheça diferentes padrões para ligar as aplicações à sua Instância Gerida SQL.

## <a name="feature-configuration"></a>Configuração de recurso

- [Configurar a autenticação do Azure AD](../database/authentication-aad-configure.md)
- [Configurar acesso condicional](../database/conditional-access-configure.md)
- [Autenticação multifator do AAD](../database/authentication-mfa-ssms-overview.md)
- [Configurar a autenticação multifator](../database/authentication-mfa-ssms-configure.md)
- [Configurar a política de retenção temporal](../database/temporal-tables-retention-policy.md)
- [Configurar o TDE com o BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Rodar chaves BYOK de TDE](../database/transparent-data-encryption-byok-key-rotation.md)
- [Remover o protetor de TDE](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurar o OLTP dentro da memória](../in-memory-oltp-configure.md)
- [Configure Automação Azure](../database/automation-manage.md)
- [A replicação transacional](replication-between-two-instances-configure-tutorial.md) permite-lhe replicar os seus dados entre as Instâncias Geridas do Azure SQL ou do SQL Server no local para uma Instância Gerida pela SQL e vice-versa.
- [Configurar a deteção](threat-detection-configure.md) de ameaças – a [deteção](../database/threat-detection-overview.md) de ameaças é uma funcionalidade de instância gerida Azure SQL incorporada que deteta vários potenciais ataques, como a Injeção SQL ou acesso a partir de locais suspeitos. 
- [A criação de alertas](alerts-create.md) permite-lhe configurar alertas em métricas monitorizadas, tais como a utilização do CPU, o consumo de espaço de armazenamento, o IOPS e outros para a SQL Managed Instance. 

## <a name="monitoring-and-tuning"></a>Monitorização e otimização

- [Ajuste manual](../database/performance-guidance.md)
- [Utilizar DMVs para monitorizar o desempenho](../database/monitoring-with-dmvs.md)
- [Utilizar o Arquivo de consultas para monitorizar o desempenho](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Resolução de problemas de desempenho com o Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Utilizar registo de diagnóstico do Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitorizar o espaço OLTP dentro da memória](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventos expandidos

- [Eventos expandidos](../database/xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos em arquivo de eventos](../database/xevent-code-event-file.md)
- [Armazenar eventos estendidos em tampão de anel](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>Programar aplicações

- [Conetividade](../database/connect-query-content-reference-guide.md#libraries)
- [Utilizar o Conector do Spark](../../cosmos-db/spark-connector.md)
- [Autenticar aplicação](../database/application-authentication-get-client-id-keys.md)
- [Utilize lotes para um melhor desempenho](../performance-improve-use-batching.md)
- [Orientações para conectividade](../database/troubleshoot-common-connectivity-issues.md)
- [Aliases DNS](../database/dns-alias-overview.md)
- [Configuração DNS pseudónimo PowerShell](../database/dns-alias-powershell-create.md)
- [Portas - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C e C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Conceber aplicações

- [Conceber para a recuperação após desastre](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Conceção para conjuntos elásticos](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Conceber para atualizações da aplicação](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Design Aplicações SaaS multi-inquilinos

- [Padrões de design de SaaS](../database/saas-tenancy-app-design-patterns.md)
- [Indexador de vídeos de SaaS](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicação SaaS](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)



## <a name="next-steps"></a>Próximos passos

Inicie a implementação da [sua Instância Gerida SQL](instance-create-quickstart.md)
