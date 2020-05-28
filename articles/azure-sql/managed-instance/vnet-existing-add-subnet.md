---
title: Configure uma rede virtual existente
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como configurar uma rede virtual e uma subnet existentes onde pode implantar o Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e41a91320ed3226cb211de5ba3f6f0eb17da555f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044305"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Configure uma rede virtual existente para a Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Instância Gerida azure SQL deve ser implantada dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) Azure e na subrede dedicada apenas a Instâncias Geridas SQL. Pode utilizar a rede virtual existente e a sub-rede se estiver configurada de acordo com os requisitos de [rede virtual SQL Managed Instance](connectivity-architecture-overview.md#network-requirements).

Se um dos seguintes casos se aplicar a si, pode validar e modificar a sua rede utilizando o script explicado neste artigo:

- Tens uma nova sub-rede que ainda não está configurada.
- Não tem a certeza se a sub-rede está alinhada com os [requisitos.](connectivity-architecture-overview.md#network-requirements)
- Deverá verificar se a sub-rede ainda cumpre os [requisitos](connectivity-architecture-overview.md#network-requirements) da rede depois de ter feito alterações.

> [!Note]
> Só é possível criar uma Instância Gerida SQL apenas em redes virtuais criadas através do modelo de implementação do Gestor de Recursos Azure. As redes virtuais Azure criadas através do modelo de implantação clássica não são suportadas. Calcular o tamanho da sub-rede seguindo as diretrizes no determine o tamanho da subnet para o artigo [SQL Managed Instances.](vnet-subnet-determine-size.md) Não podes redimensionar a sub-rede depois de distribuires os recursos lá dentro.
>
> Após a criação da Instância Gerida SQL, não é suportada a mudança da instância ou vNet para outro grupo de recursos ou subscrição.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se pretender criar uma Instância Gerida SQL dentro de uma subnet existente, recomendamos o seguinte script PowerShell para preparar a sub-rede:

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

1. Valida: Valida a rede virtual selecionada e a subnet para os requisitos de rede SQL Managed Instance.
2. Confirmar: Mostra ao utilizador um conjunto de alterações que devem ser feitas para preparar a sub-rede para a implementação da Instância Gerida SQL. Também pede consentimento.
3. Preparar: Ele configura corretamente a rede virtual e a sub-rede.

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral, veja [o que é a Instância Gerida sQL?](sql-managed-instance-paas-overview.md)
- Para um tutorial que mostre como criar uma rede virtual, crie uma Instância Gerida SQL e restaure uma base de dados a partir de uma cópia de segurança da base de dados, consulte [Create a Azure SQL Managed Instance](instance-create-quickstart.md).
- Para problemas dNS, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
