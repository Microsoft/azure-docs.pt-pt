---
title: Criar páginas de erro personalizadas do gateway de Aplicativo Azure
description: Este artigo mostra como criar páginas de erro personalizadas do gateway de aplicativo. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129858"
---
# <a name="create-application-gateway-custom-error-pages"></a>Criar páginas de erro personalizadas do gateway de aplicativo

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Por exemplo, você pode definir sua própria página de manutenção se seu aplicativo Web não estiver acessível. Ou, você pode criar uma página de acesso não autorizado se uma solicitação mal-intencionada for enviada a um aplicativo Web.

As páginas de erro personalizadas têm suporte para os dois cenários a seguir:

- **Página de manutenção** -esta página de erro personalizada é enviada em vez de uma página 502 de gateway inadequado. Ele é mostrado quando o gateway de aplicativo não tem nenhum back-end para rotear o tráfego. Por exemplo, quando há manutenção agendada ou quando um problema imprevisto afeta o acesso ao pool de back-end.
- **Página de acesso não autorizado** – esta página de erro personalizada é enviada em vez de uma página de acesso não autorizado 403. Ele é mostrado quando o WAF do gateway de aplicativo detecta o tráfego mal-intencionado e o bloqueia.

Se um erro for proveniente dos servidores de back-end, ele será passado sem modificações de volta para o chamador. Uma página de erro personalizada não é exibida. O gateway de aplicativo pode exibir uma página de erro personalizada quando uma solicitação não pode alcançar o back-end.

Páginas de erro personalizadas podem ser definidas no nível global e no nível do ouvinte:

- **Nível global** – a página de erro se aplica ao tráfego para todos os aplicativos Web implantados nesse gateway de aplicativo.
- **Nível do ouvinte** – a página de erro é aplicada ao tráfego recebido nesse ouvinte.
- **Ambos** -a página de erro personalizada definida no nível do ouvinte substitui a definida no nível global.

Para criar uma página de erro personalizada, você deve ter:

- um código de status de resposta HTTP.
- o local correspondente para a página de erro. 
- um blob de armazenamento do Azure acessível publicamente para o local.
- um tipo de extensão *. htm ou *. html. 

O tamanho da página de erro deve ser menor que 1 MB. Se houver imagens vinculadas na página de erro, elas deverão ser URLs absolutas acessíveis publicamente ou a imagem codificada em base64 embutida na página de erro personalizada. No momento, não há suporte para links relativos com imagens no mesmo local de BLOB. 

Depois de especificar uma página de erro, o gateway de aplicativo baixa o local do blob de armazenamento e salva-o no cache do gateway de aplicativo local. Em seguida, a página de erro é servida diretamente do gateway de aplicativo. Para modificar uma página de erro personalizada existente, você deve apontar para um local de blob diferente na configuração do gateway de aplicativo. O gateway de aplicativo não verifica periodicamente o local do blob para buscar novas versões.

## <a name="portal-configuration"></a>Configuração do portal

1. Navegue até gateway de aplicativo no portal e escolha um gateway de aplicativo.

    ![ag-overview](media/custom-error/ag-overview.png)
2. Clique em **ouvintes** e navegue até um ouvinte específico no qual você deseja especificar uma página de erro.

    ![Ouvintes do gateway de aplicativo](media/custom-error/ag-listener.png)
3. Configure uma página de erro personalizada para um erro 403 WAF ou uma página de manutenção 502 no nível do ouvinte.

    > [!NOTE]
    > No momento, não há suporte para a criação de páginas de erro personalizadas de nível global do portal do Azure.

4. Especifique uma URL de blob publicamente acessível para um determinado código de status de erro e clique em **salvar**. O gateway de aplicativo agora está configurado com a página de erro personalizada.

   ![Códigos de erro do gateway de aplicativo](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configuração do Azure PowerShell

Você pode usar Azure PowerShell para configurar uma página de erro personalizada. Por exemplo, uma página de erro personalizada global:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Ou uma página de erro no nível do ouvinte:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Para obter mais informações, consulte [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) e [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o diagnóstico do gateway de aplicativo, consulte [integridade de back-end, logs de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md).