---
title: Endereços IP em Funções Azure
description: Saiba como encontrar endereços IP de entrada e saída para aplicações de funções e o que as faz mudar.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: bfd2d573e0a1c78d0ef4c68be224f92e8f689f62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656772"
---
# <a name="ip-addresses-in-azure-functions"></a>Endereços IP em Funções Azure

Este artigo explica os seguintes tópicos relacionados com endereços IP de aplicações de função:

* Como encontrar os endereços IP atualmente utilizados por uma aplicação de função.
* O que faz com que os endereços IP de uma aplicação de função sejam alterados.
* Como restringir os endereços IP que podem aceder a uma aplicação de função.
* Como obter endereços IP dedicados para uma aplicação de função.

Os endereços IP estão associados a aplicações de funções, não a funções individuais. Os pedidos de HTTP de entrada não podem utilizar o endereço IP de entrada para ligar para funções individuais; devem utilizar o nome de domínio padrão (functionappname.azurewebsites.net) ou um nome de domínio personalizado.

## <a name="function-app-inbound-ip-address"></a>Endereço IP de entrada da aplicação de função

Cada aplicação de função tem um único endereço IP de entrada. Para encontrar o endereço IP:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a aplicação de funções.
3. Selecione **Funcionalidades de plataforma**.
4. Selecione **Propriedades**, e o endereço IP de entrada aparece no **endereço IP virtual**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Endereços IP de saída da aplicação de função

Cada aplicação de função tem um conjunto de endereços IP de saída disponíveis. Qualquer ligação de saída de uma função, como uma base de dados de back-end, utiliza um dos endereços IP de saída disponíveis como endereço IP de origem. Não é possível saber previamente qual o endereço IP que uma determinada ligação irá utilizar. Por esta razão, o seu serviço back-end deve abrir a sua firewall a todos os endereços IP de saída da aplicação de função.

Para encontrar os endereços IP de saída disponíveis para uma aplicação de função:

1. Inscreva-se no Explorador de [Recursos Azure.](https://resources.azure.com)
2. **Selecione subscrições > {a sua subscrição} > fornecedores > sites de > Microsoft.Web**.
3. No painel JSON, encontre o `id` site com uma propriedade que termina em nome da sua aplicação de funções.
4. Ver `outboundIpAddresses` `possibleOutboundIpAddresses`e. 

O conjunto `outboundIpAddresses` de está atualmente disponível para a aplicação de funções. O conjunto `possibleOutboundIpAddresses` de endereços IP que só estarão disponíveis se a aplicação de função [se dimensionar para outros níveis](#outbound-ip-address-changes)de preços .

Uma forma alternativa de encontrar os endereços IP de saída disponíveis é utilizando a [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Quando uma aplicação de função que funciona no [plano de consumo](functions-scale.md#consumption-plan) for dimensionada, pode ser atribuída uma nova gama de endereços IP de saída. Ao executar o plano de consumo, poderá ser necessário branquear todo o centro de dados.

## <a name="data-center-outbound-ip-addresses"></a>Endereços IP de saída do centro de dados

Se precisar de whitelist os endereços IP de saída utilizados pelas suas aplicações de função, outra opção é branquear o centro de dados das aplicações de funções (região do Azure). Pode [descarregar um ficheiro JSON que lista endereços IP para todos os centros](https://www.microsoft.com/en-us/download/details.aspx?id=56519)de dados do Azure . Em seguida, encontre o fragmento JSON que se aplica à região onde a sua aplicação de função funciona.

Por exemplo, é assim que o fragmento jSON da Europa Ocidental pode parecer:

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

 Para obter informações sobre quando este ficheiro é atualizado e quando os endereços IP mudarem, expanda a secção **Detalhes** da [página do Centro de Descarregamento](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Alterações no endereço IP de entrada

O endereço IP de entrada **pode** mudar quando:

- Elimine uma aplicação de função e recrie-a num grupo de recursos diferente.
- Elimine a última aplicação de função numa combinação de grupo de recursos e região e recrie-a.
- Eliminar uma ligação TLS, como durante a [renovação](../app-service/configure-ssl-certificate.md#renew-certificate)do certificado .

Quando a sua aplicação de funções funciona num plano de [consumo,](functions-scale.md#consumption-plan)o endereço IP de entrada também pode mudar mesmo quando não tomou nenhuma ação, como as [listadas acima](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Alterações no endereço IP de saída

O conjunto de endereços IP de saída disponíveis para uma aplicação de função pode mudar quando:

* Tome qualquer medida que possa alterar o endereço IP de entrada.
* Altere o seu nível de preços do plano de app service. A lista de todos os possíveis endereços IP de saída que a `possibleOutboundIPAddresses` sua aplicação pode usar, para todos os níveis de preços, está na propriedade. Ver [Localizar IPs de saída](#find-outbound-ip-addresses).

Quando a sua aplicação de funções funciona num plano de [consumo,](functions-scale.md#consumption-plan)o endereço IP de saída também pode mudar mesmo quando não tomou nenhuma ação, como as [listadas acima](#inbound-ip-address-changes).

Para forçar deliberadamente uma alteração de endereço IP de saída:

1. Dimensione o seu plano de Serviço de Aplicações para cima ou para baixo entre os níveis de preços Standard e Premium v2.
2. Espere 10 minutos.
3. Escala de volta para onde começou.

## <a name="ip-address-restrictions"></a>restrições de endereços IP

Pode configurar uma lista de endereços IP que pretende permitir ou negar o acesso a uma aplicação de função. Para mais informações, consulte [as restrições de IP estáticas do Serviço de Aplicação Azure.](../app-service/app-service-ip-restrictions.md)

## <a name="dedicated-ip-addresses"></a>Endereços IP dedicados

Se necessitar de endereços IP estáticos e dedicados, recomendamos [ambientes](../app-service/environment/intro.md) de serviço de aplicação (o [nível isolado](https://azure.microsoft.com/pricing/details/app-service/) dos planos do Serviço de Aplicações). Para mais informações, consulte os [endereços IP](../app-service/environment/network-info.md#ase-ip-addresses) do App Service Environment e como controlar o tráfego de entrada para um Ambiente de Serviço de [Aplicações](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Para saber se a sua aplicação de função funciona num Ambiente de Serviço de Aplicações:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para a aplicação de funções.
3. Selecione o separador **Descrição Geral**.
4. O nível de plano de serviço de aplicações aparece no **âmbito do plano/nível**de preços do App Service . O nível de preços do ambiente do serviço de aplicações é **isolado.**
 
Como alternativa, pode utilizar a [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

O Ambiente `sku` do `Isolated`Serviço de Aplicações é .

## <a name="next-steps"></a>Passos seguintes

Uma causa comum das alterações de IP são as alterações na escala da função. [Saiba mais sobre a escala de aplicativos](functions-scale.md)de função .
