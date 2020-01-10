---
title: Topologias de rede para migrações de instância gerenciada do SQL
titleSuffix: Azure Database Migration Service
description: Conheça as configurações de origem e destino para migrações de instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9a313ea798519273ce57961544ec5b37c4d9c5ca
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749264"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologias de rede para BD SQL do Azure Instância Gerenciada migrações usando o serviço de migração de banco de dados do Azure

Este artigo discute várias topologias de rede com as quais o serviço de migração de banco de dados do Azure pode trabalhar para fornecer uma experiência de migração abrangente de servidores SQL locais para Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instância Gerenciada do Banco de Dados SQL do Azure configurado para cargas de trabalho híbridas 

Use essa topologia se sua Instância Gerenciada do Banco de Dados SQL do Azure estiver conectada à sua rede local. Essa abordagem fornece o roteamento de rede mais simplificado e produz a taxa de transferência máxima de dados durante a migração.

![Topologia de rede para cargas de trabalho híbridas](media/resource-network-topologies/hybrid-workloads.png)

**Requisitos**

- Nesse cenário, a instância gerenciada do banco de dados SQL do Azure e a instância do serviço de migração de banco de dados do Azure são criadas na mesma Rede Virtual do Microsoft Azure, mas usam sub-redes diferentes.  
- A rede virtual usada neste cenário também está conectada à rede local usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Instância Gerenciada do Banco de Dados SQL do Azure isolado da rede local

Use essa topologia de rede se seu ambiente exigir um ou mais dos seguintes cenários:

- A instância gerenciada do banco de dados SQL do Azure é isolada da conectividade local, mas sua instância do serviço de migração de banco de dados do Azure está conectada à rede local.
- Se as políticas de RBAC (controle de acesso baseado em função) estiverem em vigor e você precisar limitar os usuários a acessarem a mesma assinatura que está hospedando a instância gerenciada do banco de dados SQL do Azure.
- As redes virtuais usadas para o Instância Gerenciada do Banco de Dados SQL do Azure e o serviço de migração de banco de dados do Azure estão em assinaturas diferentes.

![Topologia de rede para Instância Gerenciada isoladas da rede local](media/resource-network-topologies/mi-isolated-workload.png)

**Requisitos**

- A rede virtual que o serviço de migração de banco de dados do Azure usa para esse cenário também deve estar conectada à rede local usando o (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configure o [emparelhamento de rede VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a rede virtual usada para instância gerenciada do banco de dados SQL do Azure e o serviço de migração de banco de dados do Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrações da nuvem para a nuvem: rede virtual compartilhada

Use essa topologia se o SQL Server de origem estiver hospedado em uma VM do Azure e compartilhar a mesma rede virtual com a instância gerenciada do banco de dados SQL do Azure e o serviço de migração de banco de dados do Azure.

![Topologia de rede para migrações de nuvem para nuvem com uma VNet compartilhada](media/resource-network-topologies/cloud-to-cloud.png)

**Requisitos**

- Nenhum requisito adicional.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrações de nuvem para nuvem: rede virtual isolada

Use essa topologia de rede se seu ambiente exigir um ou mais dos seguintes cenários:

- A instância gerenciada do banco de dados SQL do Azure é provisionada em uma rede virtual isolada.
- Se as políticas de RBAC (controle de acesso baseado em função) estiverem em vigor e você precisar limitar os usuários a acessarem a mesma assinatura que está hospedando a instância gerenciada do banco de dados SQL do Azure.
- As redes virtuais usadas para Instância Gerenciada do Banco de Dados SQL do Azure e o serviço de migração de banco de dados do Azure estão em assinaturas diferentes.

![Topologia de rede para migrações de nuvem para nuvem com uma VNet isolada](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisitos**

- Configure o [emparelhamento de rede VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a rede virtual usada para instância gerenciada do banco de dados SQL do Azure e o serviço de migração de banco de dados do Azure.

## <a name="inbound-security-rules"></a>Regras de segurança de entrada

| **NOMES**   | **Porto** | **PROTOCOLO** | **ORIGEM** | **DESTINO** | **ACTION** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualquer      | Qualquer          | SUB-REDE DMS | Qualquer             | Permitir      |

## <a name="outbound-security-rules"></a>Regras de segurança de saída

| **NOMES**                  | **Porto**                                              | **PROTOCOLO** | **ORIGEM** | **DESTINO**           | **ACTION** | **Motivo da regra**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| móveis                | 443, 9354                                              | TCP          | Qualquer        | Qualquer                       | Permitir      | Comunicação do plano de gerenciamento através do barramento de serviço e do armazenamento de BLOBs do Azure. <br/>(Se o emparelhamento da Microsoft estiver habilitado, talvez você não precise dessa regra.)                                                             |
| Diagnósticos               | 12000                                                 | TCP          | Qualquer        | Qualquer                       | Permitir      | O DMS usa essa regra para coletar informações de diagnóstico para fins de solução de problemas.                                                                                                                      |
| Servidor de origem do SQL         | 1433 (ou porta TCP IP que SQL Server está ouvindo) | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Conectividade de origem do SQL Server do DMS <br/>(Se você tiver conectividade site a site, talvez não precise dessa regra.)                                                                                       |
| SQL Server instância nomeada | 1434                                                  | UDP          | Qualquer        | Espaço de endereços no local | Permitir      | SQL Server conectividade de origem da instância nomeada do DMS <br/>(Se você tiver conectividade site a site, talvez não precise dessa regra.)                                                                        |
| Compartilhamento SMB                 | 445                                                   | TCP          | Qualquer        | Espaço de endereços no local | Permitir      | Compartilhamento de rede SMB para DMS para armazenar arquivos de backup de banco de dados para migrações para o banco de dados SQL do Azure MI e SQL Servers na VM do Azure <br/>(Se você tiver conectividade site a site, talvez não precise dessa regra). |
| DMS_subnet                | Qualquer                                                   | Qualquer          | Qualquer        | DMS_Subnet                | Permitir      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Ver também

- [Migrar SQL Server para Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Criar uma rede virtual com o portal do Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral do serviço de migração de banco de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service?](dms-overview.md).
- Para obter informações atuais sobre a disponibilidade regional do serviço de migração de banco de dados do Azure, consulte a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .
