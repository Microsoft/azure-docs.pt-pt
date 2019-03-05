---
title: Renovar um certificado de Gateway de aplicação do Azure
description: Saiba como renovar um certificado associado a um serviço de escuta do gateway de aplicação.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314522"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway de aplicação

Em algum momento, terá de renovar os certificados, se tiver configurado o gateway de aplicação para encriptação SSL.

Pode renovar um certificado associado a um serviço de escuta com o portal do Azure, o Azure PowerShell ou da CLI do Azure:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de serviço de escuta do portal, navegue para os serviços de escuta do gateway de aplicação. Clique em serviço de escuta que tem um certificado que tem de ser renovado e, em seguida, clique em **renovar ou editar certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Carregar o novo certificado PFX, atribua um nome, escreva a palavra-passe e, em seguida, clique em **guardar**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para renovar o certificado com o Azure PowerShell, utilize o seguinte script:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Passos Seguintes

Para saber como configurar a descarga de SSL com o Gateway de aplicação do Azure, veja [configurar a descarga de SSL](application-gateway-ssl-portal.md)
