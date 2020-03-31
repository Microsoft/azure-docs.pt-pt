---
title: Criar páginas de erro personalizadas do Portal de Aplicação Azure
description: Este artigo mostra-lhe como criar páginas de erro personalizadas do Application Gateway. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129858"
---
# <a name="create-application-gateway-custom-error-pages"></a>Criar páginas de erro personalizadas do Gateway de aplicação

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Por exemplo, pode definir a sua própria página de manutenção se a sua aplicação web não for alcançável. Ou, pode criar uma página de acesso não autorizada se um pedido malicioso for enviado para uma aplicação web.

As páginas de erro personalizadas são suportadas para os dois seguintes cenários:

- **Página de manutenção** - Esta página de erro personalizado é enviada em vez de uma página de gateway 502. É mostrado quando o Application Gateway não tem backend para encaminhar o tráfego para. Por exemplo, quando há manutenção programada ou quando um problema imprevisto faz efeito no acesso à piscina.
- **Página de acesso não autorizada** - Esta página de erro personalizado é enviada em vez de uma página de acesso não autorizada 403. É mostrado quando o Gateway WAF deteta tráfego malicioso e bloqueia-o.

Se um erro tiver origem nos servidores de backend, então é passado sem alterações para o chamador. Não é apresentada uma página de erro personalizada. O gateway da aplicação pode apresentar uma página de erro personalizada quando um pedido não pode chegar ao backend.

As páginas de erro personalizadas podem ser definidas a nível global e ao nível do ouvinte:

- **Nível global** - a página de erro aplica-se ao tráfego de todas as aplicações web implementadas nessa porta de aplicação.
- **Nível** de ouvinte - a página de erro é aplicada ao tráfego recebido nesse ouvinte.
- **Ambos** - a página de erro personalizada definida ao nível do ouvinte sobrepõe-se à que se passa a nível global.

Para criar uma página de erro personalizada, deve ter:

- um código de estado de resposta HTTP.
- a localização correspondente para a página de erro. 
- uma bolha de armazenamento Azure acessível ao público para a localização.
- um tipo de extensão *.htm ou *.html. 

O tamanho da página de erro deve ser inferior a 1 MB. Se houver imagens ligadas na página de erro, devem ser URLs absolutos acessíveis ao público ou imagem codificada base64 na página de erro personalizada. Atualmente, não são suportados links relativos com imagens no mesmo local de bolha. 

Depois de especificar uma página de erro, o gateway da aplicação descarrega-a a partir da localização da bolha de armazenamento e guarda-a para a cache de gateway de aplicação local. Em seguida, a página de erro é servida diretamente a partir do gateway da aplicação. Para modificar uma página de erro personalizada existente, deve apontar para uma localização blob diferente na configuração do gateway da aplicação. O gateway da aplicação não verifica periodicamente a localização da bolha para obter novas versões.

## <a name="portal-configuration"></a>Configuração do portal

1. Navegue para Application Gateway no portal e escolha um gateway de aplicação.

    ![ag-visão geral](media/custom-error/ag-overview.png)
2. Clique em **Ouvintes** e navegue para um ouvinte específico onde pretende especificar uma página de erro.

    ![Ouvintes de Gateway de aplicação](media/custom-error/ag-listener.png)
3. Configure uma página de erro personalizada para um erro de 403 WAF ou uma página de manutenção de 502 ao nível do ouvinte.

    > [!NOTE]
    > A tualmente, não é suportado o apoio de páginas de erro personalizadas a nível global do portal Azure.

4. Especifique um URL de bolha acessível ao público para um determinado código de estado de erro e clique em **Guardar**. O Gateway de Aplicação está agora configurado com a página de erro personalizada.

   ![Códigos de erro gateway de aplicação](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configuração do Azure PowerShell

Pode utilizar o Azure PowerShell para configurar uma página de erro personalizada. Por exemplo, uma página de erro personalizado global:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Ou uma página de erro de nível de ouvinte:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Para mais informações, consulte [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) e [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre diagnósticos de Application Gateway, consulte [a saúde de back-end, registos de diagnóstico e métricas para Application Gateway](application-gateway-diagnostics.md).