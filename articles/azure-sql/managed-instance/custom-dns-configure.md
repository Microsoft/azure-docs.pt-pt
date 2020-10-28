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
ms.openlocfilehash: 017698975706aa8501cd059351cf9a9d88594f77
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779905"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Configure um DNS personalizado para Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance deve ser implantado dentro de uma rede virtual Azure [(VNet)](../../virtual-network/virtual-networks-overview.md). Existem alguns cenários (por exemplo, correio da base de dados, servidores ligados a outras instâncias do SQL Server na cloud ou no ambiente híbrido) que exigem que os nomes dos anfitriões privados sejam resolvidos no SQL Managed Instance. Neste caso, precisa de configurar um DNS personalizado dentro do Azure. 

Como a SQL Managed Instance utiliza o mesmo DNS para o seu funcionamento interno, configurar o servidor DNS personalizado para que possa resolver os nomes de domínio público.

> [!IMPORTANT]
> Utilize sempre um nome de domínio totalmente qualificado (FQDN) para o servidor de correio, para a instância do SQL Server e para outros serviços, mesmo que estejam dentro da sua zona privada de DNS. Por exemplo, use `smtp.contoso.com` para o seu servidor de correio eletrónico porque não resolve `smtp` corretamente. A criação de um servidor ou replicação ligado que refira VMs do SQL Server dentro da mesma rede virtual também requer um FQDN e um sufixo DNS predefinido. Por exemplo, `SQLVM.internal.cloudapp.net`. Para obter mais informações, consulte [a resolução Nome que utiliza o seu próprio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A atualização dos servidores DNS de rede virtual não afetará imediatamente a SqL Managed Instance. A configuração DE DNS de Instância Gerida SQL é atualizada após o termo do contrato de arrendamento DHCP ou após a atualização da plataforma, o que ocorrer primeiro. **Os utilizadores são aconselhados a definir a sua configuração de DNS de rede virtual antes de criarem a sua primeira instância gerida.**

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Para um tutorial que lhe mostre como criar um novo exemplo gerido, consulte [Criar um caso gerido](instance-create-quickstart.md).
- Para obter informações sobre a configuração de um VNet para um exemplo gerido, consulte [a configuração VNet para instâncias geridas](connectivity-architecture-overview.md).