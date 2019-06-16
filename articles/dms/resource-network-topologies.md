---
title: Topologias para migrações de instância gerida da base de dados SQL do Azure com o serviço de migração de base de dados do Azure de rede | Documentos da Microsoft
description: Saiba as configurações de origem e de destino para o Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808415"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologias de rede para migrações de instância gerida BD SQL do Azure com o serviço de migração de base de dados do Azure

Este artigo aborda várias topologias de rede que funciona com o Azure Database Migration Service para fornecer uma experiência de migração abrangente de servidores do SQL Server no local para a instância gerida da base de dados SQL do Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>O Azure SQL Database Managed Instance configurado para cargas de trabalho híbrida 

Utilize esta topologia se o seu Azure SQL Database Managed Instance está ligado à sua rede no local. Esta abordagem fornece o encaminhamento de rede mais simplificado e produz o débito de dados máximo durante a migração.

![Topologia de rede para cargas de trabalho híbrida](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- Neste cenário, a instância gerida de SQL Database do Azure e a instância do serviço de migração de base de dados do Azure são criados na mesma VNet do Azure, mas usam diferentes sub-redes.  
- A VNet utilizada neste cenário também está ligada à rede no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>O Azure SQL Database Managed Instance isolada da rede no local

Utilize esta topologia de rede se o seu ambiente requer um ou mais dos seguintes cenários:

- A instância gerida da base de dados do Azure SQL é isolada da conectividade no local, mas sua instância do serviço de migração de base de dados do Azure está ligada à rede no local.
- Se as políticas de controlo de acesso baseado em ' (RBAC) da função estão em vigor e precisar de limitar os utilizadores a aceder à mesma subscrição que está a alojar a instância gerida da base de dados do Azure SQL.
- As VNets utilizadas para a instância gerida da base de dados SQL do Azure e o serviço de migração de base de dados do Azure estão em subscrições diferentes.

![Topologia de rede para a instância gerida isolada da rede no local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- A VNet que utiliza o Azure Database Migration Service para este cenário tem também de estar ligada à rede no local através de um (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configurar [VNet peering da rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a VNet utilizada para a base de dados do Azure SQL gerida instância e o Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrações de cloud para a cloud: partilhado VNet

Utilize esta topologia se a origem do SQL Server estiver alojada numa VM do Azure e compartilha a mesma VNET com a instância de base de dados do SQL do Azure gerido e o Azure Database Migration Service.

![Topologia de rede para migrações de Cloud para a Cloud com uma VNet partilhada](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- Não existem requisitos adicionais.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Na cloud para migrações para a cloud: VNet em ambiente isolado

Utilize esta topologia de rede se o seu ambiente requer um ou mais dos seguintes cenários:

- A instância gerida da base de dados do Azure SQL é aprovisionada numa VNet isolada.
- Se as políticas de controlo de acesso baseado em ' (RBAC) da função estão em vigor e precisar de limitar os utilizadores a aceder à mesma subscrição que está a alojar a instância gerida da base de dados do Azure SQL.
- As VNets utilizadas para a instância gerida da base de dados SQL do Azure e o serviço de migração de base de dados do Azure estão em subscrições diferentes.

![Topologia de rede para migrações de Cloud para a Cloud com uma VNet isolada](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Configurar [VNet peering da rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a VNet utilizada para a base de dados do Azure SQL gerida instância e o Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOME**   | **PORT** | **PROTOCOL** | **ORIGEM** | **DESTINO** | **AÇÃO** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | SUB-REDE DO DMS | Qualquer             | Permitir      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOME**                  | **PORT**                                              | **PROTOCOL** | **ORIGEM** | **DESTINO**           | **AÇÃO** | **Motivo para a regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| móveis                | 443,9354                                              | TCP          | Qualquer        | Qualquer                       | Permitir      | Comunicação de plano de gestão através do service bus e o armazenamento de Blobs do Azure. <br/>(Se estiver ativada o peering da Microsoft, poderá não ter esta regra.)                                                             |
| Diagnóstico               | 12000                                                 | TCP          | Qualquer        | Qualquer                       | Permitir      | O DMS utiliza esta regra para recolher informações de diagnóstico para fins de resolução de problemas.                                                                                                                      |
| Servidor de origem de SQL         | 1433 (ou de porta de TCP IP que o SQL Server está escutando para) | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Conectividade de origem do SQL Server do DMS <br/>(Se tiver conectividade de site a site, poderá não ter esta regra.)                                                                                       |
| Instância nomeada do SQL Server | 1434                                                  | UDP          | Qualquer        | Espaço de endereços no local | Permitir      | Conectividade de instância de origem do DMS nomeadas de SQL Server <br/>(Se tiver conectividade de site a site, poderá não ter esta regra.)                                                                        |
| Partilha SMB                 | 445                                                   | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Partilha de rede SMB para DMS armazenar ficheiros de cópia de segurança de base de dados para as migrações para o MI de base de dados do Azure SQL e SQL Servers em VM do Azure <br/>(Se tiver conectividade de site a site, poderá não ter esta regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | Permitir      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Consulte também

- [Migrar o SQL Server para instância gerida de base de dados SQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Descrição geral da pré-requisitos para utilizar o Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Criar uma rede virtual com o portal do Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do serviço de migração de base de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service?](dms-overview.md).
- Para obter informações atualizadas sobre a disponibilidade regional do serviço de migração de base de dados do Azure, consulte a [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) página.
