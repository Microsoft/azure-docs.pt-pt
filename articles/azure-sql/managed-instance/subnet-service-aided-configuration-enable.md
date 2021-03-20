---
title: Habilitação da configuração da sub-rede ajudada pelo serviço para a Azure SQL Gestdited Instance
description: Habilitação da configuração da sub-rede ajudada pelo serviço para a Azure SQL Gestdited Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91618034"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Habilitação da configuração da sub-rede ajudada pelo serviço para a Azure SQL Gestdited Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A configuração da sub-rede ajudada pelo serviço fornece uma gestão automatizada da configuração da rede para sub-redes que hospedam instâncias geridas. Com a configuração da sub-rede ajudada pelo serviço, o utilizador permanece em pleno controlo do acesso aos dados (fluxos de tráfego TDS), enquanto a instância gerida assume a responsabilidade de garantir um fluxo ininterrupto de tráfego de gestão para cumprir o SLA.

Os grupos de segurança de rede configurados automaticamente e as regras de tabela de rotas são visíveis para o cliente e anotadas com prefixo _Microsoft.Sql-managedInstances_UseOnly_ _.

A configuração ajudada pelo serviço é ativada automaticamente assim que ligar [a sub-delegação](../../virtual-network/subnet-delegation-overview.md) para o fornecedor de `Microsoft.Sql/managedInstances` recursos.

> [!IMPORTANT] 
> Uma vez ligada a sub-delegação, não poderá desligá-la até remover o último cluster virtual da sub-rede. Para obter mais detalhes sobre como eliminar o cluster virtual consulte o seguinte [artigo](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Uma vez que a configuração da sub-rede ajudada pelo serviço é uma característica essencial para manter o SLA, a partir de 1 de maio de 2020, não será possível implementar instâncias geridas em sub-redes que não são delegadas para o fornecedor de recursos de instância gerido. No dia 1 de julho de 2020, todas as sub-redes que contenham instâncias geridas serão automaticamente delegadas para o fornecedor de recursos de instância gerido. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Habilitação da sub-delegação para novas implantações
Para implementar a instância gerida na sub-rede vazia, é necessário deledetá-la ao `Microsoft.Sql/managedInstances` fornecedor de recursos, conforme descrito no artigo seguinte. [](../../virtual-network/manage-subnet-delegation.md) _Por favor, note que o artigo referenciado utiliza `Microsoft.DBforPostgreSQL/serversv2` o fornecedor de recursos, por exemplo. Em vez disso, terá de utilizar o `Microsoft.Sql/managedInstances` fornecedor de recursos._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Habilitação da sub-delegação para as implantações existentes

Para permitir a sub-delegação para a sua implementação de instância gerida existente, é necessário descobrir a sub-rede de rede virtual onde está colocada. 

Para aprender isto, pode verificar `Virtual network/subnet` na lâmina do portal da sua instância `Overview` gerida.

Como alternativa, podes executar os seguintes comandos powerShell para aprender isto. Substitua o **id de subscrição** pelo seu ID de subscrição. Substitua também o **nome rg** pelo grupo de recursos para a sua instância gerida e substitua o **nome mi-name** pelo nome da sua instância gerida.

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

Uma vez que encontre a sub-rede de instância gerida, precisa delerá-la ao `Microsoft.Sql/managedInstances` fornecedor de recursos, conforme descrito no artigo seguinte. [](../../virtual-network/manage-subnet-delegation.md) _Por favor, note que o artigo referenciado utiliza `Microsoft.DBforPostgreSQL/serversv2` o fornecedor de recursos, por exemplo. Em vez disso, terá de utilizar o `Microsoft.Sql/managedInstances` fornecedor de recursos._


> [!IMPORTANT]
> Permitir a configuração ajudada pelo serviço não causa falhas ou interrupção na conectividade para casos geridos que já se encontram na sub-rede.
