---
title: DNS personalizados de instância gerida
description: Este tópico descreve opções de configuração para um DNS personalizado com uma Instância Gerida por Base de Dados SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 1b27d316114b84434a84fc8226eba9328a803dc8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774369"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurar um DNS Personalizado para a Instância Gerida de Base de Dados SQL do Azure

Uma instância gerida por base de dados Azure SQL deve ser implantada dentro de uma rede virtual Azure [(VNet)](../virtual-network/virtual-networks-overview.md). Existem alguns cenários (por exemplo, correio db, servidores ligados a outras instâncias SQL no seu ambiente cloud ou híbrido) que exigem que nomes privados de anfitriões sejam resolvidos a partir da Instância Gerida. Neste caso, você precisa configurar um DNS personalizado dentro do Azure. 

Como a Managed Instance utiliza o mesmo DNS para o seu funcionamento interno, configure o servidor DNS personalizado para que possa resolver nomes de domínio público.

> [!IMPORTANT]
> Utilize sempre um nome de domínio totalmente qualificado (FQDN) para o servidor de correio, a instância do Servidor SQL e para outros serviços, mesmo que estejam dentro da sua zona privada de DNS. Por exemplo, utilize para o seu servidor de `smtp.contoso.com` correio porque não resolve `smtp` corretamente. A criação de um servidor ou replicação ligado que referencia VMs SQL dentro da mesma rede virtual também requer um FQDN e um sufixo DNS padrão. Por exemplo, `SQLVM.internal.cloudapp.net`. Para mais informações, consulte a [resolução de nomes que utiliza o seu próprio servidor DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Atualizar servidores DNS da rede virtual não afetará imediatamente a Instância Gerida. A configuração DNS de instância gerida é atualizada após o termo do contrato de arrendamento DHCP ou após a atualização da plataforma, o que ocorrer primeiro. **Os utilizadores são aconselhados a definir a configuração de DNS da rede virtual antes de criar a sua primeira Instância Gerida.**

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [O que é uma Instância Gerida](sql-database-managed-instance.md)
- Para um tutorial que lhe mostre como criar uma nova Instância Gerida, consulte [Criar uma Instância Gerida](sql-database-managed-instance-get-started.md).
- Para obter informações sobre a configuração de um VNet para uma instância gerida, consulte [a configuração VNet para Instâncias Geridas](sql-database-managed-instance-connectivity-architecture.md)
