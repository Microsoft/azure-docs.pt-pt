---
title: Criar uma instância da Gestão de API através do PowerShell | Microsoft Docs
description: Siga os passos deste tutorial para criar uma nova instância da Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: 7cd4810059199ddfc88261e27bc910331abe096d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "66730620"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Criar uma nova instância de serviço da Gestão de API do Azure

A Gestão de API (APIM) do Azure ajuda as organizações a publicar APIs para programadores externos, parceiros e internos, de modo a que estes possam explorar ao máximo o potencial dos seus dados e serviços. A Gestão de API fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção. A APIM permite-lhe criar e gerir gateways de API modernos para serviços de back-end existentes alojados em qualquer lado. Para obter mais informações, consulte o tópico [Descrição Geral](api-management-key-concepts.md).

Este guia de início rápido descreve os passos para a criação de uma nova instância da Gestão de API através dos scripts do PowerShell. O guia de início rápido mostra como utilizar o **Azure Cloud Shell**, que pode executar a partir do portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0 ou posterior do módulo PowerShell Azure. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.


## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Criar um serviço de Gestão de API

Esta é uma operação de longa duração e pode demorar até 15 minutos.

```azurepowershell-interactive
New-AzApiManagement -ResourceGroupName "myResourceGroup" -Location "West US" -Name "apim-name" -Organization "myOrganization" -AdminEmail "myEmail" -Sku "Developer"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar e publicar a sua primeira API](import-and-publish.md)
