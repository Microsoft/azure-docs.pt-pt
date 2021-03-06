---
title: ficheiro de inclusão do PowerShell para o DNS do Azure
description: ficheiro de inclusão do PowerShell para o DNS do Azure
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 32c516ccee3a9f4f7604a3e330285703a776b47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67133832"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurar o Azure PowerShell para o DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Tem de instalar a versão mais recente dos cmdlets PowerShell do Azure Resource Manager. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs).

Além disso, para utilizar Zonas Privada (Pré-visualização Pública), é necessário certificar-se de que tem as versões e módulos do PowerShell abaixo. 
* AzureRM.Dns - [versão 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) ou superior
* AzureRM.Network - [versão 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) ou superior

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
A saída dos comandos acima tem de mostrar que a versão do AzureRM.Dns é 4.1.0 ou versão superior, e para a versão AzureRM.Network é 5.4.0 ou versão superior.  

No caso do sistema ter versões anteriores, pode de instalar a versão mais recente do Azure PowerShell ou transferir e instalar os módulos acima a partir da Galeria do PowerShell, utilizando as ligações acima junto às versões do Módulo. Em seguida, pode instalá-los utilizando o abaixo comandos. Ambos os módulos são necessários e possuem uma retrocompatibilidade compatível total. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Abra a consola do PowerShell e ligue-se à sua conta. Para mais informações, consulte [Iniciar s-se-ão com a AzureRM](/powershell/azure/azurerm/authenticate-azureps).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Selecionar a subscrição
 
Verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

Escolha qual das subscrições do Azure utilizar.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são globais, não regionais, a opção de localização do grupo de recursos não tem impacto no DNS do Azure.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registar o fornecedor de recursos

O serviço do DNS do Azure é gerido pelo fornecedor de recursos Microsoft.Network. A subscrição do Azure tem de estar registada para utilizar este fornecedor de recursos antes de poder utilizar o DNS do Azure. Esta é uma operação única para cada subscrição.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
