---
title: Configurar uma rede virtual existente
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como configurar uma rede virtual e sub-rede existentes onde pode implementar Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 461acc07ee2217a38f7bb59805d4c7e0de4a1e22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617660"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Configurar uma rede virtual existente para o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Azure SQL Managed Instance deve ser implantado dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) Azure e a sub-rede dedicada apenas para casos geridos. Pode utilizar a rede virtual e a sub-rede existentes se estiverem configuradas de acordo com os requisitos de [rede virtual SQL Managed Instance](connectivity-architecture-overview.md#network-requirements).

Se um dos seguintes casos se aplicar a si, pode validar e modificar a sua rede utilizando o script explicado neste artigo:

- Tens uma nova sub-rede que ainda não está configurada.
- Não tem a certeza de que a sub-rede esteja alinhada com os [requisitos.](connectivity-architecture-overview.md#network-requirements)
- Pretende verificar se a sub-rede ainda está em conformidade com os [requisitos](connectivity-architecture-overview.md#network-requirements) de rede depois de ter feito alterações.

> [!Note]
> Pode criar um caso gerido apenas em redes virtuais criadas através do modelo de implementação do Azure Resource Manager. As redes virtuais Azure criadas através do modelo de implementação clássico não são suportadas. Calcular o tamanho da sub-rede seguindo as diretrizes no Determine o tamanho da sub-rede para o artigo [sql Managed Instance.](vnet-subnet-determine-size.md) Não pode redimensionar a sub-rede após implementar os recursos no interior.
>
> Após a criação da instância gerida, a deslocação da instância ou VNet para outro grupo de recursos ou subscrição não é suportada.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se pretender criar uma instância gerida dentro de uma sub-rede existente, recomendamos o seguinte script PowerShell para preparar a sub-rede:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

O script prepara a sub-rede em três passos:

1. Validar: Valida a rede virtual selecionada e a sub-rede para os requisitos de networking de instância gerida SQL.
2. Confirme: Mostra ao utilizador um conjunto de alterações que precisam de ser feitas para preparar a sub-rede para a implementação de Exemplos Geridos SQL. Também pede consentimento.
3. Preparar: Configura corretamente a rede virtual e a sub-rede.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é a SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Para um tutorial que mostre como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de [dados,](instance-create-quickstart.md)consulte Criar um caso gerido .
- Para problemas com DNS, consulte [configurar um DNS personalizado](custom-dns-configure.md).
