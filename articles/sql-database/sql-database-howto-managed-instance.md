---
title: Como configurar um caso gerido
description: Aprenda a configurar e gerir a base de dados Azure SQL gerida.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 56d605a165281b980f7780c5649cc42ba95f2728
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773394"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Como utilizar uma instância gerida na Base de Dados Azure SQL

Neste artigo pode encontrar vários guias, scripts e explicações que podem ajudá-lo a gerir e configurar a sua instância gerida.

## <a name="migration"></a>Migração

- [Migrar para um caso gerido](sql-database-managed-instance-migrate.md) – Conheça o processo de migração recomendado e ferramentas para a migração para um caso gerido.

- Migrar o [TDE para uma instância gerida](sql-database-managed-instance-migrate-tde-certificate.md) – Se a sua base de dados Do Servidor SQL estiver protegida com encriptação de dados transparente (TDE), terá de migrar um certificado que uma instância gerida pode usar para desencriptar a cópia de segurança que pretende restaurar no Azure.

## <a name="network-configuration"></a>Configuração da rede

- Determine o [tamanho de uma subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) de instância gerida – A instância gerida é colocada em subnet de dedicações que não podem ser redimensionadas uma vez que você adiciona os recursos dentro. Por isso, teria de calcular qual a gama de endereços IP necessária para a sub-rede, dependendo do número e tipos de instâncias que pretende implementar na sub-rede.
- [Crie novas VNet e subnet para uma instância gerida](sql-database-managed-instance-create-vnet-subnet.md) – Azure VNet e subnet onde pretende implementar as suas instâncias geridas deve ser configurada de acordo com os requisitos de rede aqui [descritos](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Neste guia pode encontrar a forma mais fácil de criar o seu novo VNet e subnet devidamente configurado para casos geridos.
- Configure o VNet e a [subnet existentes para uma instância gerida](sql-database-managed-instance-configure-vnet-subnet.md) – se pretender configurar o vNet e a subnet existentes para implementar instâncias geridas no seu interior, aqui pode encontrar o script que verifica os [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements) da rede e fazer configurar a sua subnet de acordo com os requisitos.
- [Configure dNS personalizados](sql-database-managed-instance-custom-dns.md) – precisa de configurar DNS personalizados se pretender aceder a recursos externos nos domínios personalizados a partir da sua instância gerida através de servidor ligado aos perfis de correio db.
- [Configuração](sql-database-managed-instance-sync-network-configuration.md) da rede Sync - Pode acontecer que, apesar de ter integrado a [sua aplicação com uma Rede Virtual Azure,](../app-service/web-sites-integrate-with-vnet.md)não pode&#39;estabelecer ligação a uma instância gerida. Uma coisa que pode tentar é refrescar a configuração de rede para o seu plano de serviço.
- [Localizar endereço IP do ponto final](sql-database-managed-instance-find-management-endpoint-ip-address.md) de gestão – A instância gerida utiliza ponto final público para fins de gestão. Pode determinar o endereço IP do ponto final de gestão utilizando o script aqui descrito.
- [Verifique a proteção contra firewall incorporada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – A instância gerida está protegida com firewall incorporado que permite o tráfego apenas em portas necessárias. Pode verificar e verificar as regras de firewall incorporadas utilizando o script descrito neste guia.
- [Connect applications](sql-database-managed-instance-connect-app.md) – A instância gerida é colocada no seu próprio Azure VNet privado com endereço IP privado. Aprenda sobre diferentes padrões para ligar as aplicações à sua instância gerida.

## <a name="feature-configuration"></a>Configuração de recurso

- [A replicação transacional](replication-with-sql-database-managed-instance.md) permite-lhe replicar os seus dados entre instâncias geridas ou de sql server no local para uma instância gerida, e vice-versa. Encontre mais informações sobre como utilizar e configurar a replicação de transações neste guia.
- [Configurar a deteção de ameaças](sql-database-managed-instance-threat-detection.md) – a [deteção](sql-database-threat-detection-overview.md) de ameaças é uma funcionalidade de base de dados Azure SQL incorporada que deteta vários potenciais ataques, como a Injeção SQL ou acesso a partir de locais suspeitos. Neste guia pode aprender a ativar e configurar a [deteção](sql-database-threat-detection-overview.md) de ameaças para uma instância gerida.
- [A criação](sql-database-managed-instance-alerts.md) de alertas permite-lhe configurar alertas sobre métricas monitorizadas, tais como a utilização do CPU, o consumo de espaço de armazenamento, o IOPS e outros, por exemplo gerido. Neste guia, aprenderá a ativar e configurar alertas para a ocorrência de um caso gerido.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [guias de como fazer para bases de dados individuais](sql-database-howto-single-database.md)
