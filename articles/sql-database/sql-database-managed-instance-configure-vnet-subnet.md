---
title: Configure uma rede virtual existente para instância gerida
description: Este artigo descreve como configurar uma rede virtual e uma subnet existentes onde pode implementar a Instância Gerida pela Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476954"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Configurar uma rede virtual existente para a Instância Gerida da Base de Dados SQL do Azure

A Instância Gerida pela Base de Dados Azure SQL deve ser implantada dentro de uma [rede virtual](../virtual-network/virtual-networks-overview.md) Azure e da subrede dedicada apenas a Instâncias Geridas. Pode utilizar a rede virtual existente e a sub-rede se estiver configurada de acordo com os requisitos de [rede virtual Managed Instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Se um dos seguintes casos se aplicar a si, pode validar e modificar a sua rede utilizando o script explicado neste artigo:

- Tens uma nova sub-rede que ainda não está configurada.
- Não tem a certeza se a sub-rede está alinhada com os [requisitos.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)
- Deverá verificar se a sub-rede ainda cumpre os [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements) da rede depois de ter feito alterações.

> [!Note]
> Só é possível criar uma Instância Gerida apenas em redes virtuais criadas através do modelo de implementação do Gestor de Recursos Azure. As redes virtuais Azure criadas através do modelo de implantação clássica não são suportadas. Calcular o tamanho da sub-rede seguindo as diretrizes no artigo Determinar o tamanho da sub-rede para o artigo [Casos Geridos.](sql-database-managed-instance-determine-size-vnet-subnet.md) Não podes redimensionar a sub-rede depois de distribuires os recursos lá dentro.
>
> Após a criação de uma instância gerida, a mudança da instância gerida ou vNet para outro grupo de recursos ou subscrição não é suportada.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se quiser criar uma Instância Gerida dentro de uma subnet existente, recomendamos o seguinte script PowerShell para preparar a sub-rede:

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

O guião prepara a sub-rede em três passos:

1. Valida: Valida a rede virtual selecionada e a subnet para os requisitos de rede de instâncias geridas.
2. Confirmar: Mostra ao utilizador um conjunto de alterações que devem ser feitas para preparar a sub-rede para a implementação de Instância Gerida. Também pede consentimento.
3. Preparar: Ele configura corretamente a rede virtual e a sub-rede.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é um caso gerido?](sql-database-managed-instance.md)
- Para um tutorial que mostre como criar uma rede virtual, criar uma Instância Gerida e restaurar uma base de dados a partir de uma cópia de dados, consulte [Create a Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Para problemas dNS, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).
