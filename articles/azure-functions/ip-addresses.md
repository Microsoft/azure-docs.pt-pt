---
title: Endereços IP em Funções Azure
description: Saiba como encontrar endereços IP de entrada e saída para aplicações de função, e o que as faz mudar.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 2c248756899459e17082bcab863a4e857b594909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608236"
---
# <a name="ip-addresses-in-azure-functions"></a>Endereços IP em Funções Azure

Este artigo explica os seguintes conceitos relacionados com endereços IP de aplicações de funções:

* Localizar os endereços IP atualmente em uso por uma aplicação de função.
* Condições que fazem com que os endereços IP da aplicação de função se alterem.
* Restringir os endereços IP que podem aceder a uma aplicação de função.
* Definição de endereços IP dedicados para uma aplicação de função.

Os endereços IP estão associados a aplicações de funções, não a funções individuais. Os pedidos HTTP de entrada não podem utilizar o endereço IP de entrada para ligar para funções individuais; devem utilizar o nome de domínio predefinido (functionappname.azurewebsites.net) ou um nome de domínio personalizado.

## <a name="function-app-inbound-ip-address"></a>Endereço IP de entrada de aplicativo de função

Cada aplicação de função tem um único endereço IP de entrada. Para encontrar o endereço IP:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a aplicação de função.
3. Em **Definições**, selecione **Propriedades**. O endereço IP de entrada aparece no **endereço IP virtual**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Endereços IP de saída de aplicativo de função

Cada aplicação de função tem um conjunto de endereços IP de saída disponíveis. Qualquer ligação de saída de uma função, como uma base de dados back-end, utiliza um dos endereços IP de saída disponíveis como o endereço IP de origem. Não é possível saber previamente qual endereço IP utilizará uma determinada ligação. Por esta razão, o seu serviço back-end deve abrir a sua firewall para todos os endereços IP de saída da aplicação de função.

Para encontrar os endereços IP de saída disponíveis para uma aplicação de função:

