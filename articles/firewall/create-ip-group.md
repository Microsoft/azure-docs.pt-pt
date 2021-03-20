---
title: Criar grupos IP em Azure Firewall
description: Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394531"
---
# <a name="create-ip-groups"></a>Criar Grupos IP

Os Grupos IP permitem-lhe agrupar e gerir endereços IP para as regras do Azure Firewall. Podem ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereço IP.

## <a name="create-an-ip-group---azure-portal"></a>Criar um Grupo IP - Portal Azure

Para criar um Grupo IP utilizando o portal Azure:

1. Na página inicial do portal Azure, selecione **Criar um recurso**.
1. Na caixa de pesquisa, insira **grupos IP** e, em seguida, selecione **Grupos IP**.
1. Selecione **Criar**.
1. Selecione a sua subscrição.
1. Selecione um grupo de recursos ou crie um novo.
1. Insira um nome único para si IP Group e, em seguida, selecione uma região.
1. Selecione **Seguinte: endereços IP**.
1. Digite um endereço IP, vários endereços IP ou intervalos de endereço IP.

   Existem duas formas de introduzir endereços IP:
   - Pode inseri-los manualmente
   - Pode importá-los de um ficheiro

   Para importar de um ficheiro, **selecione Import from a file**. Pode arrastar o seu ficheiro para a caixa ou selecionar **procurar ficheiros**. Se necessário, pode rever e editar os seus endereços IP carregados.

   Quando digita um endereço IP, o portal valida-o para verificar se há problemas de sobreposição, duplicação e formatação.

1. Quando terminar, selecione **Review + Create**.
1. Selecione **Criar**.

## <a name="create-an-ip-group---azure-powershell"></a>Criar um Grupo IP - Azure PowerShell

Este exemplo cria um Grupo IP com um prefixo de endereço e um endereço IP utilizando a Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Criar um Grupo IP - Azure CLI

Este exemplo cria um Grupo IP com um prefixo de endereço e um endereço IP utilizando o CLI Azure:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre grupos IP](ip-groups.md)
