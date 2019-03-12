---
title: Configurar uma rede virtual existente para a instância gerida da base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo descreve como configurar uma rede virtual existente e a sub-rede onde pode implementar a instância gerida da base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 13038bb7159556b36e82b598732d1f2d497bf314
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762777"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Configurar uma rede virtual existente para a instância gerida da base de dados SQL do Azure

Instância de gerida de base de dados de SQL do Azure tem de ser implementada dentro do Azure [rede virtual](../virtual-network/virtual-networks-overview.md) e a sub-rede dedicada apenas para instâncias geridas. Pode utilizar a rede virtual existente e a sub-rede, se ele é configurado de acordo com o [requisitos de rede virtual de instância gerida](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Se um dos seguintes casos se aplica a si, pode validar e modificar a sua rede usando o script explicado neste artigo:

* Tem uma nova sub-rede que ainda não está configurada.
* Não tiver a certeza de que a sub-rede está em sintonia com o [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
* Para verificar se a sub-rede ainda está em conformidade com o [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements) depois de efetuar alterações.


> [!Note]
> Só pode criar uma instância gerida nas redes virtuais criadas com o modelo de implementação Azure Resource Manager. As redes virtuais do Azure criadas através do modelo de implementação clássica não são suportadas. Calcular o tamanho da sub-rede, seguindo as diretrizes a [determinar o tamanho da sub-rede para instâncias geridas](sql-database-managed-instance-determine-size-vnet-subnet.md) artigo. Não pode redimensionar a sub-rede depois de implementar os recursos no interior.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se quiser criar uma instância gerida dentro de uma sub-rede existente, recomendamos o seguinte script do PowerShell para preparar a sub-rede:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

O script prepara a sub-rede em três passos:

1. Valide: Valida a rede virtual selecionada e a sub-rede para os requisitos de rede de instância gerida.
2. Confirme: O utilizador é mostra um conjunto de alterações que precisam ser feitas para preparar a sub-rede para a implementação de instância gerida. Ele também pede consentimento.
3. Prepare: Configura corretamente a rede virtual e sub-rede.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [o que é uma instância gerida?](sql-database-managed-instance.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerida e restaurar uma base de dados a partir de uma cópia de segurança da base de dados, consulte [criar um Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Para problemas DNS, consulte [configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
