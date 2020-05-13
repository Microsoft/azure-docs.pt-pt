---
title: Opções de rede das Funções do Azure
description: Uma visão geral de todas as opções de networking disponíveis nas Funções Azure.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: ce1a214d39f958af36931192aad4561459ca0573
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121355"
---
# <a name="azure-functions-networking-options"></a>Opções de rede das Funções do Azure

Este artigo descreve as funcionalidades de networking disponíveis nas opções de hospedagem para funções Azure. Todas as seguintes opções de networking dão-lhe alguma capacidade de aceder a recursos sem usar endereços de saída da Internet ou para restringir o acesso à Internet a uma aplicação de função.

Os modelos de hospedagem têm diferentes níveis de isolamento de rede disponíveis. Escolher o correto ajuda-o a cumprir os requisitos de isolamento da rede.

Você pode hospedar aplicativos de função de várias maneiras:

* Pode escolher entre opções de plano que funcionam numa infraestrutura multiarrendatária, com vários níveis de conectividade de rede virtual e opções de escala:
    * O [plano de consumo](functions-scale.md#consumption-plan) escala dinamicamente em resposta à carga e oferece opções mínimas de isolamento da rede.
    * O [plano Premium](functions-scale.md#premium-plan) também escala dinamicamente e oferece um isolamento mais abrangente da rede.
    * O plano Azure [App Service](functions-scale.md#app-service-plan) funciona a uma escala fixa e oferece isolamento de rede semelhante ao plano Premium.
* Pode executar funções num Ambiente de Serviço de [Aplicações.](../app-service/environment/intro.md) Este método implementa a sua função na sua rede virtual e oferece controlo e isolamento completos da rede.

## <a name="matrix-of-networking-features"></a>Matriz de funcionalidades de networking

|                |[Plano de consumo](functions-scale.md#consumption-plan)|[Plano Premium](functions-scale.md#premium-plan)|[Plano de serviço de aplicações](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicações](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrições IP de entrada e acesso ao site privado](#inbound-ip-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração da rede virtual](#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Sim|
|[Gatilhos de rede virtuais (não HTTP)](#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|
|[Ligações híbridas](#hybrid-connections) (apenas janelas)|❌Não|✅Sim|✅Sim|✅Sim|
|[Restrições ip de saída](#outbound-ip-restrictions)|❌Não| ✅Sim|✅Sim|✅Sim|

## <a name="inbound-ip-restrictions"></a>Restrições ip de entrada

Pode utilizar restrições IP para definir uma lista ordenada por prioridade saqueada de endereços IP que são permitidos ou negados acesso à sua aplicação. A lista pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, existe um "negar tudo" implícito no final da lista. As restrições IP funcionam com todas as opções de hospedagem de funções.

> [!NOTE]
> Com restrições de rede em vigor, só pode utilizar o editor do portal a partir da sua rede virtual, ou quando tiver colocado o endereço IP da máquina que está a usar para aceder ao portal Azure na lista de Destinatários Seguros. No entanto, ainda pode aceder a quaisquer funcionalidades no separador **de funcionalidades** da Plataforma a partir de qualquer máquina.

Para saber mais, consulte [as restrições estáticas](../app-service/app-service-ip-restrictions.md)de acesso ao Azure App Service.

## <a name="private-site-access"></a>O acesso a sites privados

O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como uma rede virtual Azure.

* O acesso ao site privado está disponível nos planos [Premium](./functions-premium-plan.md), [Consumption](functions-scale.md#consumption-plan)e [App Service](functions-scale.md#app-service-plan) quando os pontos finais do serviço estão configurados.
    * Os pontos finais do serviço podem ser configurados numa base por app em **funções de Plataforma de**  >  **Configuração de**  >  **Configuração De Regra**de Adição de Restrições de Acesso  >  **Add Rule**Configurados . As redes virtuais podem agora ser selecionadas como um tipo de regra.
    * Para mais informações, consulte [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)do serviço de rede Virtual .
    * Tenha em mente que, com pontos finais de serviço, a sua função ainda tem acesso total à internet, mesmo com a integração virtual da rede configurada.
* O acesso ao site privado também está disponível dentro de um App Service Environment que está configurado com um equilibrista de carga interna (ILB). Para mais informações, consulte [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações](../app-service/environment/create-ilb-ase.md).

Para saber como configurar o acesso ao site privado, consulte o acesso privado ao [site do Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integração da rede virtual

A integração virtual da rede permite que a sua aplicação de funções aceda a recursos dentro de uma rede virtual.
A Azure Functions suporta dois tipos de integração virtual da rede:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

A integração virtual da rede em Funções Azure utiliza infraestruturapartilhada com aplicações web do App Service. Para saber mais sobre os dois tipos de integração virtual da rede, consulte:

* [Integração da rede virtual regional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integração da rede virtual exigida por Gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Para aprender a configurar a integração de redes virtuais, consulte [Integrar uma aplicação de função com uma rede virtual Azure.](functions-create-vnet.md)

## <a name="regional-virtual-network-integration"></a>Integração regional da rede virtual

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Ligar a recursos garantidos do ponto final do serviço

Para fornecer um nível de segurança mais elevado, pode restringir uma série de serviços Azure a uma rede virtual utilizando pontos finais de serviço. Em seguida, deve integrar a sua aplicação de funções com essa rede virtual para aceder ao recurso. Esta configuração é suportada em todos os planos que suportam a integração de redes virtuais.

Para saber mais, consulte [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço de rede Virtual .

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restrinja a sua conta de armazenamento a uma rede virtual

Quando criar uma aplicação de função, deve criar ou ligar-se a uma conta de Armazenamento Azure de uso geral que suporta o armazenamento de Blob, Queue e Mesa. Não pode utilizar atualmente quaisquer restrições de rede virtuais nesta conta. Se configurar um ponto final do serviço de rede virtual na conta de armazenamento que está a usar para a sua aplicação de função, essa configuração quebrará a sua aplicação.

Para saber mais, consulte os requisitos da [conta de armazenamento.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="use-key-vault-references"></a>Utilizar referências chave vault

Pode utilizar referências do Cofre de Chaves Azure para utilizar segredos do Cofre de Chaves Azure na aplicação Funções Azure sem exigir alterações de código. Azure Key Vault é um serviço que fornece gestão centralizada de segredos, com total controlo sobre políticas de acesso e histórico de auditoria.

Atualmente, as [referências do Cofre chave](../app-service/app-service-key-vault-references.md) não funcionarão se o seu cofre de chaves estiver seguro com pontos finais de serviço. Para se ligar a um cofre chave utilizando a integração virtual da rede, precisa de ligar para o Key Vault no seu código de aplicação.

## <a name="virtual-network-triggers-non-http"></a>Gatilhos de rede virtuais (não HTTP)

Atualmente, pode utilizar funções de gatilho não HTTP a partir de uma rede virtual de uma de duas formas:

+ Execute a sua aplicação de função num plano Premium e ative o suporte ao gatilho da rede virtual.
+ Execute a sua aplicação de função num plano de serviço de aplicações ou no Ambiente de Serviço de Aplicações.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plano premium com gatilhos de rede virtual

Quando executa um plano Premium, pode ligar funções de gatilho não HTTP a serviços que funcionam dentro de uma rede virtual. Para isso, tem de ativar o suporte de disparo de rede virtual para a sua aplicação de função. A definição de suporte do gatilho da **rede virtual** encontra-se no portal [Azure](https://portal.azure.com) sob as definições de tempo de funcionamento da Função de **Configuração**  >  **Function runtime settings**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Também pode ativar os gatilhos de rede virtuais utilizando o seguinte comando Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Os gatilhos de rede virtuais são suportados na versão 2.x e acima do tempo de funcionamento das Funções. São suportados os seguintes tipos de gatilhos não HTTP.

| Extensão | Versão mínima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou superior |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou acima|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou superior|
|[Microsoft.Azure.webJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou acima|

> [!IMPORTANT]
> Quando ativa o suporte ao gatilho da rede virtual, apenas os tipos de gatilho mostrados na escala de tabela anterior dinamicamente com a sua aplicação. Ainda podeusar gatilhos que não estão na mesa, mas não são escalados para além da contagem de casos pré-aquecido. Para obter a lista completa de gatilhos, consulte [Gatilhos e encadernações](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plano de serviço de aplicações e ambiente de serviço de aplicações com gatilhos de rede virtual

Quando a sua aplicação de funções funciona num plano de serviço de aplicações ou num Ambiente de Serviço de Aplicações, pode utilizar funções de gatilho não HTTP. Para que as suas funções sejam ativadas corretamente, deve estar ligada a uma rede virtual com acesso ao recurso definido na ligação do gatilho.

Por exemplo, assuma que quer configurar o Azure Cosmos DB para aceitar o tráfego apenas a partir de uma rede virtual. Neste caso, deve implementar a sua aplicação de funções num plano de Serviço de Aplicações que forneça integração virtual de rede com essa rede virtual. A integração permite que uma função seja desencadeada por esse recurso Azure Cosmos DB.

## <a name="hybrid-connections"></a>Ligações Híbridas

[Ligações Híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é uma característica do Azure Relay que pode utilizar para aceder aos recursos de aplicação noutras redes. Fornece acesso da sua app a um ponto final de aplicação. Não pode usá-lo para aceder à sua aplicação. As Conexões Híbridas estão disponíveis para funções que funcionam no Windows em todos, menos no plano de consumo.

Conforme utilizado nas Funções Azure, cada ligação híbrida correlaciona-se com um único hospedeiro tCP e uma combinação de porta. Isto significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicação desde que aceda a uma porta de escuta TCP. A funcionalidade Conexões Híbridas não sabe nem se importa com o protocolo de aplicação ou a que acede. Só fornece acesso à rede.

Para saber mais, consulte a documentação do Serviço de [Aplicações para Ligações Híbridas.](../app-service/app-service-hybrid-connections.md) Estes mesmos passos de configuração suportam funções Azure.

>[!IMPORTANT]
> As Ligações Híbridas só são suportadas nos planos do Windows. Linux não é apoiado.

## <a name="outbound-ip-restrictions"></a>Restrições ip de saída

As restrições ip de saída estão disponíveis num plano Premium, plano de serviço de aplicações ou ambiente de serviço de aplicações. Pode configurar restrições de saída para a rede virtual onde o seu Ambiente de Serviço de Aplicações está implantado.

Quando integra uma aplicação de função num plano Premium ou num plano de Serviço de Aplicações com uma rede virtual, a aplicação ainda pode fazer chamadas de saída para a internet por padrão. Ao adicionar a definição de aplicação, força que todo o tráfego de saída seja enviado para a sua rede virtual, onde as regras do grupo de segurança da `WEBSITE_VNET_ROUTE_ALL=1` rede podem ser usadas para restringir o tráfego.

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o networking e as funções azure:

* [Siga o tutorial sobre começar com a integração de rede virtual](./functions-create-vnet.md)
* [Leia as Funções em rede FAQ](./functions-networking-faq.md)
* [Saiba mais sobre integração de rede virtual com Serviço/Funções app](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais em Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais funcionalidades de networking e controlo com ambientes de serviço de aplicação](../app-service/environment/intro.md)
* [Ligue-se aos recursos individuais no local sem alterações de firewall utilizando ligações híbridas](../app-service/app-service-hybrid-connections.md)
