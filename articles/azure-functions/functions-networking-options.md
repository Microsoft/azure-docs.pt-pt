---
title: Opções de networking de funções Azure
description: Uma visão geral de todas as opções de networking disponíveis nas Funções Azure.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 79c27d252136281249c217f51019e53987922334
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370335"
---
# <a name="azure-functions-networking-options"></a>Opções de networking de funções Azure

Este artigo descreve as funcionalidades de networking disponíveis nas opções de hospedagem para funções Azure. Todas as seguintes opções de networking dão-lhe alguma capacidade de aceder a recursos sem usar endereços de saída da Internet ou para restringir o acesso à Internet a uma aplicação de função.

Os modelos de hospedagem têm diferentes níveis de isolamento de rede disponíveis. Escolher o correto irá ajudá-lo a cumprir os requisitos de isolamento da sua rede.

Você pode hospedar aplicativos de função de várias maneiras:

* Há um conjunto de opções de plano que funcionam em uma infraestrutura multi-inquilino, com vários níveis de conectividade de rede virtual e opções de escala:
    * O [plano de Consumo](functions-scale.md#consumption-plan), que escala dinamicamente em resposta à carga e oferece opções mínimas de isolamento da rede.
    * O [plano Premium](functions-scale.md#premium-plan), que também escala dinamicamente, ao mesmo tempo que oferece um isolamento mais abrangente da rede.
    * O plano Azure [App Service,](functions-scale.md#app-service-plan)que funciona a uma escala fixa e oferece um isolamento de rede semelhante ao plano Premium.
* Pode executar funções num Ambiente de Serviço de [Aplicações.](../app-service/environment/intro.md) Este método implementa a sua função na sua rede virtual e oferece controlo e isolamento completos da rede.

## <a name="matrix-of-networking-features"></a>Matriz de funcionalidades de networking

|                |[Plano de consumo](functions-scale.md#consumption-plan)|[Plano premium](functions-scale.md#premium-plan)|[Plano do Serviço de Aplicações](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicações](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrições ip de entrada e acesso ao site privado](#inbound-ip-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração da rede virtual](#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Sim|
|[Gatilhos de rede virtuais (não HTTP)](#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|
|[Ligações híbridas](#hybrid-connections) (apenas janelas)|❌Não|✅Sim|✅Sim|✅Sim|
|[Restrições ip de saída](#outbound-ip-restrictions)|❌Não| ❌Não|❌Não|✅Sim|

## <a name="inbound-ip-restrictions"></a>Restrições ip de entrada

Pode utilizar restrições IP para definir uma lista ordenada por prioridade saqueada de endereços IP que são permitidos ou negados acesso à sua aplicação. A lista pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, existe um "negar tudo" implícito no final da lista. As restrições IP funcionam com todas as opções de hospedagem de funções.

> [!NOTE]
> Com restrições de rede em vigor, só pode utilizar o editor do portal a partir da sua rede virtual, ou quando tiver colocado o endereço IP da máquina que está a usar para aceder ao portal Azure na lista de Destinatários Seguros. No entanto, ainda pode aceder a quaisquer funcionalidades no separador **de funcionalidades** da Plataforma a partir de qualquer máquina.

Para saber mais, consulte [as restrições estáticas](../app-service/app-service-ip-restrictions.md)de acesso ao Azure App Service.

## <a name="private-site-access"></a>O acesso a sites privados

O acesso ao site privado refere-se a tornar a sua aplicação acessível apenas a partir de uma rede privada, como uma rede virtual Azure.

* O acesso ao site privado está disponível nos planos [Premium](./functions-premium-plan.md), [Consumption](functions-scale.md#consumption-plan)e [App Service](functions-scale.md#app-service-plan) quando os pontos finais do serviço estão configurados.
    * Os pontos finais do serviço podem ser configurados numa base por app em **função das funcionalidades** da Plataforma > **Networking** > **Configurar restrições** de acesso > **Regra de adicionar**. As redes virtuais podem agora ser selecionadas como um tipo de regra.
    * Para mais informações, consulte [os pontos finais do serviço](../virtual-network/virtual-network-service-endpoints-overview.md)de rede virtual .
    * Tenha em mente que, com pontos finais de serviço, a sua função ainda tem acesso total à internet, mesmo com a integração virtual da rede configurada.
* O acesso ao site privado também está disponível dentro de um App Service Environment que está configurado com um equilibrista de carga interna (ILB). Para mais informações, consulte [Criar e utilizar um equilibrador de carga interno com um Ambiente de Serviço de Aplicações](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integração da rede virtual

A integração virtual da rede permite que a sua aplicação de funções aceda a recursos dentro de uma rede virtual. Esta funcionalidade está disponível tanto no plano Premium como no plano de Serviço de Aplicações. Se a sua aplicação estiver num Ambiente de Serviço de Aplicações, já se encontra numa rede virtual e não necessita de integração virtual de rede para chegar a recursos na mesma rede virtual.

Pode utilizar a integração de redes virtuais para permitir o acesso de apps a bases de dados e serviços web que estão a funcionar na sua rede virtual. Com a integração virtual da rede, não precisa de expor um ponto final público para aplicações no seu VM. Em vez disso, pode utilizar endereços privados e não-internet.

Existem duas formas de integração virtual da rede:

+ **Integração regional da rede virtual (pré-visualização)** : Permite a integração com redes virtuais na mesma região. Este tipo de integração requer uma subrede numa rede virtual na mesma região. Esta funcionalidade ainda se encontra em pré-visualização, mas é suportada para aplicações de funções que funcionam no Windows, com as ressalvas descritas após a seguinte tabela Problem/Solution.
+ **Gateway exigiu integração virtual da rede**: Permite a integração com redes virtuais em regiões remotas, ou com redes virtuais clássicas. Este tipo de integração requer a implementação de uma porta de entrada de rede virtual no seu VNet. Esta é uma funcionalidade baseada em VPN, que é suportada apenas para aplicações de funções que funcionam no Windows.

Uma aplicação pode usar apenas um tipo da funcionalidade de integração de rede virtual de cada vez. Embora ambos sejam úteis para muitos cenários, a tabela seguinte indica onde cada um deve ser usado:

| Problema  | Solução |
|----------|----------|
| Quer chegar a um endereço RFC 1918 (10.0.0.0.0/8, 172.16.0.0/12, 192.168.0.0.0/16) na mesma região | Integração regional da rede virtual |
| Quer chegar a recursos numa rede virtual clássica ou numa rede virtual noutra região | Gateway exigiu integração virtual da rede |
| Quer chegar aos pontos finais do RFC 1918 através do Azure ExpressRoute | Integração regional da rede virtual |
| Quer chegar a recursos através de pontos finais de serviço | Integração regional da rede virtual |

Nenhuma das funcionalidades permite chegar a endereços não RFC 1918 através do ExpressRoute. Para isso, tem de utilizar um Ambiente de Serviço de Aplicações.

A utilização da integração da rede virtual regional não liga a sua rede virtual a pontos finais no local ou a configurar pontos finais de serviço. É uma configuração separada de rede. A integração regional da rede virtual apenas permite que a sua app faça chamadas através desses tipos de conexão.

Independentemente da versão utilizada, a integração de redes virtuais dá à sua app de funções acesso aos recursos da sua rede virtual, mas não concede acesso ao site privado à sua aplicação de funções a partir da rede virtual. Acesso ao site privado significa tornar a sua aplicação acessível apenas a partir de uma rede privada como uma rede virtual Azure. A integração virtual da rede é apenas para fazer chamadas de saída da sua app para a sua rede virtual.

A funcionalidade de integração de rede virtual:

* Requer um plano de serviço de aplicações Standard, Premium ou PremiumV2
* Suporta TCP e UDP
* Funciona com apps de Serviço de Aplicações e aplicações de função

Há algumas coisas que a integração virtual da rede não suporta, incluindo:

* A montagem de drives
* Integração com o Active Directory
* NetBIOS

A integração virtual da rede em Funções Azure utiliza infraestruturapartilhada com aplicações web do App Service. Para saber mais sobre os dois tipos de integração virtual da rede, consulte:

* [Integração da rede virtual regional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway exigiu integração virtual da rede](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Para saber mais sobre a utilização da integração de redes virtuais, consulte [Integrar uma aplicação de função com uma rede virtual Azure.](functions-create-vnet.md)

## <a name="connecting-to-service-endpoint-secured-resources"></a>Ligação a recursos garantidos de ponto final de serviço

> [!NOTE]
> Por enquanto, pode levar até 12 horas para que novos pontos finais do serviço fiquem disponíveis para a sua aplicação de funções depois de configurar as restrições de acesso no recurso a jusante. Durante este período, o recurso ficará completamente indisponível na sua aplicação.

Para fornecer um nível de segurança mais elevado, pode restringir uma série de serviços Azure a uma rede virtual utilizando pontos finais de serviço. Em seguida, deve integrar a sua aplicação de funções com essa rede virtual para aceder ao recurso. Esta configuração é suportada em todos os planos que suportam a integração de redes virtuais.

[Saiba mais sobre os pontos finais do serviço de rede virtual.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Restringir a sua conta de armazenamento a uma rede virtual

Quando criar uma aplicação de função, deve criar ou ligar-se a uma conta de Armazenamento Azure de uso geral que suporta o armazenamento de Blob, Queue e Mesa. Não pode utilizar atualmente quaisquer restrições de rede virtuais nesta conta. Se configurar um ponto final do serviço de rede virtual na conta de armazenamento que está a usar para a sua aplicação de função, isso irá quebrar a sua aplicação.

[Saiba mais sobre os requisitos da conta de armazenamento.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Usando referências chave vault 

As referências do Cofre chave permitem-lhe utilizar segredos do Cofre de Chaves Azure na aplicação Funções Azure sem exigir alterações de código. Azure Key Vault é um serviço que fornece gestão centralizada de segredos, com total controlo sobre políticas de acesso e histórico de auditoria.

Atualmente, as [referências do Cofre chave](../app-service/app-service-key-vault-references.md) não funcionarão se o seu Cofre chave estiver seguro com pontos finais de serviço. Para ligar a um Cofre chave utilizando a integração virtual da rede, terá de ligar para o cofre chave no seu código de aplicação.

## <a name="virtual-network-triggers-non-http"></a>Gatilhos de rede virtuais (não HTTP)

Atualmente, pode utilizar funções de gatilho não HTTP a partir de uma rede virtual de uma de duas formas: 
+ Execute a sua aplicação de função num plano Premium e ative o suporte ao gatilho da rede Virtual.
+ Execute a sua aplicação de função num plano de serviço de aplicações ou no Ambiente de Serviço de Aplicações.

### <a name="premium-plan-with-virtual-network-triggers"></a>Plano Premium com gatilhos de rede virtual

Ao executar um plano Premium, pode ligar funções de gatilho não HTTP a serviços que funcionam dentro de uma rede virtual. Para isso, tem de ativar o suporte de disparo de rede virtual para a sua aplicação de função. A definição de suporte ao gatilho da **rede virtual** encontra-se no portal [Azure](https://portal.azure.com) sob as definições da **aplicação Function**.

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

Também pode ativar os gatilhos de rede virtuais utilizando o seguinte comando Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Os gatilhos de rede virtuais são suportados na versão 2.x e acima do tempo de funcionamento das Funções. São suportados os seguintes tipos de gatilhos não HTTP.

| Extensão | Versão Mínima |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 ou superior |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 ou superior|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 ou acima|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 ou superior|
|[Microsoft.Azure.webJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 ou acima|

> [!IMPORTANT]
> Ao ativar o suporte ao gatilho da rede virtual, apenas os tipos de gatilho acima da escala são dinamicamente com a sua aplicação. Ainda pode utilizar gatilhos não listados acima, no entanto não são dimensionados para além da sua contagem de instâncias pré-aquecida. Consulte [os gatilhos e encadernações](./functions-triggers-bindings.md#supported-bindings) para obter a lista completa de gatilhos.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>Plano de Serviço de Aplicações e Ambiente de Serviço de Aplicações com gatilhos de rede virtual

Quando a sua aplicação de funções funciona num Plano de Serviço de Aplicações ou num Ambiente de Serviço de Aplicações, pode utilizar funções de gatilho não HTTP. Para que as suas funções sejam ativadas corretamente, deve estar ligada a uma rede virtual com acesso ao recurso definido na ligação do gatilho. 

Por exemplo, assuma que quer configurar o Azure Cosmos DB para aceitar o tráfego apenas a partir de uma rede virtual. Neste caso, deve implementar a sua aplicação de funções num plano de Serviço de Aplicações que forneça integração virtual de rede com essa rede virtual. Isto permite que uma função seja desencadeada por aquele recurso Azure Cosmos DB. 

## <a name="hybrid-connections"></a>Ligações Híbridas

[Ligações Híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é uma característica do Azure Relay que pode utilizar para aceder aos recursos de aplicação noutras redes. Fornece acesso da sua app a um ponto final de aplicação. Não pode usá-lo para aceder à sua aplicação. As Ligações Híbridas estão disponíveis para funções em execução no Windows em todos, menos no plano de consumo.

Conforme utilizado nas Funções Azure, cada ligação híbrida correlaciona-se com um único hospedeiro tCP e uma combinação de porta. Isto significa que o ponto final da ligação híbrida pode estar em qualquer sistema operativo e qualquer aplicação desde que aceda a uma porta de escuta TCP. A funcionalidade Conexões Híbridas não sabe nem se importa com o protocolo de aplicação ou a que acede. Só fornece acesso à rede.

Para saber mais, consulte a documentação do Serviço de [Aplicações para Ligações Híbridas.](../app-service/app-service-hybrid-connections.md) Estes mesmos passos de configuração suportam funções Azure.

>[!IMPORTANT]
> As Ligações Híbridas só são suportadas nos planos do Windows. Linux não é apoiado

## <a name="outbound-ip-restrictions"></a>Restrições ip de saída

As restrições IP de saída estão disponíveis apenas para funções implementadas num Ambiente de Serviço de Aplicações. Pode configurar restrições de saída para a rede virtual onde o seu Ambiente de Serviço de Aplicações está implantado.

Quando integra uma aplicação de função num plano Premium ou num plano de Serviço de Aplicações com uma rede virtual, a aplicação ainda pode fazer chamadas de saída para a internet.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o networking e as funções azure:

* [Siga o tutorial sobre começar com a integração de rede virtual](./functions-create-vnet.md)
* [Leia as Funções em rede FAQ](./functions-networking-faq.md)
* [Saiba mais sobre integração de rede virtual com Serviço/Funções app](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais em Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais funcionalidades de networking e controlo com ambientes de serviço de aplicação](../app-service/environment/intro.md)
* [Ligue-se aos recursos individuais no local sem alterações de firewall utilizando ligações híbridas](../app-service/app-service-hybrid-connections.md)
