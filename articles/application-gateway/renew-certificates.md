---
title: Renovar um certificado Azure Application Gateway
description: Saiba como renovar um certificado associado a um ouvinte de gateway de aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: de57a58f7c891009d2e0cc43b351c2cad42a2766
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807881"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway de Aplicação

Em algum momento, terá de renovar os seus certificados se configurar o seu gateway de aplicações para encriptação TLS/SSL.

Pode renovar um certificado associado a um ouvinte utilizando o portal Azure, Azure PowerShell ou Azure CLI:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvintes do seu gateway de aplicações. Clique no ouvinte que tem um certificado que precisa de ser renovado e, em seguida, clique em **Renovar ou editar o certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Faça o upload do seu novo certificado PFX, dê-lhe um nome, escreva a palavra-passe e, em seguida, clique em **Guardar**.

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

Para aprender a configurar o descarregamento de TLS com gateway de aplicação Azure, consulte [Configure TLS Offload](application-gateway-ssl-portal.md)
