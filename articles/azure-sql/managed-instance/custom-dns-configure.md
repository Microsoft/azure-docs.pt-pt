---
title: DNS Personalizado
titleSuffix: Azure SQL Managed Instance
description: Este tópico descreve opções de configuração para um DNS personalizado com Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831506"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Configurar um DNS personalizado para o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance deve ser implantado dentro de uma rede virtual Azure [(VNet)](../../virtual-network/virtual-networks-overview.md). Existem alguns cenários (por exemplo, correio da base de dados, servidores ligados a outras instâncias do SQL Server na cloud ou no ambiente híbrido) que exigem que os nomes dos anfitriões privados sejam resolvidos no SQL Managed Instance. Neste caso, precisa de configurar um DNS personalizado dentro do Azure. 

Como a SQL Managed Instance utiliza o mesmo DNS para o seu funcionamento interno, configurar o servidor DNS personalizado para que possa resolver os nomes de domínio público.

> [!IMPORTANT]
> Utilize sempre um nome de domínio totalmente qualificado (FQDN) para o servidor de correio, para a instância do SQL Server e para outros serviços, mesmo que estejam dentro da sua zona privada de DNS. Por exemplo, use `smtp.contoso.com` para o seu servidor de correio eletrónico porque não resolve `smtp` corretamente. A criação de um servidor ou replicação ligado que refira VMs do SQL Server dentro da mesma rede virtual também requer um FQDN e um sufixo DNS predefinido. Por exemplo, `SQLVM.internal.cloudapp.net`. Para obter mais informações, consulte [a resolução Nome que utiliza o seu próprio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A atualização dos servidores DNS de rede virtual não afetará imediatamente a SqL Managed Instance. Veja [como sincronizar a configuração de servidores DNS de rede virtual no cluster virtual SQL Managed Instance](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Para um tutorial que lhe mostre como criar um novo exemplo gerido, consulte [Criar um caso gerido](instance-create-quickstart.md).
- Para obter informações sobre a configuração de um VNet para um exemplo gerido, consulte [a configuração VNet para instâncias geridas](connectivity-architecture-overview.md).
