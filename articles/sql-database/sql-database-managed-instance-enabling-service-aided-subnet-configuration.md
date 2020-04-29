---
title: Habilitar a configuração da sub-rede ajudada pelo serviço para a instância gerida pela base de dados Azure SQL
description: Habilitar a configuração da sub-rede ajudada pelo serviço para a instância gerida pela base de dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533271"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Habilitar a configuração da sub-rede ajudada pelo serviço para a instância gerida pela base de dados Azure SQL
A configuração da sub-rede ajudada pelo serviço fornece uma gestão automatizada de configuração da rede para subredes que acolhem instâncias geridas. Com a configuração da subnet ajudada pelo serviço, o utilizador permanece em pleno controlo do acesso aos dados (fluxos de tráfego TDS), enquanto a instância gerida assume a responsabilidade de garantir um fluxo ininterrupto de tráfego de gestão para cumprir o SLA.

Os grupos de segurança de rede configurados automaticamente e as regras da tabela de rotas são visíveis para o cliente e anotados com prefixo _Microsoft.Sql-managedInstances_UseOnly__.

A configuração ajudada pelo serviço é ativada automaticamente `Microsoft.Sql/managedInstances` assim que ligar a [subnet-delegação](../virtual-network/subnet-delegation-overview.md) para o fornecedor de recursos.

> [!IMPORTANT] 
> Uma vez ligada a delegação de sub-rede, não poderá desligá-la até remover o último aglomerado virtual da sub-rede. Para obter mais detalhes sobre como eliminar o cluster virtual consulte o [seguinte artigo](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Uma vez que a configuração da sub-rede ajudada pelo serviço é uma característica essencial para a manutenção do SLA, a partir de 1 de maio de 2020, não será possível implementar instâncias geridas em subredes que não sejam delegadas para gerir o fornecedor de recursos de instância. No dia 1 de julho de 2020, todas as subredes que contenham instâncias geridas serão automaticamente delegadas para o fornecedor de recursos de instância gerido. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Permitir a sub-delegação de novos destacamentos
Para implementar instâncias geridas na subnet vazia, é necessário delegá-la ao `Microsoft.Sql/managedInstances` fornecedor de recursos, conforme descrito no seguinte [artigo](../virtual-network/manage-subnet-delegation.md). _Por favor, note `Microsoft.DBforPostgreSQL/serversv2` que o artigo referenciado utiliza o fornecedor de recursos, por exemplo. Em vez disso, `Microsoft.Sql/managedInstances` terá supor que o fornecedor de recursos._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Permitir a sub-delegação de sub-redes para as implantações existentes

Para permitir a sub-delegação da sua implementação de instânciagerida existente, você precisa descobrir a subnet de rede virtual onde está colocada. 

Para aprender isto, `Virtual network/subnet` pode `Overview` verificar na lâmina do portal da sua instância gerida.

Como alternativa, pode executar os seguintes comandos PowerShell para aprender isto. Substitua **o id de subscrição** pelo seu ID de subscrição. Substitua também o **nome rg** pelo grupo de recursos para a sua instância gerida e substitua o **nome mi** pelo nome da sua instância gerida.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Assim que encontrar uma subnet de instância `Microsoft.Sql/managedInstances` gerida, é necessário delegá-la ao fornecedor de recursos, conforme descrito no [seguinte artigo](../virtual-network/manage-subnet-delegation.md). _Por favor, note `Microsoft.DBforPostgreSQL/serversv2` que o artigo referenciado utiliza o fornecedor de recursos, por exemplo. Em vez disso, `Microsoft.Sql/managedInstances` terá supor que o fornecedor de recursos._


> [!IMPORTANT]
> Permitir a configuração ajudada pelo serviço não causa falhas ou interrupções na conectividade para casos geridos que já estão na sub-rede.
