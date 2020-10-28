---
title: Configurar & gerir referência de conteúdo
titleSuffix: Azure SQL Managed Instance
description: Um guia de referência de conteúdos que lhe ensina a configurar e gerir a Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779769"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Referência de conteúdo de instância gerida Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste artigo pode encontrar uma referência de conteúdo a vários guias, scripts e explicações que o ajudam a gerir e configurar Azure SQL Managed Instance.

## <a name="load-data"></a>Carregar dados

- [Migrar para Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md): Saiba mais sobre o processo de migração recomendado e ferramentas para migração para Azure SQL Managed Instance.
- [Migrar o certificado TDE para Azure SQL Managed Instance](tde-certificate-migrate.md): Se a base de dados do SEU SQL Server estiver protegida com encriptação de dados transparente (TDE), terá de migrar o certificado que a SQL Managed Instance pode usar para desencriptar a cópia de segurança que pretende restaurar no Azure.
- [Importar uma BD de um BACPAC](../database/database-import.md)
- [Exportar uma BD de um BACPAC](../database/database-export.md)
- [Carregar dados com o BCP](../load-from-csv-with-bcp.md)
- [Carregar dados com o Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Configuração de rede

- Determine o [tamanho da sub-rede](vnet-subnet-determine-size.md): Uma vez que a sub-rede não pode ser redimensionada após a implementação do SQL Managed Instance, é necessário calcular qual a gama de endereços IP necessária para o número e tipos de instâncias geridas que pretende implantar na sub-rede. 
- [Criar um novo VNet e sub-rede](virtual-network-subnet-create-arm-template.md): Configurar a rede virtual e a sub-rede de acordo com os [requisitos](connectivity-architecture-overview.md#network-requirements)da rede . 
- [Configure um VNet e uma sub-rede existentes](vnet-existing-add-subnet.md): Verifique os requisitos de rede e configuure a sua rede virtual e sub-rede existentes para implementar a SqL Managed Instance. 
- [Configurar DNS personalizados](custom-dns-configure.md): Configurar DNS personalizados para conceder acesso de recursos externos a domínios personalizados a partir de SQL Managed Instance através de um servidor ligado de perfis de correio db. 
- [Configuração da rede sincronizada](azure-app-sync-network-configuration.md): Refresque o plano de configuração de rede se não conseguir estabelecer uma ligação depois [de integrar a sua aplicação com uma rede virtual Azure](../../app-service/web-sites-integrate-with-vnet.md).
- [Encontre o endereço IP do ponto final de gestão](management-endpoint-find-ip-address.md): Determine o ponto final público que a SQL Managed Instance está a utilizar para fins de gestão. 
- [Verifique a proteção contra firewall incorporada](management-endpoint-verify-built-in-firewall.md): Verifique se a SQL Managed Instance permite o tráfego apenas em portas necessárias e outras regras de firewall incorporadas. 
- [Conecte aplicações](connect-application-instance.md): Saiba mais sobre diferentes padrões para ligar as aplicações à SQL Managed Instance.

## <a name="feature-configuration"></a>Configuração de recursos

- [Configurar a autenticação do Azure AD](../database/authentication-aad-configure.md)
- [Configurar o acesso condicional](../database/conditional-access-configure.md)
- [Autenticação multifator do Azure AD](../database/authentication-mfa-ssms-overview.md)
- [Configurar a autenticação multifator](../database/authentication-mfa-ssms-configure.md)
- [Configurar uma política de retenção temporal](../database/temporal-tables-retention-policy.md)
- [Configurar o TDE com o BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Rodar chaves BYOK de TDE](../database/transparent-data-encryption-byok-key-rotation.md)
- [Remover um protetor TDE](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurar o OLTP dentro da memória](../in-memory-oltp-configure.md)
- [Configurar a azure Automation](../database/automation-manage.md)
- [A replicação transacional](replication-between-two-instances-configure-tutorial.md) permite-lhe replicar os seus dados entre instâncias geridas, ou de SQL Server no local para SQL Managed Instance, e vice-versa.
- [Configure a deteção de ameaças](threat-detection-configure.md) – [a deteção](../database/threat-detection-overview.md) de ameaças é uma funcionalidade incorporada de Azure SQL Managed Instance que deteta vários potenciais ataques, como injeção de SQL ou acesso a locais suspeitos. 
- [A criação de alertas](alerts-create.md) permite-lhe configurar alertas em métricas monitorizadas como utilização de CPU, consumo de espaço de armazenamento, IOPS e outros para SQL Managed Instance. 

## <a name="monitoring-and-tuning"></a>Monitorização e otimização

- [Ajuste manual](../database/performance-guidance.md)
- [Utilizar DMVs para monitorizar o desempenho](../database/monitoring-with-dmvs.md)
- [Utilize a Loja de Consultas para monitorizar o desempenho](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Resolução de problemas de desempenho com o Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Utilize o registo de diagnósticos de Insights Inteligentes](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitor In-Memory espaço OLTP](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventos expandidos

- [Eventos expandidos](../database/xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos em um arquivo de eventos](../database/xevent-code-event-file.md)
- [Armazenar eventos estendidos em um tampão de anel](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Alertas

- [Criar alertas na instância gerida](alerts-create.md)

## <a name="operations"></a>Operações

- [Falha manual iniciada pelo utilizador em SqL Managed Instance](user-initiated-failover.md)

## <a name="develop-applications"></a>Programar aplicações

- [Conetividade](../database/connect-query-content-reference-guide.md#libraries)
- [Utilizar o Conector do Spark](../../cosmos-db/spark-connector.md)
- [Autenticar uma aplicação](../database/application-authentication-get-client-id-keys.md)
- [Utilize o lote para um melhor desempenho](../performance-improve-use-batching.md)
- [Orientações para conectividade](../database/troubleshoot-common-connectivity-issues.md)
- [Aliases DNS](../database/dns-alias-overview.md)
- [Crie um pseudónimo DNS utilizando o PowerShell](../database/dns-alias-powershell-create.md)
- [Portas - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C e C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Conceber aplicações

- [Conceber para a recuperação após desastre](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Conceção para conjuntos elásticos](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Conceber para atualizações da aplicação](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Design Multi-inquilino SaaS aplicações

- [Padrões de design de SaaS](../database/saas-tenancy-app-design-patterns.md)
- [Indexador de vídeos de SaaS](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicação SaaS](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passos seguintes

Começa por [implementar a SQL Managed Instance](instance-create-quickstart.md).