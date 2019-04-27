---
title: Criar páginas de erro personalizado do Gateway de aplicação do Azure
description: Este artigo mostra-lhe como criar páginas de erro personalizado do Gateway de aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 02/14/2019
ms.date: 02/26/2019
ms.author: v-junlch
ms.openlocfilehash: abfe33ff679bef125d9bf5b78e1790a1a4c64863
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832035"
---
# <a name="create-application-gateway-custom-error-pages"></a>Criar páginas de erro personalizado do Gateway de aplicação

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Por exemplo, pode definir sua própria página de manutenção, se a sua aplicação web não estiver acessível. Em alternativa, pode criar uma página de acesso não autorizado se um pedido malicioso for enviado para um aplicativo web.

Páginas de erro personalizadas são suportadas para dois cenários a seguir:

- **Página de manutenção** -esta página de erro personalizado é enviada em vez de uma página de 502 gateway incorreto. É mostrado quando o Gateway de aplicação tem sem back-end para encaminhar o tráfego. Por exemplo, se existir tem agendada de manutenção ou quando um problema imprevisto afete o acesso de conjunto de back-end.
- **Página de acesso não autorizado** -esta página de erro personalizado é enviada em vez de uma página de acesso não autorizado 403. É mostrado quando o WAF do Gateway de aplicação Deteta o tráfego malicioso e bloqueia-lo.

Se um erro são originados pelos servidores de back-end, em seguida, ele será passado ao back sem modificações o autor da chamada. Não é apresentada uma página de erro personalizada. Gateway de aplicação pode apresentar uma página de erro personalizada quando um pedido não é possível alcançar o back-end.

Páginas de erro personalizadas podem ser definidas ao nível global e o nível de serviço de escuta:

- **Nível global** -a página de erro se aplica a tráfego para todas as aplicações web implementadas em que o gateway de aplicação.
- **Nível de serviço de escuta** -a página de erro é aplicada a tráfego recebido nesse serviço de escuta.
- **Ambos** -a página de erro personalizada definida no nível de serviço de escuta substitui o um conjunto nível global.

Para criar uma página de erro personalizado, tem de ter:
- um código de estado de resposta HTTP.
- a localização correspondente para a página de erro. 
- um blob de armazenamento do Azure acessível ao público para a localização.
- um tipo de extensão htm ou *.html. 

O tamanho da página de erro deve ser inferior a 1 MB. Se existirem imagens ligadas na página de erro, têm de ser acessíveis publicamente URLs absolutos ou codificado por base64 imagem embutida na página de erro personalizada. Ligações relativas com imagens na mesma localização do blob não são atualmente suportadas. 

Depois de especificar uma página de erro, o gateway de aplicação transfere-o partir da localização do blob de armazenamento e guarda-o no cache de gateway de aplicação local. Em seguida, a página de erro é atendida diretamente a partir do gateway de aplicação. Para modificar uma página de erro personalizada existente, tem de apontar para uma localização de blob diferente na configuração do gateway da aplicação. O gateway de aplicação periodicamente não verifique a localização de blob para buscar novas versões.

## <a name="portal-configuration"></a>Configuração do portal

1. Navegue para o Gateway de aplicação no portal e escolher um gateway de aplicação.

    ![ag-overview](./media/custom-error/ag-overview.png)
2. Clique em **serviços de escuta** e navegue para um determinado serviço de escuta em que pretende especificar uma página de erro.

    ![Serviços de escuta de Gateway de aplicação](./media/custom-error/ag-listener.png)
3. Configure uma página de erro personalizado para um erro 403 do WAF ou uma página de 502 Manutenção ao nível do serviço de escuta.

    > [!NOTE]
    > Criar páginas de erro personalizadas de nível global a partir do portal do Azure não é atualmente suportada.

4. Especifique um URL acessível publicamente blob para um código de estado de erro fornecida e clique em **guardar**. O Gateway de aplicação está agora configurado com a página de erro personalizada.

   ![Códigos de erro do Gateway de aplicação](./media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configuração do Azure PowerShell

Pode utilizar o Azure PowerShell para configurar uma página de erro personalizada. Por exemplo, uma página de erro personalizada global:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Ou uma página de erro de nível de serviço de escuta:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Para obter mais informações, consulte [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) e [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre o diagnóstico do Gateway de aplicação, consulte [estado de funcionamento do Back-end, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).

<!-- Update_Description: wording update -->
