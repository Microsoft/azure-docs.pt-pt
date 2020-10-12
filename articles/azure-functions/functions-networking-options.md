---
title: Opções de rede das Funções do Azure
description: Uma visão geral de todas as opções de networking disponíveis em Funções Azure.
ms.topic: conceptual
ms.date: 4/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 271730e57a2d7ef8324420744b4bcd088b9809cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530098"
---
# <a name="azure-functions-networking-options"></a>Opções de rede das Funções do Azure

Este artigo descreve as funcionalidades de networking disponíveis nas opções de hospedagem para Funções Azure. Todas as seguintes opções de networking dão-lhe alguma capacidade de aceder a recursos sem usar endereços de internet-routable ou para restringir o acesso à Internet a uma aplicação de função.

Os modelos de hospedagem têm diferentes níveis de isolamento de rede disponíveis. Escolher o correto ajuda-o a cumprir os requisitos de isolamento da rede.

Pode hospedar aplicações de função de várias maneiras:

* Pode escolher entre opções de plano que funcionam numa infraestrutura multitenante, com vários níveis de conectividade de rede virtual e opções de escala:
    * O [plano de consumo](functions-scale.md#consumption-plan) escala dinamicamente em resposta à carga e oferece opções mínimas de isolamento de rede.
    * O [plano Premium](functions-scale.md#premium-plan) também escala dinamicamente e oferece um isolamento de rede mais abrangente.
    * O plano Azure [App Service](functions-scale.md#app-service-plan) funciona a uma escala fixa e oferece isolamento de rede semelhante ao plano Premium.
* Pode executar funções num [Ambiente de Serviço de Aplicações.](../app-service/environment/intro.md) Este método implementa a sua função na sua rede virtual e oferece controlo e isolamento completos da rede.

## <a name="matrix-of-networking-features"></a>Matriz de funcionalidades de networking

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-ip-restrictions"></a>Restrições ip de entrada

Pode utilizar restrições IP para definir uma lista de endereços IP encomendados por prioridade que sejam permitidos ou impedidos de aceder à sua aplicação. A lista pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, existe um "negar tudo" implícito no final da lista. As restrições ip funcionam com todas as opções de hospedagem de funções.

> [!NOTE]
> Com as restrições de rede em vigor, pode utilizar o editor do portal apenas a partir da sua rede virtual, ou quando tiver colocado o endereço IP da máquina que está a utilizar para aceder ao portal Azure na lista "Recetores Seguros". No entanto, ainda é possível aceder a quaisquer funcionalidades no separador **de funcionalidades** da Plataforma a partir de qualquer máquina.

Para saber mais, consulte [as restrições de acesso estático do Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>O acesso a sites privados

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

## <a name="virtual-network-integration"></a>Integração da rede virtual

A integração de rede virtual permite que a sua aplicação de função aceda a recursos dentro de uma rede virtual.
A Azure Functions suporta dois tipos de integração de rede virtual:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

A integração virtual da rede em Azure Functions utiliza infraestruturas partilhadas com aplicações web do App Service. Para saber mais sobre os dois tipos de integração de rede virtual, consulte:

* [Integração de rede virtual regional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integração da rede virtual exigida pelo Gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Para aprender a configurar a integração de rede virtual, consulte [integrar uma aplicação de função com uma rede virtual Azure.](functions-create-vnet.md)

## <a name="regional-virtual-network-integration"></a>Integração de rede virtual regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Ligar ao ponto final de serviço recursos seguros

Para fornecer um nível de segurança mais elevado, pode restringir uma série de serviços Azure a uma rede virtual utilizando pontos finais de serviço. Em seguida, deve integrar a sua aplicação de função com aquela rede virtual para aceder ao recurso. Esta configuração é suportada em todos os planos que suportam a integração de redes virtuais.

Para saber mais, consulte [os pontos finais do serviço de rede Virtual.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restringir a sua conta de armazenamento a uma rede virtual

Quando criar uma aplicação de função, deve criar ou ligar para uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Table. Não é possível utilizar quaisquer restrições de rede virtuais nesta conta. Se configurar um ponto final de serviço de rede virtual na conta de armazenamento que está a usar para a sua aplicação de função, essa configuração quebrará a sua aplicação.

Para saber mais, consulte os [requisitos da conta de Armazenamento.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="use-key-vault-references"></a>Utilizar as referências do Key Vault

Pode utilizar referências do Azure Key Vault para utilizar segredos do Azure Key Vault na sua aplicação Azure Functions sem exigir alterações de código. O Azure Key Vault é um serviço que fornece gestão centralizada de segredos, com controlo total sobre políticas de acesso e histórico de auditoria.

Atualmente, [as referências do Key Vault](../app-service/app-service-key-vault-references.md) não funcionarão se o cofre da chave estiver seguro com pontos finais de serviço. Para se ligar a um cofre de chaves utilizando a integração virtual da rede, tem de ligar para o Key Vault no seu código de aplicação.

## <a name="virtual-network-triggers-non-http"></a>Gatilhos de rede virtuais (não HTTP)

Atualmente, pode utilizar funções de gatilho não-HTTP a partir de uma rede virtual de uma de duas maneiras:

+ Execute a sua aplicação de função num plano Premium e ative o suporte ao gatilho da rede virtual.
+ Execute a sua aplicação de função num plano de Serviço de Aplicações ou ambiente de serviço de aplicações.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plano premium com gatilhos de rede virtual

Quando executar um plano Premium, pode ligar funções de gatilho não-HTTP a serviços que funcionam dentro de uma rede virtual. Para isso, tem de ativar o suporte de gatilho de rede virtual para a sua aplicação de função. A definição **de monitorização da escala de tempo** de execução encontra-se no portal [Azure](https://portal.azure.com) nas definições de tempo de funcionamento da função **de configuração**  >  **Function runtime settings**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Também pode ativar os gatilhos de rede virtuais utilizando o seguinte comando Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Os gatilhos de rede virtuais são suportados na versão 2.x e acima do tempo de execução de Funções. Os seguintes tipos de gatilho não-HTTP são suportados.

| Extensão | Versão mínima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou superior |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou superior|

> [!IMPORTANT]
> Quando ativa o suporte ao gatilho de rede virtual, apenas os tipos de gatilho mostrados na escala de tabela anterior dinamicamente com a sua aplicação. Ainda podes usar gatilhos que não estão na mesa, mas não estão além da contagem de exemplos pré-aquecidas. Para obter a lista completa de gatilhos, consulte [Triggers e encadernações](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plano de Serviço de Aplicações e Ambiente de Serviço de Aplicações com gatilhos de rede virtual

Quando a sua aplicação de função é executado num plano de Serviço de Aplicações ou num Ambiente de Serviço de Aplicações, pode utilizar funções de gatilho não-HTTP. Para que as suas funções sejam ativadas corretamente, tem de estar ligado a uma rede virtual com acesso ao recurso definido na ligação do gatilho.

Por exemplo, assuma que quer configurar a Azure Cosmos DB para aceitar o tráfego apenas a partir de uma rede virtual. Neste caso, deve implementar a sua aplicação de função num plano de Serviço de Aplicações que proporciona integração de rede virtual com essa rede virtual. A integração permite que uma função seja desencadeada por esse recurso DB da Azure Cosmos.

## <a name="hybrid-connections"></a>Ligações Híbridas

[Hybrid Connections](../azure-relay/relay-hybrid-connections-protocol.md) é uma característica do Azure Relay que pode usar para aceder a recursos de aplicações noutras redes. Fornece acesso da sua aplicação a um ponto final de aplicação. Não pode usá-lo para aceder à sua aplicação. As Ligações Híbridas estão disponíveis para funções que funcionam no Windows em todos, menos no plano de Consumo.

Como usado em Funções Azure, cada ligação híbrida está relacionada com um único hospedeiro TCP e combinação de porta. Isto significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicação, desde que aceda a uma porta de escuta TCP. A funcionalidade Ligações Híbridas não sabe nem se importa com o protocolo de aplicação ou o que está a aceder. Só dá acesso à rede.

Para saber mais, consulte a documentação do [Serviço de Aplicações para Ligações Híbridas.](../app-service/app-service-hybrid-connections.md) Estes mesmos passos de configuração suportam funções Azure.

>[!IMPORTANT]
> As Ligações Híbridas só são suportadas nos planos do Windows. Linux não é apoiado.

## <a name="outbound-ip-restrictions"></a>Restrições ip de saída

As restrições IP de saída estão disponíveis num plano Premium, plano de Serviço de Aplicações ou Ambiente de Serviço de Aplicações. Pode configurar restrições de saída para a rede virtual onde o seu Ambiente de Serviço de Aplicações está implantado.

Quando integra uma aplicação de função num plano Premium ou num plano de Serviço de Aplicações com uma rede virtual, a aplicação ainda pode fazer chamadas de saída para a internet por padrão. Ao adicionar a definição de aplicação, força todo o `WEBSITE_VNET_ROUTE_ALL=1` tráfego de saída a ser enviado para a sua rede virtual, onde as regras do grupo de segurança de rede podem ser usadas para restringir o tráfego.

## <a name="automation"></a>Automatização
As seguintes APIs permitem gerir programáticamente integrações de redes virtuais regionais:

+ **Azure CLI**: Utilize os [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) comandos para adicionar, listar ou remover uma integração regional de rede virtual.  
+ **Modelos ARM**: A integração regional da rede virtual pode ser ativada utilizando um modelo de Gestor de Recursos Azure. Para um exemplo completo, consulte [este modelo de arranque rápido de funções](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre networking e Funções Azure:

* [Acompanhe o tutorial sobre o início com integração de rede virtual](./functions-create-vnet.md)
* [Leia as FaQ de networking de funções](./functions-networking-faq.md)
* [Saiba mais sobre a integração de rede virtual com o Serviço de Aplicações/Funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais em Azure](../virtual-network/virtual-networks-overview.md)
* [Permitir mais funcionalidades de networking e controlo com Ambientes de Serviço de Aplicações](../app-service/environment/intro.md)
* [Conecte-se a recursos individuais no local sem alterações de firewall utilizando ligações híbridas](../app-service/app-service-hybrid-connections.md)
