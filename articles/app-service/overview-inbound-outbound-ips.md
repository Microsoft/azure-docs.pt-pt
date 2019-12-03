---
title: Endereços IP de entrada/saída
description: Saiba como os endereços IP de entrada e saída são usados no serviço Azure App, quando eles mudam e como encontrar os endereços para seu aplicativo.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671593"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Endereços IP de entrada e saída no serviço Azure App

[Azure app serviço](overview.md) é um serviço multilocatário, exceto para [ambientes de serviço de aplicativo](environment/intro.md). Os aplicativos que não estão em um ambiente do serviço de aplicativo (não na [camada isolada](https://azure.microsoft.com/pricing/details/app-service/)) compartilham a infraestrutura de rede com outros aplicativos. Como resultado, os endereços IP de entrada e saída de um aplicativo podem ser diferentes e podem até mesmo mudar em determinadas situações. 

Os [ambientes de serviço de aplicativo](environment/intro.md) usam infraestruturas de rede dedicadas, de modo que os aplicativos em execução em um ambiente de serviço de aplicativo obtenham endereços IP dedicados e estáticos para conexões de entrada e saída.

## <a name="when-inbound-ip-changes"></a>Quando o IP de entrada é alterado

Independentemente do número de instâncias escaladas horizontalmente, cada aplicativo tem um único endereço IP de entrada. O endereço IP de entrada pode ser alterado quando você executa uma das seguintes ações:

- Exclua um aplicativo e recrie-o em um grupo de recursos diferente.
- Exclua o último aplicativo em um grupo de recursos _e_ uma combinação de região e recrie-o.
- Exclua uma associação SSL existente, como durante a renovação do certificado (consulte [renovar certificado](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Localizar o IP de entrada

Basta executar o seguinte comando em um terminal local:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Obter um IP de entrada estático

Às vezes, você pode querer um endereço IP estático dedicado para seu aplicativo. Para obter um endereço IP de entrada estático, você precisa configurar uma [Associação SSL com base em IP](configure-ssl-bindings.md#secure-a-custom-domain). Se você não precisar realmente da funcionalidade SSL para proteger seu aplicativo, você pode até mesmo carregar um certificado autoassinado para essa associação. Em uma associação SSL com base em IP, o certificado é associado ao próprio endereço IP, portanto, o serviço de aplicativo provisiona um endereço IP estático para que ele aconteça. 

## <a name="when-outbound-ips-change"></a>Quando os IPs de saída forem alterados

Independentemente do número de instâncias escaladas horizontalmente, cada aplicativo tem um número definido de endereços IP de saída em um determinado momento. Qualquer conexão de saída do aplicativo do serviço de aplicativo, como para um banco de dados back-end, usa um dos endereços IP de saída como o endereço IP de origem. Você não pode saber antecipadamente qual endereço IP uma determinada instância de aplicativo usará para fazer a conexão de saída, de modo que seu serviço de back-end deve abrir seu firewall para todos os endereços IP de saída de seu aplicativo.

O conjunto de endereços IP de saída para seu aplicativo é alterado quando você dimensiona seu aplicativo entre as camadas inferiores (**Basic**, **Standard**e **Premium**) e a camada **Premium v2** .

Você pode encontrar o conjunto de todos os endereços IP de saída possíveis que seu aplicativo pode usar, independentemente dos tipos de preço, procurando a propriedade `possibleOutboundIpAddresses` ou no campo **endereços IP de saída adicionais** na folha **Propriedades** do portal do Azure. Consulte [Localizar IPS de saída](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Localizar IPs de saída

Para localizar os endereços IP de saída usados atualmente pelo seu aplicativo no portal do Azure, clique em **Propriedades** na navegação esquerda do seu aplicativo. Eles são listados no campo **endereços IP de saída** .

Você pode encontrar as mesmas informações executando o comando a seguir no [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Para localizar _todos os_ endereços IP de saída possíveis para seu aplicativo, independentemente dos tipos de preço, clique em **Propriedades** na navegação esquerda do seu aplicativo. Eles são listados no campo **endereços IP de saída adicionais** .

Você pode encontrar as mesmas informações executando o comando a seguir no [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Passos seguintes

Saiba como restringir o tráfego de entrada por endereços IP de origem.

> [!div class="nextstepaction"]
> [Restrições de IP estático](app-service-ip-restrictions.md)
