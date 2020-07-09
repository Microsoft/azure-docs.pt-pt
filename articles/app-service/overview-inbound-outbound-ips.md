---
title: Endereços IP de entrada/saída
description: Saiba como os endereços IP de entrada e saída são usados no Azure App Service, quando eles mudam, e como encontrar os endereços para a sua aplicação.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8bcd80fde95e467513590f3ed09b1dadd2646aee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537632"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Endereços IP de entrada e saída no Serviço de Aplicações Azure

[O Azure App Service](overview.md) é um serviço multi-inquilino, com exceção [dos Ambientes de Serviço de Aplicações.](environment/intro.md) As aplicações que não estão num ambiente de Serviço de Aplicações (não no [nível isolado)](https://azure.microsoft.com/pricing/details/app-service/)partilham a infraestrutura de rede com outras aplicações. Como resultado, os endereços IP de entrada e saída de uma aplicação podem ser diferentes, podendo mesmo mudar em determinadas situações. 

[Os Ambientes de Serviço de Aplicações](environment/intro.md) utilizam infraestruturas de rede dedicadas, para que as aplicações que executam num ambiente de Serviço de Aplicações obtenham endereços IP estáticos e dedicados tanto para ligações de entrada como de saída.

## <a name="when-inbound-ip-changes"></a>Quando o IP de entrada muda

Independentemente do número de casos de escala, cada aplicação tem um único endereço IP de entrada. O endereço IP de entrada pode ser alterado quando efetuar uma das seguintes ações:

- Elimine uma aplicação e recrie-a num grupo de recursos diferente.
- Elimine a última aplicação numa combinação de grupo de recursos _e_ região e recrie-a.
- Eliminar uma ligação TLS existente, como durante a renovação do certificado (ver [certificado de renovação).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Encontre o IP de entrada

Basta executar o seguinte comando em um terminal local:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Obtenha um IP de entrada estática

Por vezes, pode querer um endereço IP dedicado e estático para a sua aplicação. Para obter um endereço IP de entrada estática, é necessário [proteger um domínio personalizado](configure-ssl-bindings.md#secure-a-custom-domain). Se realmente não precisar da funcionalidade TLS para proteger a sua aplicação, pode até carregar um certificado auto-assinado para esta vinculação. Numa ligação TLS baseada em IP, o certificado está ligado ao próprio endereço IP, pelo que o Serviço de Aplicações fornece um endereço IP estático para o fazer acontecer. 

## <a name="when-outbound-ips-change"></a>Quando os IPs de saída mudam

Independentemente do número de casos de escala, cada aplicação tem um número definido de endereços IP de saída em qualquer momento. Qualquer ligação de saída da aplicação Do Serviço de Aplicações, como uma base de dados back-end, utiliza um dos endereços IP de saída como o endereço IP de origem. Não é possível saber previamente qual endereço IP uma determinada instância da aplicação usará para fazer a ligação de saída, por isso o seu serviço back-end deve abrir a sua firewall para todos os endereços IP de saída da sua aplicação.

O conjunto de endereços IP de saída para a sua aplicação muda quando escala a sua aplicação entre os níveis inferiores **(Basic,** **Standard**e **Premium)** e o nível **Premium V2.**

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

## <a name="next-steps"></a>Próximos passos

Saiba como restringir o tráfego de entrada através de endereços IP de origem.

> [!div class="nextstepaction"]
> [Restrições estáticas de IP](app-service-ip-restrictions.md)
