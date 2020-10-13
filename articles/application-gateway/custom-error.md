---
title: Criar páginas de erro personalizadas do Azure Application Gateway
description: Este artigo mostra-lhe como criar páginas de erro personalizadas do Application Gateway. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff3e9db4dcfe0bedc348323dbbddd1e66124fc5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360162"
---
# <a name="create-application-gateway-custom-error-pages"></a>Criar páginas de erro personalizadas do Portal da Aplicação Gateway

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Por exemplo, pode definir a sua própria página de manutenção se a sua aplicação web não for acessível. Ou, pode criar uma página de acesso não autorizada se um pedido malicioso for enviado para uma aplicação web.

As páginas de erro personalizadas são suportadas para os seguintes dois cenários:

- **Página de manutenção** - Esta página de erro personalizado é enviada em vez de uma página de 502 bad gateway. É mostrado quando o Application Gateway não tem apoio para encaminhar o tráfego para. Por exemplo, quando há manutenção programada ou quando um problema imprevisto afeta o acesso à piscina.
- **Página de acesso não autorizada** - Esta página de erro personalizado é enviada em vez de uma página de acesso 403 não autorizada. É mostrado quando a App Gateway WAF deteta tráfego malicioso e bloqueia-o.

Se um erro se originar dos servidores de backend, então é transmitido de volta não modificado para o chamador. Não é apresentada uma página de erro personalizada. O gateway de aplicações pode exibir uma página de erro personalizada quando um pedido não pode chegar ao backend.

As páginas de erro personalizadas podem ser definidas a nível global e ao nível do ouvinte:

- **Nível global** - a página de erro aplica-se ao tráfego de todas as aplicações web implantadas no gateway de aplicações.
- **Nível de ouvinte** - a página de erro é aplicada ao tráfego recebido no ouvinte.
- **Ambos** - a página de erro personalizada definida ao nível do ouvinte sobrepõe-se ao conjunto a nível global.

Para criar uma página de erro personalizada, tem de ter:

- um código de estado de resposta HTTP.
- a localização correspondente para a página de erro. 
- uma bolha de armazenamento Azure acessível ao público para a localização.
- um tipo de extensão *.htm ou *.html. 

O tamanho da página de erro deve ser inferior a 1 MB. Se houver imagens ligadas na página de erro, devem ser URLs absolutos acessíveis ao público ou linha de imagem codificada base64 na página de erro personalizado. Atualmente, não são suportadas ligações relativas com imagens no mesmo local da bolha. 

Depois de especificar uma página de erro, o gateway de aplicação descarrega-o a partir do local do bloco de armazenamento e guarda-o para a cache do gateway de aplicações local. Em seguida, a página de erro é servida diretamente a partir do gateway de aplicação. Para modificar uma página de erro personalizada existente, deve apontar para uma localização de blob diferente na configuração do gateway de aplicação. O gateway de aplicações não verifica periodicamente a localização do blob para obter novas versões.

## <a name="portal-configuration"></a>Configuração do portal

1. Navegue para o Gateway de Aplicações no portal e escolha um gateway de aplicações.

    ![A screenshot mostra a página de visão geral para um gateway de aplicações.](media/custom-error/ag-overview.png)
2. Clique **em Ouvintes** e navegue para um ouvinte em particular onde pretende especificar uma página de erro.

    ![Ouvintes do Gateway de Aplicação](media/custom-error/ag-listener.png)
3. Configure uma página de erro personalizada para um erro 403 WAF ou uma página de manutenção 502 ao nível do ouvinte.

    > [!NOTE]
    > A criação de páginas de erro personalizadas a nível global a partir do portal Azure não é suportada.

4. Especifique um URL blob acessível ao público para um determinado código de estado de erro e clique em **Guardar**. O Gateway de Aplicações está agora configurado com a página de erro personalizada.

   ![Códigos de erro do Gateway de Aplicação](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configuração do Azure PowerShell

Pode utilizar o Azure PowerShell para configurar uma página de erro personalizada. Por exemplo, uma página de erro personalizado global:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Ou uma página de erro de nível de ouvinte:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Para mais informações, consulte [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) e [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre diagnósticos de Gateway de aplicação, consulte [saúde back-end, registos de diagnóstico e métricas para o Gateway de Aplicações](application-gateway-diagnostics.md).
