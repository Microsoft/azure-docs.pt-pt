---
title: Endereços IP de entrada/saída
description: Saiba como os endereços IP de entrada e saída são usados no Azure App Service, quando eles mudam, e como encontrar os endereços para a sua aplicação.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 4237e51251a7ece05800aa7efa328a9c6cf65e76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591372"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Endereços IP de entrada e saída no Serviço de Aplicações Azure

[O Azure App Service](overview.md) é um serviço multi-inquilino, com exceção [dos Ambientes de Serviço de Aplicações.](environment/intro.md) As aplicações que não estão num ambiente de Serviço de Aplicações (não no [nível isolado)](https://azure.microsoft.com/pricing/details/app-service/)partilham a infraestrutura de rede com outras aplicações. Como resultado, os endereços IP de entrada e saída de uma aplicação podem ser diferentes, podendo mesmo mudar em determinadas situações.

[Os Ambientes de Serviço de Aplicações](environment/intro.md) utilizam infraestruturas de rede dedicadas, para que as aplicações que executam num ambiente de Serviço de Aplicações obtenham endereços IP estáticos e dedicados tanto para ligações de entrada como de saída.

## <a name="how-ip-addresses-work-in-app-service"></a>Como os endereços IP funcionam no Serviço de Aplicações

Uma aplicação do Serviço de Aplicações funciona num plano de Serviço de Aplicações e os planos do Serviço de Aplicações são implantados numa das unidades de implantação da infraestrutura Azure (chamada internamente de espaço web). A cada unidade de implantação é atribuído um conjunto de endereços IP virtuais, que inclui um endereço IP de entrada pública e um conjunto de [endereços IP de saída](#find-outbound-ips). Todos os planos do Serviço de Aplicações na mesma unidade de implementação, e as instâncias de aplicação que nelas funcionam, partilham o mesmo conjunto de endereços IP virtuais. Para um Ambiente de Serviço de Aplicações (um plano de Serviço de Aplicações em [nível isolado),](https://azure.microsoft.com/pricing/details/app-service/)o plano de Serviço de Aplicações é a própria unidade de implantação, pelo que os endereços IP virtuais são dedicados a ele como resultado.

Como não está autorizado a mover um plano de Serviço de Aplicações entre unidades de implementação, os endereços IP virtuais atribuídos à sua app geralmente permanecem os mesmos, mas existem exceções.

## <a name="when-inbound-ip-changes"></a>Quando o IP de entrada muda

Independentemente do número de casos de escala, cada aplicação tem um único endereço IP de entrada. O endereço IP de entrada pode ser alterado quando efetuar uma das seguintes ações:

- Elimine uma aplicação e recrie-a num grupo de recursos diferente (a unidade de implantação pode mudar).
- Elimine a última aplicação numa combinação de grupo de recursos _e_ região e recrie-a (a unidade de implantação pode mudar).
- Eliminar uma ligação TLS/SSL baseada em IP existente, como durante a renovação do certificado (ver [certificado de renovação).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Encontre o IP de entrada

Basta executar o seguinte comando em um terminal local:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Obtenha um IP de entrada estática

Por vezes, pode querer um endereço IP dedicado e estático para a sua aplicação. Para obter um endereço IP de entrada estática, é necessário [proteger um domínio personalizado](configure-ssl-bindings.md#secure-a-custom-domain). Se realmente não precisar da funcionalidade TLS para proteger a sua aplicação, pode até carregar um certificado auto-assinado para esta vinculação. Numa ligação TLS baseada em IP, o certificado está ligado ao próprio endereço IP, pelo que o Serviço de Aplicações fornece um endereço IP estático para o fazer acontecer. 

## <a name="when-outbound-ips-change"></a>Quando os IPs de saída mudam

Independentemente do número de casos de escala, cada aplicação tem um número definido de endereços IP de saída em qualquer momento. Qualquer ligação de saída da aplicação Do Serviço de Aplicações, como uma base de dados back-end, utiliza um dos endereços IP de saída como o endereço IP de origem. O endereço IP a utilizar é selecionado aleatoriamente no tempo de execução, pelo que o seu serviço back-end deve abrir a sua firewall para todos os endereços IP de saída para a sua aplicação.

O conjunto de endereços IP de saída para a sua aplicação muda quando executa uma das seguintes ações:

- Elimine uma aplicação e recrie-a num grupo de recursos diferente (a unidade de implantação pode mudar).
- Elimine a última aplicação numa combinação de grupo de recursos _e_ região e recrie-a (a unidade de implantação pode mudar).
- Dimensione a sua aplicação entre os níveis inferiores **(Básico,** **Standard** e **Premium)** e o nível **Premium V2** (os endereços IP podem ser adicionados ou subtraídos do conjunto).

Pode encontrar o conjunto de todos os possíveis endereços IP de saída que a sua aplicação pode utilizar, independentemente dos níveis de preços, procurando a `possibleOutboundIpAddresses` propriedade ou no campo de **endereços IP de saída adicional** na lâmina **Propriedades** no portal Azure. Ver [Encontrar IPs de saída](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Encontre IPs de saída

Para encontrar os endereços IP de saída atualmente utilizados pela sua aplicação no portal Azure, clique em **Propriedades** na navegação à esquerda da sua aplicação. Estão listados no campo **endereços IP de saída.**

Pode encontrar a mesma informação executando o seguinte comando na [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Para encontrar _todos os_ possíveis endereços IP de saída para a sua aplicação, independentemente dos níveis de preços, clique em **Propriedades** na navegação à esquerda da sua aplicação. Estão listados no campo **de endereços IP de saída adicional.**

Pode encontrar a mesma informação executando o seguinte comando na [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Passos seguintes

Saiba como restringir o tráfego de entrada através de endereços IP de origem.

> [!div class="nextstepaction"]
> [Restrições de IP estático](app-service-ip-restrictions.md)
