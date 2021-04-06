---
title: Renovar um certificado Azure Application Gateway
description: Saiba como renovar um certificado associado a um ouvinte de gateway de aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622185"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway de Aplicação

Em algum momento, terá de renovar os seus certificados se configurar o seu gateway de aplicações para encriptação TLS/SSL.

Pode renovar um certificado associado a um ouvinte utilizando o portal Azure, Azure PowerShell ou Azure CLI:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvintes do seu gateway de aplicações. Selecione o ouvinte que tem um certificado que precisa de ser renovado e, em seguida, selecione **Renovar ou editar o certificado selecionado**.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Renovar certificado":::

Faça o upload do seu novo certificado PFX, dê-lhe um nome, escreva a palavra-passe e, em seguida, **selecione Guardar**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para renovar o seu certificado utilizando a Azure PowerShell, utilize o seguinte script:

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

## <a name="next-steps"></a>Passos seguintes

Para aprender a configurar o descarregamento de TLS com gateway de aplicação Azure, consulte [Configure TLS Offload](./create-ssl-portal.md)