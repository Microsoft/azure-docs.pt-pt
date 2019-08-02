---
title: Configurar uma rede virtual existente para o Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como configurar uma rede virtual e uma sub-rede existentes onde você pode implantar Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 01/15/2019
ms.openlocfilehash: 509a08705d87e8f789e26ebbcec295a31f99f416
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567636"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Configurar uma rede virtual existente para Instância Gerenciada do Banco de Dados SQL do Azure

Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantado em uma [rede virtual](../virtual-network/virtual-networks-overview.md) do Azure e a sub-rede dedicada somente para instâncias gerenciadas. Você pode usar a rede virtual e a sub-rede existentes se ela estiver configurada de acordo com os [requisitos de rede virtual instância gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Se um dos casos a seguir se aplicar a você, você poderá validar e modificar sua rede usando o script explicado neste artigo:

- Você tem uma nova sub-rede que ainda não está configurada.
- Você não tem certeza de que a sub-rede está alinhada com os [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Você deseja verificar se a sub-rede ainda está em conformidade com os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements) depois de fazer as alterações.

> [!Note]
> Você pode criar um Instância Gerenciada somente em redes virtuais criadas por meio do modelo de implantação de Azure Resource Manager. Não há suporte para redes virtuais do Azure criadas por meio do modelo de implantação clássico. Calcule o tamanho da sub-rede seguindo as diretrizes no artigo [determinar o tamanho da sub-rede para instâncias gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md) . Você não pode redimensionar a sub-rede depois de implantar os recursos dentro do.
>
> Depois que uma instância gerenciada é criada, não há suporte para a movimentação da instância gerenciada ou da VNet para outro grupo de recursos ou assinatura.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se você quiser criar um Instância Gerenciada dentro de uma sub-rede existente, recomendamos o seguinte script do PowerShell para preparar a sub-rede:

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

O script prepara a sub-rede em três etapas:

1. Verifica Ele valida a rede virtual e a sub-rede selecionadas para Instância Gerenciada requisitos de rede.
2. Veja Ele mostra ao usuário um conjunto de alterações que precisam ser feitas para preparar a sub-rede para Instância Gerenciada implantação. Ele também pede consentimento.
3. Deixar Ele configura corretamente a rede virtual e a sub-rede.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral, consulte [o que é um instância gerenciada?](sql-database-managed-instance.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar um Instância Gerenciada e restaurar um banco de dados de um backup de banco de dados, consulte [criar um instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte Configurando [um DNS personalizado](sql-database-managed-instance-custom-dns.md).
