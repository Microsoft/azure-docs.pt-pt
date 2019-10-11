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
ms.date: 07/17/2019
ms.openlocfilehash: 9b15ebc40e99c1cd454396ccde5cca6b1a46abbc
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244754"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurando um DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Um Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantado em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md)do Azure. Há alguns cenários (por exemplo, email de BD, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrido) que exigem que nomes de hosts privados sejam resolvidos no Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. 

Como Instância Gerenciada usa o mesmo DNS para seus trabalhos internos, configure o servidor DNS personalizado para que ele possa resolver nomes de domínio públicos.

> [!IMPORTANT]
> Sempre use um FQDN (nome de domínio totalmente qualificado) para o servidor de email, a instância de SQL Server e para outros serviços, mesmo se eles estiverem dentro de sua zona DNS privada. Por exemplo, use `smtp.contoso.com` para seu servidor de email porque `smtp` não resolverá corretamente. A criação de um servidor vinculado ou replicação que faz referência a VMs do SQL dentro da mesma rede virtual também requer um FQDN e um sufixo DNS padrão. Por exemplo, `SQLVM.internal.cloudapp.net`. Para obter mais informações, consulte [resolução de nomes que usa seu próprio servidor DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A atualização de servidores DNS da rede virtual não afetará Instância Gerenciada imediatamente. Instância Gerenciada configuração de DNS é atualizada após a concessão do DHCP expirar ou após a upgarade da plataforma, o que ocorrer primeiro. **Os usuários são aconselhados a definir sua configuração de DNS de rede virtual antes de criar o primeiro Instância Gerenciada.**

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar um novo Instância Gerenciada, consulte [criando um instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter informações sobre como configurar uma VNet para um Instância Gerenciada, consulte [configuração de vnet para instâncias gerenciadas](sql-database-managed-instance-connectivity-architecture.md)
