---
title: Instância Gerenciada do Banco de Dados SQL do Azure DNS personalizado | Microsoft Docs
description: Este tópico descreve as opções de configuração para um DNS personalizado com um Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 07/17/2019
ms.openlocfilehash: 674c5d48dad5d3cfd138853d7ea38ae4a216c93d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309870"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurando um DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Um Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantado em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md)do Azure. Há alguns cenários (por exemplo, email de BD, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrido) que exigem que nomes de hosts privados sejam resolvidos no Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. 

Como Instância Gerenciada usa o mesmo DNS para seus trabalhos internos, você precisa configurar o servidor DNS personalizado para que ele possa resolver nomes de domínio públicos.

   > [!IMPORTANT]
   > Sempre use nomes de domínio totalmente qualificados (FQDN) para servidores de email, SQL Servers e outros serviços, mesmo que estejam dentro de sua zona DNS privada. Por exemplo, `smtp.contoso.com` use para o servidor de `smtp` email porque Simple não será resolvido corretamente.

   > [!IMPORTANT]
   > A atualização de servidores DNS de rede virtual não afetará Instância Gerenciada imediatamente. Instância Gerenciada configuração do DNS será atualizada após a concessão do DHCP expirar ou após a upgarade da plataforma, o que ocorrer primeiro. **Os usuários são aconselhados a definir sua configuração de DNS de rede virtual antes de criar o primeiro Instância Gerenciada.**

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar um novo Instância Gerenciada, consulte [criando um instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter informações sobre como configurar uma VNet para um Instância Gerenciada, consulte [configuração de vnet para instâncias gerenciadas](sql-database-managed-instance-connectivity-architecture.md)
