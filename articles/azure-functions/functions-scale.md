---
title: Dimensionamento e alojamento de Funções do Azure
description: Saiba como escolher entre o plano de consumo de funções Azure e o plano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ac0417e9d8adca168dd68e1721a1c9c890de1c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656945"
---
# <a name="azure-functions-scale-and-hosting"></a>Dimensionamento e alojamento de Funções do Azure

Quando cria uma aplicação de função no Azure, tem de escolher um plano de hospedagem para a sua aplicação. Existem três planos de hospedagem disponíveis para funções Azure: [plano de consumo,](#consumption-plan) [plano Premium](#premium-plan)e plano [dedicado (App Service).](#app-service-plan)

O plano de hospedagem que escolher dita os seguintes comportamentos:

* Como a sua aplicação de função é dimensionada.
* Os recursos disponíveis para cada instância de aplicação de funções.
* Suporte para funcionalidades avançadas, como conectividade Azure Virtual Network.

Tanto os planos de Consumo como de Premium adicionam automaticamente a potência do cálculo quando o seu código está em funcionamento. A sua aplicação é dimensionada quando necessária para lidar com a carga e dimensionada quando o código para de funcionar. Para o plano de consumo, também não tem de pagar por VMs ociosos ou capacidade de reserva com antecedência.  

O plano premium fornece funcionalidades adicionais, como instâncias de computação premium, a capacidade de manter as instâncias quentes indefinidamente, e a conectividade VNet.

O plano de Serviço de Aplicações permite-lhe tirar partido de infraestruturas dedicadas, que gere. A sua aplicação de funções não escala com base em eventos, o que significa que nunca é escala para zero. (Requer que [sempre ligado](#always-on) esteja ativado.)

## <a name="hosting-plan-support"></a>Apoio ao plano de hospedagem

O suporte de recurso insere-se nas duas categorias seguintes:

* _Geralmente disponível (GA)_: totalmente apoiado e aprovado para utilização da produção.
* _Pré-visualização_: ainda não totalmente suportado ou aprovado para utilização da produção.

A tabela seguinte indica o nível atual de suporte para os três planos de hospedagem, quando está em execução no Windows ou linux:

| | Plano de consumo | Plano Premium | Plano dedicado |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Plano de consumo

Quando se está a usar o plano de consumo, os casos do hospedeiro das Funções Azure são dinamicamente adicionados e removidos com base no número de eventos que chegam. Este plano sem servidor dimensiona automaticamente, sendo-lhe cobrados os recursos de computação apenas quando as suas funções estão em execução. Num plano de consumo, a execução de uma função excede o tempo limite após um período de tempo configurável.

A faturação baseia-se no número de execuções, no tempo de execução e na memória utilizada. A faturação é agregada entre todas as funções, dentro de uma aplicação de funções. Para mais informações, consulte a página de preços das [Funções Azure](https://azure.microsoft.com/pricing/details/functions/).

O plano de Consumo é o plano de hospedagem padrão e oferece os seguintes benefícios:

* Pague apenas quando as suas funções estiverem em funcionamento
* Esternação automaticamente, mesmo durante períodos de alta carga

As aplicações de função na mesma região podem ser atribuídas ao mesmo plano de consumo. Não há nenhuma desvantagem ou impacto em ter várias aplicações a funcionar no mesmo plano de consumo. Atribuir várias aplicações ao mesmo plano de consumo não tem impacto na resiliência, escalabilidade ou fiabilidade de cada aplicação.

Para saber mais sobre como estimar os custos ao executar um plano de consumo, consulte [os custos do plano de consumo de compreensão.](functions-consumption-costs.md)

## <a name="premium-plan"></a><a name="premium-plan"></a>Plano Premium

Quando se está a usar o plano Premium, as instâncias do anfitrião das Funções Azure são adicionadas e removidas com base no número de eventos que chegam, tal como o plano de consumo.  O plano Premium suporta as seguintes funcionalidades:

* Casos perpetuamente quentes para evitar qualquer arranque frio
* Conectividade VNet
* Duração ilimitada de execução (60 minutos garantidos)
* Tamanhos de instância premium (um núcleo, dois núcleos e quatro instâncias centrais)
* Preços mais previsíveis
* Alocação de aplicativos de alta densidade para planos com aplicações de múltiplas funções

Informações sobre como configurar estas opções podem ser encontradas no documento do [plano Azure Functions Premium](functions-premium-plan.md).

Em vez de faturar por execução e memória consumida, a faturação do plano Premium baseia-se no número de segundos fundamentais e memória usados em casos necessários e pré-aquecidos. Pelo menos uma instância deve estar sempre quente por plano. Isto significa que existe um custo mensal mínimo por plano ativo, independentemente do número de execuções. Tenha em mente que todas as aplicações de função num plano Premium partilham instâncias pré-aquecidas e ativas.

Considere o plano Azure Functions Premium nas seguintes situações:

* As suas aplicações de função funcionam continuamente, ou quase continuamente.
* Você tem um grande número de pequenas execuções e tem uma alta conta de execução, mas baixa nota gb segundo no plano de consumo.
* Precisa de mais CPU ou opções de memória do que as fornecidas pelo plano de consumo.
* O seu código tem de ser mais longo do que o tempo máximo de [execução permitido](#timeout) no plano de consumo.
* Você precisa de funcionalidades que só estão disponíveis num plano Premium, como conectividade de rede virtual.

Ao executar funções JavaScript num plano Premium, deve escolher uma instância que tenha menos vCPUs. Para mais informações, consulte os [planos Premium de base única .](functions-reference-node.md#considerations-for-javascript-functions)  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Plano dedicado (Serviço de Aplicações)

As suas aplicações de funções também podem ser executadas nos mesmos VMs dedicados que outras aplicações do App Service (Basic, Standard, Premium e SKUs isolados).

Considere um plano de Serviço de Aplicações nas seguintes situações:

* Tem VMs existentes e subutilizados que já estão a executar outras instâncias do Serviço de Aplicações.
* Pretende fornecer uma imagem personalizada para executar as suas funções.

Paga o mesmo pelas aplicações de função num Plano de Serviço de Aplicações como faria com outros recursos do App Service, como aplicações web. Para mais detalhes sobre como funciona o plano de serviço de aplicações, consulte os planos do Serviço de [Aplicações Azure em profundidade.](../app-service/overview-hosting-plans.md)

Com um plano de Serviço de Aplicações, pode escalar manualmente adicionando mais instâncias vM. Também pode ativar a escala automática. Para mais informações, consulte A contagem manual ou automática da [contagem da escala](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Também pode escalar escolhendo um plano de Serviço de Aplicações diferente. Para mais informações, consulte [Scale up uma app em Azure](../app-service/manage-scale-up.md). 

Ao executar funções JavaScript num plano de Serviço de Aplicações, deve escolher um plano que tenha menos vCPUs. Para mais informações, consulte [Escolha planos de serviço de aplicações de base única](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Sempre ligado

Se executar um plano de Serviço de Aplicações, deve ativar o **Always na** definição de modo a que a sua aplicação de funções funcione corretamente. Num plano de Serviço de Aplicações, o tempo de funcionamento das funções fica inativo após alguns minutos de inatividade, pelo que apenas os gatilhos HTTP irão "acordar" as suas funções. Sempre ligado está disponível apenas num plano de Serviço de Aplicações. Num plano de consumo, a plataforma ativa automaticamente as aplicações de função.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com o Always On ativado, o tempo de `functionTimeout` execução para funções individuais é controlado pela definição no ficheiro do projeto [host.json.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinar o plano de hospedagem de uma aplicação existente

Para determinar o plano de hospedagem utilizado pela sua aplicação de funções, consulte o **plano do Serviço de Aplicações** no separador **Overview** para a aplicação de funções no [portal Azure](https://portal.azure.com). Para ver o nível de preços, selecione o nome do Plano de Serviço de **Aplicações**e, em seguida, selecione **Propriedades** do painel esquerdo.

![Ver plano de escala no portal](./media/functions-scale/function-app-overview-portal.png)

Também pode utilizar o Azure CLI para determinar o plano, da seguinte forma:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída deste `dynamic`comando for, a sua aplicação de funções está no plano de consumo. Quando a saída deste `ElasticPremium`comando for, a sua aplicação de funções está no plano Premium. Todos os outros valores indicam diferentes níveis de um plano de Serviço de Aplicações.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em qualquer plano, uma aplicação de função requer uma conta de Armazenamento Azure geral, que suporta O Blob, Fila, Ficheiros e Armazenamento de Mesa. Isto porque a Azure Functions conta com o Armazenamento Azure para operações como a gestão de gatilhos e execuções de funções de exploração madeireira, mas algumas contas de armazenamento não suportam filas e tabelas. Estas contas, que incluem contas de armazenamento apenas blob (incluindo armazenamento premium) e contas de armazenamento de uso geral com replicação de armazenamento redundante, são filtradas das seleções da Conta de **Armazenamento** existentes quando cria uma aplicação de função.

A mesma conta de armazenamento utilizada pela sua aplicação de funções também pode ser utilizada pelos seus gatilhos e encadernações para armazenar os dados da sua aplicação. No entanto, para operações intensivas de armazenamento, deve utilizar uma conta de armazenamento separada.  

É certamente possível que várias aplicações de função partilhem a mesma conta de armazenamento sem qualquer problema. (Um bom exemplo disso é quando desenvolve várias aplicações no seu ambiente local usando o Emulador de Armazenamento Azure, que funciona como uma conta de armazenamento.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, consulte a introdução dos serviços de [armazenamento Azure.](../storage/common/storage-introduction.md#azure-storage-services)

## <a name="how-the-consumption-and-premium-plans-work"></a>Como funcionam os planos de Consumo e Premium

Nos planos de Consumo e Premium, a infraestrutura de Funções Azure escala cpU e recursos de memória adicionais das funções hospedadas, com base no número de eventos em que as suas funções são desencadeadas. Cada instância das funções hospedadas no plano de consumo está limitada a 1,5 GB de memória e um CPU.  Uma instância do anfitrião é toda a aplicação de função, o que significa que todas as funções dentro de uma aplicação de função partilham recursos dentro de uma instância e escala ao mesmo tempo. As aplicações de função que partilham o mesmo plano de consumo são dimensionadas de forma independente.  No plano Premium, o tamanho do seu plano determinará a memória disponível e cpU para todas as aplicações nesse plano nesse caso.  

Os ficheiros de código de função são armazenados nas ações do Azure Files na conta de armazenamento principal da função. Quando elimina a conta de armazenamento principal da aplicação de função, os ficheiros do código de função são eliminados e não podem ser recuperados.

### <a name="runtime-scaling"></a>Escalade tempo de execução

As Funções Azure utilizam um componente chamado controlador de *escala* para monitorizar a taxa de eventos e determinar se devem ou não escalar. O controlador de escala utiliza heurística para cada tipo de gatilho. Por exemplo, quando se está a usar um gatilho de armazenamento de fila Azure, ele escala com base no comprimento da fila e na idade da mensagem de fila mais antiga.

A unidade de escala para funções Azure é a aplicação de função. Quando a aplicação de funções é dimensionada, são atribuídos recursos adicionais para executar várias instâncias do hospedeiro das Funções Azure. Inversamente, à medida que a procura de cálculo é reduzida, o controlador de escala remove as instâncias de acolhimento de funções. O número de casos acaba por ser *escalado* para zero quando não estão a funcionar funções dentro de uma aplicação de função.

![Eventos de monitorização do controlador de escala e criação de instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Compreender comportamentos de escala

O escalonamento pode variar em vários fatores, e escalar de forma diferente com base no gatilho e na linguagem selecionadas. Há algumas complexidades de comportamentos de escalação para estar atento:

* Uma única aplicação de função apenas se eleva para um máximo de 200 instâncias. Uma única instância pode processar mais do que uma mensagem ou pedido de cada vez, por isso não há um limite definido para o número de execuções simultâneas.
* Para os gatilhos HTTP, novos casos são atribuídos, no máximo, uma vez por segundo.
* Para os gatilhos não HTTP, são atribuídos novos casos, no máximo, uma vez a cada 30 segundos. A escala é mais rápida quando se corre num [plano Premium.](#premium-plan)
* Para os gatilhos de ônibus de serviço, utilize _a Gestão_ de direitos sobre recursos para a escala mais eficiente. Com os direitos de _escuta,_ a escala não é tão precisa porque o comprimento da fila não pode ser usado para informar decisões de escala. Para saber mais sobre a definição de direitos nas políticas de acesso ao ônibus de serviço, consulte a Política de Autorização de [Acesso Partilhado](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Para os gatilhos do Event Hub, consulte a [orientação](functions-bindings-event-hubs-trigger.md#scaling) de escala no artigo de referência. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Boas práticas e padrões para aplicações escaláveis

Existem muitos aspetos de uma aplicação de função que impactará o quão bem irá escalar, incluindo a configuração do hospedeiro, a pegada do tempo de execução e a eficiência dos recursos.  Para mais informações, consulte a [secção de escalabilidade do artigo considerações de desempenho](functions-best-practices.md#scalability-best-practices). Também deve estar ciente de como as ligações se comportam à medida que a sua função funciona. Para mais informações, consulte [Como gerir as ligações em Funções Azure](manage-connections.md).

Para obter mais informações sobre escalaem em Python e Node.js, consulte o guia de desenvolvimento do [Azure Functions Python - Escala e conmoeda](functions-reference-python.md#scaling-and-concurrency) e guia de desenvolvimento de [funções azure Node.js - Escala e conmoeda](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Modelo de faturação

A faturação dos diferentes planos é descrita em detalhe na página de preços das [Funções Azure](https://azure.microsoft.com/pricing/details/functions/). O uso é agregado ao nível da aplicação de função e conta apenas o tempo em que o código de função é executado. Seguem-se unidades de faturação:

* **Consumo de recursos em gigabytes-segundos (GB-s)**. Computada como uma combinação do tamanho da memória e tempo de execução para todas as funções dentro de uma aplicação de função. 
* **Execuções.** Contada cada vez que uma função é executada em resposta a um gatilho de evento.

Consultas úteis e informações sobre como entender a sua conta de consumo podem ser encontradas [no FAQ de faturação](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites do serviço

O quadro seguinte indica os limites aplicáveis às aplicações de função quando estão a ser realizados nos vários planos de hospedagem:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
