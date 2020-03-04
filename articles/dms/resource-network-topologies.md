---
title: Topologs de rede para migrações de instânciageridas SQL
titleSuffix: Azure Database Migration Service
description: Saiba as configurações de origem e alvo para a Base de Dados Azure SQL gerida por migrações de instâncias utilizando o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254966"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologs de rede para migrações de instância gerida sql DB Azure usando o Serviço de Migração de Bases de Dados Azure

Este artigo discute várias topoologias de rede com as quais o Azure Database Migration Service pode trabalhar para proporcionar uma experiência de migração abrangente desde os servidores SQL no local até à Instância Gerida pela Base de Dados Azure SQL.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instância gerida pela base de dados Azure SQL configurada para cargas de trabalho híbridas 

Utilize esta topologia se a sua Instância gerida pela base de dados Azure SQL estiver ligada à sua rede no local. Esta abordagem fornece o encaminhamento de rede mais simplificado e produz o máximo de produção de dados durante a migração.

![Topologia de rede para cargas de trabalho híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- Neste cenário, a base de dados Azure SQL gerida e a instância do Serviço de Migração de Bases de Dados Azure são criadas na mesma Rede Virtual Microsoft Azure, mas utilizam subredes diferentes.  
- A rede virtual utilizada neste cenário também está ligada à rede no local utilizando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [o VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Caso gerido pela base de dados Azure SQL isolado da rede no local

Utilize esta topologia de rede se o seu ambiente necessitar de um ou mais dos seguintes cenários:

- A instância gerida pela Base de Dados Azure SQL está isolada da conectividade no local, mas a instância do Serviço de Migração de Bases de Dados Azure está ligada à rede no local.
- Se estiverem em vigor, e se estiverem em vigor, e tiver de limitar os utilizadores ao acesso à mesma subscrição que acolhe a instância gerida pela Base de Dados Azure SQL.
- As redes virtuais utilizadas para o Serviço de Migração de Dados de Dados Azure SQL e Azure Database estão em diferentes subscrições.

![Topologia de rede para instância gerida isolada da rede no local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- A rede virtual que o Azure Database Migration Service utiliza para este cenário também deve ser ligada à rede no local utilizando (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configurar o [peering da rede VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a rede virtual utilizada para a base de dados Azure SQL gerida por exemplo e o Serviço de Migração de Bases de Dados Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrações cloud-to-cloud: Rede virtual partilhada

Utilize esta topologia se a fonte SQL Server estiver hospedada num VM Azure e partilhar a mesma rede virtual com a instância gerida pela Azure SQL Database e o Serviço de Migração de Bases de Dados Azure.

![Topologia de rede para migrações Cloud-to-Cloud com uma VNet partilhada](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- Sem requisitos adicionais.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Cloud to cloud migrações: Rede virtual isolada

Utilize esta topologia de rede se o seu ambiente necessitar de um ou mais dos seguintes cenários:

- A instância gerida pela Base de Dados Azure SQL é disponibilizada numa rede virtual isolada.
- Se estiverem em vigor, e se estiverem em vigor, e tiver de limitar os utilizadores ao acesso à mesma subscrição que acolhe a instância gerida pela Base de Dados Azure SQL.
- As redes virtuais utilizadas para o Azure SQL Database Managed Instance e o Azure Database Migration Service estão em diferentes subscrições.

![Topologia de rede para migrações cloud-to-cloud com uma VNet isolada](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Configurar o [peering da rede VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a rede virtual utilizada para a base de dados Azure SQL gerida por exemplo e o Serviço de Migração de Bases de Dados Azure.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOME**   | **PORTO** | **PROTOCOLO** | **FONTE** | **DESTINO** | **AÇÃO** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | DMS SUBNET | Qualquer             | Permitir      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOME**                  | **PORTO**                                              | **PROTOCOLO** | **FONTE** | **DESTINO**           | **AÇÃO** | **Razão para a regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| gestão                | 443,9354                                              | TCP          | Qualquer        | Qualquer                       | Permitir      | Gestão de comunicação de aviões através do service Bus e do armazenamento de blob Azure. <br/>(Se o peering da Microsoft estiver ativado, pode não precisar desta regra.)                                                             |
| Diagnóstico               | 12000                                                 | TCP          | Qualquer        | Qualquer                       | Permitir      | O DMS usa esta regra para recolher informações de diagnóstico para fins de resolução de problemas.                                                                                                                      |
| Servidor SQL Source         | 1433 (ou porta IP TCP que o SQL Server está a ouvir) | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Conectividade de fonte do Servidor SQL a partir de DMS <br/>(Se tiver conectividade site-a-site, pode não precisar desta regra.)                                                                                       |
| Caso nomeado pelo Servidor SQL | 1434                                                  | UDP          | Qualquer        | Espaço de endereços no local | Permitir      | SQL Server nomeado conectividade de origem de caso a partir de DMS <br/>(Se tiver conectividade site-a-site, pode não precisar desta regra.)                                                                        |
| SMB share                 | 445                                                   | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Partilha de rede SMB para o DMS armazenar ficheiros de backup de base de dados para migrações para O Mi e SQL Database MI e SQL Servers em Azure VM <br/>(Se tiver conectividade site-a-site, pode não precisar desta regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | Permitir      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Consulte também

- [Migrar o Servidor SQL para a base de dados Azure SQL Gerida](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Visão geral dos pré-requisitos para a utilização do Serviço de Migração de Bases de Dados Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Criar uma rede virtual com o portal do Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do Serviço de Migração de Bases de Dados Azure, consulte o artigo O que é o Serviço de Migração de Bases de [Dados Azure?](dms-overview.md)
- Para obter informações atuais sobre a disponibilidade regional do Serviço de Migração de Bases de Dados Azure, consulte os Produtos disponíveis por página da [região.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)
