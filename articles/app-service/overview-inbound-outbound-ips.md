---
title: Endereços IP de entrada/saída
description: Saiba como os endereços IP de entrada e saída são usados no Serviço de Aplicações Azure, quando mudam e como encontrar os endereços para a sua aplicação.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279211"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Endereços IP de entrada e saída no Serviço de Aplicações Azure

[O Azure App Service](overview.md) é um serviço multi-inquilino, com exceção dos Ambientes de Serviço de [Aplicações.](environment/intro.md) As aplicações que não se encontram num ambiente de Serviço de Aplicações (não no [nível isolado)](https://azure.microsoft.com/pricing/details/app-service/)partilham a infraestrutura de rede com outras aplicações. Como resultado, os endereços IP de entrada e saída de uma aplicação podem ser diferentes, e podem até mudar em determinadas situações. 

[Os Ambientes de Serviço](environment/intro.md) de Aplicações utilizam infraestruturas de rede dedicadas, pelo que as aplicações que executam um ambiente de Serviço de Aplicações obtêm endereços IP estáticos e dedicados tanto para ligações de entrada como para saída.

## <a name="when-inbound-ip-changes"></a>Quando o IP de entrada muda

Independentemente do número de instâncias escalonadas, cada aplicação tem um único endereço IP de entrada. O endereço IP de entrada pode ser alterado quando executa uma das seguintes ações:

- Elimine uma aplicação e recrie-a num grupo de recursos diferente.
- Elimine a última aplicação numa combinação de grupo de recursos _e_ região e recrie-a.
- Eliminar uma ligação SSL existente, como durante a renovação do certificado (ver [certificado Renovar).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Encontre o IP de entrada

Basta executar o seguinte comando num terminal local:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Obtenha um IP estático de entrada

Às vezes, pode querer um endereço IP dedicado e estático para a sua aplicação. Para obter um endereço IP de entrada estático, é necessário configurar uma [ligação SSL baseada em IP](configure-ssl-bindings.md#secure-a-custom-domain). Se não necessitar de funcionalidade SSL para proteger a sua aplicação, pode até fazer o upload de um certificado auto-assinado para esta ligação. Numa ligação SSL baseada em IP, o certificado está vinculado ao próprio endereço IP, pelo que o Serviço de Aplicações prevê um endereço IP estático para que isso aconteça. 

## <a name="when-outbound-ips-change"></a>Quando os IPs de saída mudam

Independentemente do número de instâncias escalonadas, cada aplicação tem um número definido de endereços IP de saída a qualquer momento. Qualquer ligação de saída da aplicação do Serviço de Aplicações, como uma base de dados de back-end, utiliza um dos endereços IP de saída como endereço IP de origem. Não é possível saber previamente qual o endereço IP que uma determinada instância de aplicação utilizará para fazer a ligação de saída, pelo que o seu serviço back-end deve abrir a sua firewall a todos os endereços IP de saída da sua aplicação.

O conjunto de endereços IP de saída para a sua aplicação muda quando escala a sua aplicação entre os níveis inferiores **(Básico,** **Standard**e **Premium)** e o nível **Premium V2.**

Pode encontrar o conjunto de todos os possíveis endereços IP de saída que a `possibleOutboundIpAddresses` sua aplicação possa utilizar, independentemente dos níveis de preços, procurando a propriedade ou no campo adicional de **endereços IP outbound** na lâmina **Propriedades** no portal Azure. Ver [Localizar IPs de saída](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Descubra iPs de saída

Para encontrar os endereços IP de saída atualmente utilizados pela sua aplicação no portal Azure, clique em **Propriedades** na navegação à esquerda da sua aplicação. Estão listados no campo de **endereços IP outbound.**

Pode encontrar as mesmas informações executando o seguinte comando na [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Para encontrar _todos os_ possíveis endereços IP de saída para a sua aplicação, independentemente dos níveis de preços, clique em **Propriedades** na navegação à esquerda da sua aplicação. Estão listados no campo **de endereços IP de saída adicional.**

Pode encontrar as mesmas informações executando o seguinte comando na [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Passos seguintes

Aprenda a restringir o tráfego de entrada através de endereços IP de origem.

> [!div class="nextstepaction"]
> [Restrições ip estáticas](app-service-ip-restrictions.md)
