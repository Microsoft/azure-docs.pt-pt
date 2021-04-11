---
title: Topologias de rede para migrações de instância gerida sql
titleSuffix: Azure Database Migration Service
description: Aprenda as configurações de origem e alvo para as migrações de instância gerida Azure SQL utilizando o Serviço de Migração da Base de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093326"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Topologias de rede para migrações de instância gerida Azure SQL utilizando o Serviço de Migração da Base de Dados Azure

Este artigo discute várias topologias de rede com as quais o Azure Database Migration Service pode trabalhar para proporcionar uma experiência de migração abrangente de SQL Servers a Azure SQL Managed Instance.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Caso Gerido configurado para cargas de trabalho híbridas 

Utilize esta topologia se o seu Azure SQL Managed Instance estiver ligado à sua rede no local. Esta abordagem fornece o encaminhamento de rede mais simplificado e produz o máximo de produção de dados durante a migração.

![Topologia da rede para cargas de trabalho híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- Neste cenário, o SQL Managed Instance e o Azure Database Migration Service são criados na mesma Rede Virtual Microsoft Azure, mas utilizam sub-redes diferentes.  
- A rede virtual utilizada neste cenário também está ligada à rede de instalações utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>SQL Gestão de Instância isolada da rede de instalações

Utilize esta topologia da rede se o seu ambiente necessitar de um ou mais dos seguintes cenários:

- O SQL Managed Instance está isolado da conectividade no local, mas a sua instância do Serviço de Migração da Base de Dados Azure está ligada à rede no local.
- Se as políticas de controlo de acesso baseado em funções (Azure RBAC) estiverem em vigor e precisar de limitar os utilizadores a aceder à mesma subscrição que está a hospedar a SQL Managed Instance.
- As redes virtuais utilizadas para o SQL Managed Instance e o Azure Database Migration Service estão em diferentes subscrições.

![Topologia da Rede para Instância Gerida isolada da rede de instalações](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- A rede virtual que o Azure Database Migration Service utiliza para este cenário também deve ser ligada à rede no local através da utilização de qualquer um https://docs.microsoft.com/azure/expressroute/expressroute-introduction) (ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Confiúdo [da rede VNet](../virtual-network/virtual-network-peering-overview.md) entre a rede virtual utilizada para o SQL Managed Instance e o Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrações em nuvem: Rede virtual partilhada

Utilize esta topologia se o SqL Server de origem estiver hospedado num Azure VM e partilhe a mesma rede virtual com o SQL Managed Instance e o Azure Database Migration Service.

![Topologia da rede para migrações cloud-to-cloud com um VNet compartilhado](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- Sem requisitos adicionais.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrações em nuvem: Rede virtual isolada

Utilize esta topologia da rede se o seu ambiente necessitar de um ou mais dos seguintes cenários:

- O SQL Managed Instance é a provisionado numa rede virtual isolada.
- Se as políticas de controlo de acesso baseado em funções (Azure RBAC) estiverem em vigor e precisar de limitar os utilizadores a aceder à mesma subscrição que está a hospedar a SQL Managed Instance.
- As redes virtuais utilizadas para o SQL Managed Instance e o Azure Database Migration Service estão em diferentes subscrições.

![Topologia da rede para migrações cloud-to-cloud com um VNet isolado](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Confiúdo [da rede VNet](../virtual-network/virtual-network-peering-overview.md) entre a rede virtual utilizada para o SQL Managed Instance e o Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOME**   | **PORTO** | **PROTOCOLO** | **FONTE** | **DESTINO** | **AÇÃO** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | SUB-REDE DMS | Qualquer             | Permitir      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOME**                  | **PORTO**                                              | **PROTOCOLO** | **FONTE** | **DESTINO**           | **AÇÃO** | **Razão para a regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | Qualquer        | Qualquer                       | Permitir      | Comunicação de avião de gestão através do Service Bus. <br/>(Se o espreitamento da Microsoft estiver ativado, poderá não precisar desta regra.)                                                             |
| Armazenamento                   | 443, ServiceTag: Armazenamento                              | TCP          | Qualquer        | Qualquer                       | Permitir      | Avião de gestão usando o armazenamento de bolhas Azure. <br/>(Se o espreitamento da Microsoft estiver ativado, poderá não precisar desta regra.)                                                             |
| Diagnóstico               | 443, ServiceTag: AzureMonitor                         | TCP          | Qualquer        | Qualquer                       | Permitir      | O DMS utiliza esta regra para recolher informações de diagnóstico para efeitos de resolução de problemas. <br/>(Se o espreitamento da Microsoft estiver ativado, poderá não precisar desta regra.)                                                  |
| Servidor SQL Source         | 1433 (ou porta IP TCP que o SQL Server está a ouvir) | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Conectividade de fonte de servidor SQL a partir de DMS <br/>(Se tiver conectividade site-to-site, pode não precisar desta regra.)                                                                                       |
| Sql Server nomeou exemplo | 1434                                                  | UDP          | Qualquer        | Espaço de endereços no local | Permitir      | SQL Server nomeado conectividade de fonte de exemplo do DMS <br/>(Se tiver conectividade site-to-site, pode não precisar desta regra.)                                                                        |
| Participação do SMB                 | 445 (se o cenário se adoeis)                             | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Partilha de rede SMB para DMS para armazenar ficheiros de backup de bases de dados para migrações para Azure SQL Database MI e SQL Servers em Azure VM <br/>(Se tiver conectividade site-to-site, pode não precisar desta regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | Permitir      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Ver também

- [Migrar o SqL Server para o SQL Gerenciado Instância](./tutorial-sql-server-to-managed-instance.md)
- [Visão geral dos pré-requisitos para a utilização do Serviço de Migração da Base de Dados Azure](./pre-reqs.md)
- [Criar uma rede virtual com o portal do Azure](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do Serviço de Migração da Base de Dados Azure, consulte o artigo [O que é o Serviço de Migração da Base de Dados Azure?](dms-overview.md)
- Para obter informações sobre a disponibilidade regional do Serviço de Migração da Base de Dados Azure, consulte os [Produtos disponíveis por página da região.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)