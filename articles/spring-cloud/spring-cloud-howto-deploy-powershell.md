---
title: Como implementar a nuvem de primavera do Azure com a Azure PowerShell
description: Como implementar a nuvem de primavera do Azure com a Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550014"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Implementar nuvem de primavera Azure com Azure PowerShell

Este artigo descreve como pode criar um exemplo de Azure Spring Cloud usando o módulo [Az.SpringCloud](/powershell/module/Az.SpringCloud) PowerShell.

## <a name="requirements"></a>Requisitos

* Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Enquanto o módulo **Az.SpringCloud** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Depois de este módulo PowerShell ficar geralmente disponível, fará parte das futuras versões do módulo Az PowerShell e disponível por padrão a partir de Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado e no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Provisionar uma nova instância de Azure Spring Cloud

Para criar uma nova instância de Azure Spring Cloud, você usa o [cmdlet New-AzSpringCloud.](/powershell/module/az.springcloud/new-azspringcloud) O exemplo a seguir cria um serviço Azure Spring Cloud com o nome especificado no grupo de recursos previamente criado.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Criar uma nova aplicação Azure Spring Cloud

Para criar uma nova App, utiliza o [cmdlet New-AzSpringCloudApp.](/powershell/module/az.springcloud/new-azspringcloudapp) O exemplo a seguir cria uma aplicação Azure Spring Cloud chamada `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Criar uma nova implementação da Cloud Azure Spring

Para criar uma nova Implementação, utiliza o [cmdlet New-AzSpringCloudDeployment.](/powershell/module/az.springcloud/new-azspringcloudappdeployment) O exemplo a seguir cria uma implementação Azure Spring Cloud nomeada `default` para a `gateway` aplicação.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Obtenha um serviço Azure Spring Cloud

Para obter um serviço Azure Spring Cloud e suas propriedades, você usa o [cmdlet Get-AzSpringCloud.](/powershell/module/az.springcloud/get-azspringcloud) O exemplo a seguir recupera informações sobre o serviço Azure Spring Cloud especificado.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Obtenha uma aplicação Azure Spring Cloud

Para obter uma aplicação Azure Spring Cloud e suas propriedades, você usa o cmdlet [Get-AzSpringCloudApp.](/powershell/module/az.springcloud/get-azspringcloudapp) O exemplo a seguir recupera informações sobre a `gateway` aplicação Spring Cloud.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Obtenha uma implementação da Nuvem de primavera Azure

Para obter uma implementação da Cloud Azure Spring e suas propriedades, você usa o [cmdlet Get-AzSpringCloudDeployment.](/powershell/module/az.springcloud/get-azspringcloudappdeployment) O exemplo a seguir recupera informações sobre a implementação da `default` Cloud spring.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, pode eliminá-los executando os seguintes exemplos.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Eliminar uma implementação da Nuvem de primavera Azure

Para remover uma implementação da Nuvem de Mola Azure, utilize o [cmdlet Remove-AzSpringCloudAppDeployment.](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) O exemplo a seguir elimina uma aplicação Azure Spring Cloud nomeada `default` para o serviço e aplicação especificados.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Elimine uma aplicação Azure Spring Cloud

Para remover uma aplicação Cloud spring, utilize o cmdlet [Remove-AzSpringCloudApp.](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) O exemplo a seguir elimina a `gateway` aplicação no grupo de serviços e recursos especificados.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Excluir um serviço Azure Spring Cloud

Para remover um serviço Azure Spring Cloud, utilize o [cmdlet Remove-AzSpringCloud.](/powershell/module/Az.SpringCloud/remove-azspringcloud) O exemplo a seguir elimina o serviço Azure Spring Cloud especificado.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Passos seguintes

[Recursos de desenvolvimento da Azure Spring Cloud.](spring-cloud-resources.md)
