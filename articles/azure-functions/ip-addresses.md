---
title: Endereços IP no Azure Functions
description: Saiba como localizar endereços IP de entrada e saída para aplicativos de funções e o que os faz alterar.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 327d616c36bcbbb1562349afffd529efb2b5d27f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230332"
---
# <a name="ip-addresses-in-azure-functions"></a>Endereços IP no Azure Functions

Este artigo explica os seguintes tópicos relacionados aos endereços IP dos aplicativos de funções:

* Como localizar os endereços IP atualmente em uso por um aplicativo de funções.
* O que faz com que os endereços IP de um aplicativo de funções sejam alterados.
* Como restringir os endereços IP que podem acessar um aplicativo de funções.
* Como obter endereços IP dedicados para um aplicativo de funções.

Os endereços IP são associados a aplicativos de funções, não com funções individuais. As solicitações HTTP de entrada não podem usar o endereço IP de entrada para chamar funções individuais; Eles devem usar o nome de domínio padrão (functionappname.azurewebsites.net) ou um nome de domínio personalizado.

## <a name="function-app-inbound-ip-address"></a>Endereço IP de entrada do aplicativo de funções

Cada aplicativo de funções tem um único endereço IP de entrada. Para encontrar esse endereço IP:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até o aplicativo de funções.
3. Selecione **recursos de plataforma**.
4. Selecione **Propriedades**e o endereço IP de entrada aparece em **endereço IP virtual**.

## <a name="find-outbound-ip-addresses"></a>Endereços IP de saída do aplicativo de funções

Cada aplicativo de funções tem um conjunto de endereços IP de saída disponíveis. Qualquer conexão de saída de uma função, como para um banco de dados back-end, usa um dos endereços IP de saída disponíveis como o endereço IP de origem. Você não pode saber antecipadamente qual endereço IP uma determinada conexão usará. Por esse motivo, o serviço de back-end deve abrir seu firewall para todos os endereços IP de saída do aplicativo de funções.

Para localizar os endereços IP de saída disponíveis para um aplicativo de funções:

1. Entre no [Azure Resource Explorer](https://resources.azure.com).
2. Selecione **assinaturas > {sua assinatura} > provedores > sites > da Microsoft. Web**.
3. No painel JSON, localize o site com uma propriedade `id` que termine no nome do seu aplicativo de funções.
4. Consulte `outboundIpAddresses` e `possibleOutboundIpAddresses`. 

O conjunto de `outboundIpAddresses` está disponível no momento para o aplicativo de funções. O conjunto de `possibleOutboundIpAddresses` inclui endereços IP que estarão disponíveis somente se o aplicativo de funções for [dimensionado para outros tipos de preço](#outbound-ip-address-changes).

Uma maneira alternativa de localizar os endereços IP de saída disponíveis é usando o [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Quando um aplicativo de funções que é executado no [plano de consumo](functions-scale.md#consumption-plan) é dimensionado, um novo intervalo de endereços IP de saída pode ser atribuído. Quando em execução no plano de consumo, poderá ter lista aprovada integral do Datacenter.

## <a name="data-center-outbound-ip-addresses"></a>Endereços IP de saída do Data Center

Se você precisar colocar os endereços IP de saída usados por seus aplicativos de funções na lista de permissões, outra opção será a lista de permissões do aplicativo de funções ' data center (região do Azure). Você pode [baixar um arquivo JSON que lista os endereços IP para todos os data centers do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Em seguida, localize o fragmento JSON que se aplica à região em que seu aplicativo de funções é executado.

Por exemplo, é assim que o fragmento JSON da Europa ocidental poderia ser:

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

 Para obter informações sobre quando esse arquivo é atualizado e quando os endereços IP são alterados, expanda a seção **detalhes** da [página do centro de download](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a>Alterações de endereço IP de entrada

O endereço IP de entrada **pode ser** alterado quando você:

- Exclua um aplicativo de funções e recrie-o em um grupo de recursos diferente.
- Exclua o último aplicativo de funções em uma combinação de grupo de recursos e região e recrie-o.
- Excluir uma associação SSL, como durante a [renovação do certificado](../app-service/configure-ssl-certificate.md#renew-certificate)).

Quando seu aplicativo de funções é executado em um [plano de consumo](functions-scale.md#consumption-plan), o endereço IP de entrada também pode ser alterado quando você não executou nenhuma ação como aquelas listadas.

## <a name="outbound-ip-address-changes"></a>Alterações de endereço IP de saída

O conjunto de endereços IP de saída disponíveis para um aplicativo de funções pode ser alterado quando você:

* Execute qualquer ação que possa alterar o endereço IP de entrada.
* Altere o tipo de preço do plano do serviço de aplicativo. A lista de todos os endereços IP de saída possíveis que seu aplicativo pode usar, para todos os tipos de preço, está na propriedade `possibleOutboundIPAddresses`. Consulte [Localizar IPS de saída](#find-outbound-ip-addresses).

Quando seu aplicativo de funções é executado em um [plano de consumo](functions-scale.md#consumption-plan), o endereço IP de saída também pode ser alterado quando você não executou nenhuma ação como aquelas listadas.

Para forçar deliberadamente uma alteração de endereço IP de saída:

1. Dimensione o plano do serviço de aplicativo para cima ou para baixo entre os tipos de preço Standard e Premium v2.
2. Aguarde 10 minutos.
3. Dimensione de volta para onde você começou.

## <a name="ip-address-restrictions"></a>Restrições de endereço IP

Você pode configurar uma lista de endereços IP que deseja permitir ou negar acesso a um aplicativo de funções. Para obter mais informações, consulte [Azure app restrições de IP estático do serviço](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Endereços IP dedicados

Se você precisar de endereços IP estáticos dedicados, recomendamos os [ambientes do serviço de aplicativo](../app-service/environment/intro.md) (a [camada isolada](https://azure.microsoft.com/pricing/details/app-service/) dos planos do serviço de aplicativo). Para obter mais informações, consulte [ambiente do serviço de aplicativo endereços IP](../app-service/environment/network-info.md#ase-ip-addresses) e [como controlar o tráfego de entrada para um ambiente do serviço de aplicativo](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Para descobrir se seu aplicativo de funções é executado em um Ambiente do Serviço de Aplicativo:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até o aplicativo de funções.
3. Selecione a guia **visão geral** .
4. A camada de plano do serviço de aplicativo aparece em **plano do serviço de aplicativo/tipo de preço**. O tipo de preço Ambiente do Serviço de Aplicativo é **isolado**.
 
Como alternativa, você pode usar o [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

O `sku` de Ambiente do Serviço de Aplicativo é `Isolated`.

## <a name="next-steps"></a>Passos seguintes

Uma causa comum das alterações de IP é as alterações de escala do aplicativo de funções. [Saiba mais sobre o dimensionamento do aplicativo de funções](functions-scale.md).