1. Inscreva-se no [Azure Resource Explorer](https://resources.azure.com).
2. Selecione **subscrições > {sua subscrição} > fornecedores > sites microsoft.Web >**.
3. No painel JSON, encontre o site com um `id` imóvel que termina em nome da sua aplicação de função.
4. Ver `outboundIpAddresses` `possibleOutboundIpAddresses` e. 

O conjunto de `outboundIpAddresses` está atualmente disponível para a aplicação de função. O conjunto de `possibleOutboundIpAddresses` endereços IP inclui endereços IP que só estarão disponíveis se a aplicação de função [se escalar para outros níveis de preços](#outbound-ip-address-changes).

Uma forma alternativa de encontrar os endereços IP de saída disponíveis é utilizando a [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> Quando uma aplicação de função que executa o [plano de Consumo](consumption-plan.md) ou o plano [Premium](functions-premium-plan.md) for dimensionada, uma nova gama de endereços IP de saída pode ser atribuída. Ao executar qualquer um destes planos, poderá ter de adicionar todo o centro de dados a uma lista de autorizações.

## <a name="data-center-outbound-ip-addresses"></a>Endereços IP de saída do data center

Se precisar de adicionar os endereços IP de saída utilizados pelas suas aplicações de função a uma lista de admissões, outra opção é adicionar o data center das aplicações de função (região Azure) a uma lista de admissões. Você pode [baixar um ficheiro JSON que lista endereços IP para todos os centros de dados Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Em seguida, encontre o fragmento JSON que se aplica à região em que a sua aplicação de função funciona.

Por exemplo, o seguinte fragmento JSON é o que a lista de admissão para a Europa Ocidental pode parecer:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Para obter informações sobre quando este ficheiro é atualizado e quando os endereços IP mudarem, expanda a secção **Detalhes** da [página Centro de Descarregamento](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Alterações no endereço IP de entrada

O endereço IP de entrada **pode** mudar quando:

- Elimine uma aplicação de função e recrie-a num grupo de recursos diferente.
- Elimine a última aplicação de função numa combinação de grupo de recursos e região e reco crie-a.
- Eliminar uma ligação TLS, como durante a [renovação do certificado](../app-service/configure-ssl-certificate.md#renew-certificate).

Quando a sua aplicação de função funciona num [plano de Consumo](consumption-plan.md) ou num plano [Premium,](functions-premium-plan.md)o endereço IP de entrada também pode mudar mesmo quando não tenha tomado quaisquer ações como as [listadas acima.](#inbound-ip-address-changes)

## <a name="outbound-ip-address-changes"></a>Alterações no endereço IP de saída

O conjunto de endereços IP de saída disponíveis para uma aplicação de função pode mudar quando:

* Tome qualquer ação que possa alterar o endereço IP de entrada.
* Altere o seu nível de preços do plano de aplicações. A lista de todos os possíveis endereços IP de saída que a sua aplicação pode usar, para todos os níveis de preços, está na `possibleOutboundIPAddresses` propriedade. Ver [Encontrar IPs de saída](#find-outbound-ip-addresses).

Quando a sua aplicação de função funciona num [plano de Consumo](consumption-plan.md) ou num plano [Premium,](functions-premium-plan.md)o endereço IP de saída também pode mudar mesmo quando não tenha tomado quaisquer ações como as [listadas acima.](#inbound-ip-address-changes)

Utilize o seguinte procedimento para forçar deliberadamente uma alteração de endereço IP de saída:

1. Dimensione o seu plano de Serviço de Aplicações para cima ou para baixo entre os níveis de preços Standard e Premium v2.

2. Espere 10 minutos.

3. Recue para onde começou.

## <a name="ip-address-restrictions"></a>restrições de endereços IP

Pode configurar uma lista de endereços IP que pretende permitir ou negar o acesso a uma aplicação de função. Para obter mais informações, consulte [as restrições estáticas de IP do Serviço de Aplicações Azure.](../app-service/app-service-ip-restrictions.md)

## <a name="dedicated-ip-addresses"></a>Endereços IP dedicados

Existem várias estratégias para explorar quando a sua aplicação de função requer endereços IP estáticos e dedicados. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Gateway NAT de rede virtual para IP estático de saída

Pode controlar o endereço IP do tráfego de saída das suas funções utilizando uma porta de entrada NAT de rede virtual para direcionar o tráfego através de um endereço IP público estático. Você pode usar esta topologia ao executar um [plano Premium.](functions-premium-plan.md) Para saber mais, consulte [Tutorial: ControlE Azure Functions outbound IP com um gateway NAT de rede virtual Azure](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>Ambientes do App Service

Para controlo total sobre os endereços IP, tanto de entrada como de saída, recomendamos [Ambientes de Serviço de Aplicação](../app-service/environment/intro.md) (o [nível isolado](https://azure.microsoft.com/pricing/details/app-service/) dos planos de Serviço de Aplicações). Para obter mais informações, consulte [os endereços IP do App Service Environment](../app-service/environment/network-info.md#ase-ip-addresses) e como controlar o tráfego de entrada num Ambiente de Serviço de [Aplicações.](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)

Para saber se a sua aplicação de função funciona num Ambiente de Serviço de Aplicações:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a aplicação de função.
3. Selecione o separador **Descrição Geral**.
4. O nível do plano de serviço de aplicações aparece no **âmbito do plano/nível de preços do Serviço de Aplicação.** O nível de preços do Ambiente do Serviço de Aplicações está **isolado.**
 
Como alternativa, pode utilizar a [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

O Ambiente de Serviço de `sku` Aplicações é `Isolated` .

## <a name="next-steps"></a>Passos seguintes

Uma causa comum de alterações ip é alterações na escala de aplicações de função. [Saiba mais sobre o dimensionamento de aplicações de função.](functions-scale.md)
