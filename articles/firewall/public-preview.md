---
title: Ativar a pré-visualização pública do Firewall do Azure
description: Utilizar o Azure PowerShell para ativar a pré-visualização pública do Firewall do Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193073"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Ativar a pré-visualização pública do Firewall do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Ativar com o Azure PowerShell

Para ativar a pré-visualização pública do Firewall do Azure, utilize os seguintes comandos do Azure PowerShell:

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Demora até 30 minutos para o registo de funcionalidade concluir. Pode verificar o estado de registo ao executar os seguintes comandos do Azure PowerShell:

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
Após o registo estiver concluído, execute o seguinte comando:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](tutorial-firewall-deploy-portal.md)

