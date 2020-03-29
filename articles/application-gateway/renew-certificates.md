---
title: Renovar um certificado de gateway de aplicação Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway de aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278005"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados gateway de aplicação

A dada altura, terá de renovar os seus certificados se configurar a sua porta de aplicação para encriptação SSL.

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

Para aprender a configurar o Descarregamento sL com o Gateway de Aplicação Azure, consulte [Configure SSL Offload](application-gateway-ssl-portal.md)
