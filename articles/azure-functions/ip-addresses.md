---
title: Endereços IP nas funções do Azure
description: Saiba como localizar os endereços IP de entrada e saídos para aplicações de função e o que faz com que eles alterar.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035866"
---
# <a name="ip-addresses-in-azure-functions"></a>Endereços IP nas funções do Azure

Este artigo explica os seguintes tópicos relacionados com endereços IP das aplicações de funções:

* Como encontrar os endereços IP atualmente em utilização por uma aplicação de funções.
* O que faz com que uma função de endereços IP da aplicação ser alterada.
* Como restringir os endereços IP que podem aceder a uma aplicação de funções.
* Como obter endereços IP dedicados para uma aplicação de funções.

Endereços IP estão associados com aplicações de funções, não com funções individuais. Pedidos HTTP recebidos não é possível utilizar o endereço IP de entrada para chamar funções individuais; terão de utilizar o nome de domínio predefinido (functionappname.azurewebsites.net) ou um nome de domínio personalizado.

## <a name="function-app-inbound-ip-address"></a>Aplicação de funções endereço IP de entrada

Cada aplicação de função tem um único endereço IP de entrada. Para localizar esse endereço IP:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a aplicação de função.
3. Selecione **funcionalidades de plataforma**.
4. Selecione **propriedades**, e o endereço IP de entrada aparece sob **endereço Virtual IP**.

## <a name="find-outbound-ip-addresses"></a>Endereços IP de saída de aplicação de função

Cada aplicação de função tem um conjunto de endereços IP de saída disponíveis. Qualquer ligação de saída de uma função, como para uma base de dados de back-end, utiliza um dos endereços IP de saída disponíveis, como o endereço IP de origem. Não é possível saber antecipadamente qual o endereço IP uma determinada ligação utilizará. Por esse motivo, o seu serviço de back-end tem de abrir a firewall para todos os endereços IP de saída da aplicação de função.

Para localizar os endereços IP de saída disponíveis para uma aplicação de funções:

1. Inicie sessão para o [Explorador de recursos do Azure](https://resources.azure.com).
2. Selecione **subscrições > {a sua subscrição} > fornecedores > Microsoft. Web > sites**.
3. No painel de JSON, encontrar o site com um `id` propriedade que termina em nome da sua aplicação de funções.
4. Ver `outboundIpAddresses` e `possibleOutboundIpAddresses`. 

O conjunto de `outboundIpAddresses` está atualmente disponível para a aplicação de funções. O conjunto de `possibleOutboundIpAddresses` inclui os endereços IP que vão estar disponíveis apenas se a aplicação de funções [pode ser dimensionada para outros escalões de preços](#outbound-ip-address-changes).

Uma forma alternativa para localizar os endereços IP de saída disponíveis é utilizando o [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Quando uma aplicação de função que é executado no [plano de consumo](functions-scale.md#consumption-plan) é ajustada, pode ser atribuído um novo intervalo de endereços IP de saída. Quando em execução no plano de consumo, poderá ter lista aprovada integral do Datacenter.

## <a name="data-center-outbound-ip-addresses"></a>Endereços IP de saída do Centro de dados

Se precisar de lista de permissões os endereços IP de saída utilizado por aplicações de funções, outra opção consiste em lista de permissões, o Centro de dados de aplicações function App (região do Azure). Pode [transferir um ficheiro JSON que apresenta uma lista de endereços IP para todos os centros de dados do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Em seguida, localize o fragmento JSON, que aplica-se para a região que a aplicação de função for executada no.

Por exemplo, este é o que o fragmento JSON na Europa Ocidental pode ter o seguinte aspeto:

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

 Para obter informações sobre o quando este ficheiro é atualizado e quando os endereços IP mudam, expanda o **detalhes** secção a [página do Centro de transferências](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a>Entrada alterações ao endereço IP

O endereço IP de entrada **poderá** alterar quando:

- Eliminar uma aplicação de funções e recrie-a num grupo de recursos diferentes.
- Eliminar a última aplicação de funções numa combinação de região e grupo de recursos e recriá-la.
- Eliminar um enlace de SSL, tal como durante [renovação de certificado](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)).

Quando a aplicação function app é executada [plano de consumo](functions-scale.md#consumption-plan), o endereço IP de entrada também pode mudar quando ainda não foram todas as ações, como aqueles listados.

## <a name="outbound-ip-address-changes"></a>Saída alterações ao endereço IP

O conjunto de IP de saída disponível endereços para uma aplicação de funções podem ser alteradas quando:

* Tome nenhuma ação que pode alterar o endereço IP de entrada.
* Altere o seu plano de serviço de aplicações, escalão de preço. A lista de todos os possíveis endereços IP de saída pode utilizar a sua aplicação, para todos os escalões de preços, está no `possibleOutboundIPAddresses` propriedade. Ver [encontrar IPs de saída](#find-outbound-ip-addresses).

Quando a aplicação function app é executada [plano de consumo](functions-scale.md#consumption-plan), o endereço IP de saída também pode mudar quando ainda não foram todas as ações, como aqueles listados.

Para forçar deliberadamente uma alteração de endereço IP saída:

1. Aumente ou reduzir verticalmente o seu plano do serviço de aplicações entre escalões de preço de Standard e Premium v2.
2. Aguarde 10 minutos.
3. Escala de volta para onde começou.

## <a name="ip-address-restrictions"></a>Restrições de endereço IP

Pode configurar uma lista de endereços IP que pretende permitir ou negar o acesso a uma aplicação de funções. Para obter mais informações, consulte [restrições de IP do Azure App Service estático](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Endereços IP dedicados

Se precisar de endereços IP estáticos e dedicados, recomendamos [ambientes de serviço de aplicações](../app-service/environment/intro.md) (o [escalão isolado](https://azure.microsoft.com/pricing/details/app-service/) dos planos do serviço de aplicações). Para obter mais informações, consulte [endereços IP de ambiente de serviço de aplicações](../app-service/environment/network-info.md#ase-ip-addresses) e [como controlar o tráfego de entrada para um ambiente de serviço de aplicações](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Para saber se a sua aplicação function app é executado num ambiente de serviço de aplicações:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a aplicação de função.
3. Selecione o **descrição geral** separador.
4. Escalão do plano de serviço de aplicações é apresentado em **plano/escalão de preço de serviço de aplicações**. O ambiente de serviço de aplicações escalão de preço **Isolated**.
 
Como alternativa, pode utilizar o [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

O App Service Environment `sku` é `Isolated`.

## <a name="next-steps"></a>Passos Seguintes

Uma causa comum das alterações IP é alterações de dimensionamento da aplicação de função. [Saiba mais sobre o dimensionamento da aplicação de função](functions-scale.md).
