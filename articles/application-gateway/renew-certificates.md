---
title: Renovar um certificado de gateway de aplicação Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway de aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311951"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados gateway de aplicação

A dada altura, terá de renovar os seus certificados se configurar a sua porta de aplicação para encriptação TLS/SSL.

Pode renovar um certificado associado a um ouvinte utilizando o portal Azure, Azure PowerShell ou Azure CLI:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvintes da sua aplicação. Clique no ouvinte que tem um certificado que precisa de ser renovado e, em seguida, clique em **Renovar ou editar certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Faça upload do seu novo certificado PFX, dê-lhe um nome, escreva a palavra-passe e clique em **Guardar**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para renovar o seu certificado utilizando o Azure PowerShell, utilize o seguinte script:

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

Para aprender a configurar o Descarregamento de TLS com o Gateway de Aplicação Azure, consulte [Configure TLS Offload](application-gateway-ssl-portal.md)
