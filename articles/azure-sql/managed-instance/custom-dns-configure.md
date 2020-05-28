---
title: DNS Personalizado
titleSuffix: Azure SQL Managed Instance
description: Este tópico descreve opções de configuração para um DNS personalizado com uma Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 5f442eb5b48611d39e9a123a495f8f42095c8017
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045173"
---
# <a name="configuring-a-custom-dns-for-azure-sql-managed-instance"></a>Configurar um DNS personalizado para a instância gerida pelo Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Uma instância gerida azure SQL deve ser implantada dentro de uma rede virtual Azure [(VNet)](../../virtual-network/virtual-networks-overview.md). Existem alguns cenários (por exemplo, correio db, servidores ligados a outras instâncias SQL no seu ambiente cloud ou híbrido) que exigem que nomes privados de anfitriões sejam resolvidos a partir da Instância Gerida SQL. Neste caso, você precisa configurar um DNS personalizado dentro do Azure. 

Uma vez que o SQL Managed Instance utiliza o mesmo DNS para o seu funcionamento interno, configure o servidor DNS personalizado para que possa resolver nomes de domínio público.

> [!IMPORTANT]
> Utilize sempre um nome de domínio totalmente qualificado (FQDN) para o servidor de correio, a instância do Servidor SQL e para outros serviços, mesmo que estejam dentro da sua zona privada de DNS. Por exemplo, utilize para o seu servidor de `smtp.contoso.com` correio porque não resolve `smtp` corretamente. A criação de um servidor ou replicação ligado que referencia VMs SQL dentro da mesma rede virtual também requer um FQDN e um sufixo DNS padrão. Por exemplo, `SQLVM.internal.cloudapp.net`. Para mais informações, consulte a [resolução de nomes que utiliza o seu próprio servidor DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Atualizar servidores DNS da rede virtual não afetará imediatamente a Instância Gerida pela SQL. A configuração DNS de instância gerida é atualizada após o termo do contrato de arrendamento DHCP ou após a atualização da plataforma, o que ocorrer primeiro. **Os utilizadores são aconselhados a definir a configuração de DNS da rede virtual antes de criar a sua primeira Instância Gerida.**

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral, veja [O que é uma Instância Gerida](sql-managed-instance-paas-overview.md)
- Para um tutorial que lhe mostre como criar uma nova Instância Gerida, consulte [Criar uma Instância Gerida](instance-create-quickstart.md).
- Para obter informações sobre a configuração de um VNet para uma instância gerida, consulte [a configuração VNet para Instâncias Geridas](connectivity-architecture-overview.md)
