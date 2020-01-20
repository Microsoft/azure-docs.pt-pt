---
title: Renovar um certificado de gateway Aplicativo Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway de aplicativo.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278005"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados do gateway de aplicativo

Em algum momento, você precisará renovar seus certificados se tiver configurado o gateway de aplicativo para criptografia SSL.

Você pode renovar um certificado associado a um ouvinte usando o portal do Azure, Azure PowerShell ou CLI do Azure:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue até os ouvintes do gateway de aplicativo. Clique no ouvinte que tem um certificado que precisa ser renovado e, em seguida, clique em **renovar ou editar certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Carregue seu novo certificado PFX, dê a ele um nome, digite a senha e, em seguida, clique em **salvar**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para renovar seu certificado usando Azure PowerShell, use o seguinte script:

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

Para saber como configurar o descarregamento de SSL com Aplicativo Azure gateway, consulte [Configurar descarregamento SSL](application-gateway-ssl-portal.md)
