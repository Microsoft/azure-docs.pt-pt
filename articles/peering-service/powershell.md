---
title: 'Registar uma ligação de Serviço de Peering - Azure PowerShell '
description: Neste tutorial aprenda a registar uma ligação de Serviço de Peering com o PowerShell.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400439"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Tutorial: Registar uma ligação de serviço de peering usando Azure PowerShell

Neste tutorial, você aprenderá a registar o Serviço de Peering usando a Azure PowerShell.

O Azure Peering Service é um serviço de networking que melhora a conectividade do cliente com os serviços na nuvem da Microsoft, tais como o Microsoft 365, Dynamics 365, software como serviço (SaaS), Azure ou quaisquer serviços da Microsoft acessíveis através da internet pública. Neste artigo, você aprenderá a registar uma ligação de Serviço de Peering usando Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma [conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o PowerShell localmente, este arranque rápido requer que utilize a versão 1.0 ou mais tarde do módulo Azure PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Para obter informações de instalação e atualização, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Finalmente, se estiver a executar o PowerShell localmente, também terá de `Connect-AzAccount` correr. Este comando cria uma ligação com O Azure.

Utilize o módulo Azure PowerShell para registar e gerir o Serviço de Peering. Pode registar-se ou gerir o Serviço de Peering a partir da linha de comando PowerShell ou em scripts.


## <a name="prerequisites"></a>Pré-requisitos  
Deve ter o seguinte:

### <a name="azure-account"></a>Conta do Azure

Deve ter uma conta Microsoft Azure válida e ativa. Esta conta é necessária para configurar a ligação serviço de peering. O Serviço de Observação é um recurso dentro das subscrições Azure.

### <a name="connectivity-provider"></a>Fornecedor de conectividade

Pode trabalhar com um fornecedor de serviços de internet ou um parceiro de intercâmbio de internet para obter o Peering Service para ligar a sua rede à rede Microsoft.

Certifique-se de que os fornecedores de conectividade são parceiros com a Microsoft.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Registar uma subscrição com o fornecedor de recursos e bandeira de recurso

Antes de avançar para os passos de registo do Serviço de Pares, registe a sua subscrição com o fornecedor de recursos e sinalize a bandeira de recurso utilizando o Azure PowerShell. Os comandos Azure PowerShell são especificados aqui:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Buscar a localização e o prestador de serviços 

Executar os seguintes comandos no Azure PowerShell para adquirir a localização e o prestador de serviços a que o Serviço de Pares deve estar ativado. 

Obtenha localizações do Serviço de Observação de Pares:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Obtenha fornecedores de serviços de peering:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Registar a ligação de Serviço de Pares

Registe a ligação de Serviço de Peering utilizando o seguinte conjunto de comandos via Azure PowerShell. Este exemplo regista o Serviço de Peering denominado myPeeringService.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Registar o prefixo do Serviço de Observação

Registe o prefixo fornecido pelo fornecedor de conectividade executando os seguintes comandos através do Azure PowerShell. Este exemplo regista o prefixo chamado myPrefix.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Listar todas as ligações de Serviços de Peering

Para ver a lista de todos os Serviços de Peering, executar o seguinte comando:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Listar todos os prefixos do Serviço de Observação

Para ver a lista de todos os prefixos do Serviço de Peering, execute o seguinte comando:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Remover o prefixo do serviço de peering

Para remover o prefixo do Serviço de Peering, executar o seguinte comando:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação do Serviço de Peering, consulte [a ligação do Serviço de Peering](connection.md).
- Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).
- Para registar uma ligação de Serviço de Peering utilizando o portal Azure, consulte [registar uma ligação de Serviço de Pares - portal Azure](azure-portal.md).
- Para registar uma ligação de Serviço de Peering utilizando o Azure CLI, consulte [registar uma ligação de Serviço de Pares - Azure CLI](cli.md).
