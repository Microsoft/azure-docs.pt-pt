---
title: Como configurar uma instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: Saiba como configurar e gerenciar a instância gerenciada do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: b8c05ab05630e92e64a4885d5e6cdc6f5471dff2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568069"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Como usar uma instância gerenciada no banco de dados SQL do Azure

Neste artigo, você pode encontrar vários guias, scripts e explicações que podem ajudá-lo a gerenciar e configurar sua instância gerenciada.

## <a name="migration"></a>Migração

- [Migrar para uma instância gerenciada](sql-database-managed-instance-migrate.md) – saiba mais sobre o processo de migração e as ferramentas recomendadas para migração para uma instância gerenciada.

- [Migrar o certificado TDE para uma instância gerenciada](sql-database-managed-instance-migrate-tde-certificate.md) – se seu banco de dados SQL Server estiver protegido com TDE (Transparent Data Encryption), você precisará migrar o certificado que uma instância gerenciada pode usar para descriptografar o backup que você deseja restaurar no Azure.

## <a name="network-configuration"></a>Configuração de rede

- [Determinar o tamanho de uma sub-rede de instância gerenciada](sql-database-managed-instance-determine-size-vnet-subnet.md) – a instância gerenciada é colocada em uma sub-rede dedicada que não pode ser redimensionada quando você adiciona os recursos dentro. Portanto, você precisará calcular qual intervalo de IP de endereços seria necessário para a sub-rede, dependendo do número e dos tipos de instâncias que você deseja implantar na sub-rede.
- [Criar nova VNet e sub-rede para uma instância gerenciada](sql-database-managed-instance-create-vnet-subnet.md) – vnet e sub-rede do Azure em que você deseja implantar suas instâncias gerenciadas devem ser configuradas de acordo com os [requisitos de rede descritos aqui](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Neste guia, você pode encontrar a maneira mais fácil de criar sua nova VNet e sub-rede adequadamente configuradas para instâncias gerenciadas.
- [Configurar vnet e sub-rede existentes para uma instância gerenciada](sql-database-managed-instance-configure-vnet-subnet.md) – se você quiser configurar sua vnet e sub-rede existentes para implantar instâncias gerenciadas dentro do, aqui você encontrará o script que verifica os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements) e configura seu sub-rede de acordo com os requisitos.
- [Configurar DNS personalizado](sql-database-managed-instance-custom-dns.md) – você precisará configurar o DNS personalizado se desejar acessar recursos externos nos domínios personalizados de sua instância gerenciada por meio do servidor vinculado de perfis do db mail.
- [Sincronizar configuração de rede](sql-database-managed-instance-sync-network-configuration.md) – pode acontecer que, embora você tenha [integrado seu aplicativo com uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md), você não pode&#39;estabelecer conexão com uma instância gerenciada. Uma coisa que você pode tentar é atualizar a configuração de rede para seu plano de serviço.
- [Localizar endereço IP do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md) – a instância gerenciada usa um ponto de extremidade público para fins de gerenciamento. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento usando o script descrito aqui.
- [Verificar a proteção de firewall interna](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – a instância gerenciada é protegida com um firewall interno que permite o tráfego somente em portas necessárias. Você pode verificar e verificar as regras de firewall internas usando o script descrito neste guia.
- [Conectar aplicativos](sql-database-managed-instance-connect-app.md) – a instância gerenciada é colocada em sua própria VNet privada do Azure com o endereço IP privado. Saiba mais sobre padrões diferentes para conectar os aplicativos à sua instância gerenciada.

## <a name="feature-configuration"></a>Configuração de recurso

- A [replicação](replication-with-sql-database-managed-instance.md) transacional permite replicar seus dados entre instâncias gerenciadas ou de SQL Server locais para uma instância gerenciada, e vice-versa. Encontre mais informações sobre como usar e configurar a replicação de transações neste guia.
- [Configurar a detecção de ameaças](sql-database-managed-instance-threat-detection.md) – a [detecção de ameaças](sql-database-threat-detection-overview.md) é um recurso interno do banco de dados SQL do Azure que detecta vários ataques em potencial, como injeção de SQL ou acesso de locais suspeitos. Neste guia, você pode aprender a habilitar e configurar a [detecção de ameaças](sql-database-threat-detection-overview.md) para uma instância gerenciada.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [os guias de instruções para bancos de dados individuais](sql-database-howto-single-database.md)