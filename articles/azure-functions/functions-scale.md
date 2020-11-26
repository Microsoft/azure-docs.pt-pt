---
title: Dimensionamento e alojamento de Funções do Azure
description: Saiba como escolher entre o plano de consumo de funções Azure e o plano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f41354630f4885a30bd5c036495b216a2cc05599
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96167799"
---
# <a name="azure-functions-scale-and-hosting"></a>Dimensionamento e alojamento de Funções do Azure

Quando criar uma aplicação de função em Azure, tem de escolher um plano de hospedagem para a sua aplicação. Existem três planos básicos de hospedagem disponíveis para as Funções Azure: [Plano de Consumo,](#consumption-plan) [Plano Premium](#premium-plan)e Plano [Dedicado (Serviço de Aplicações).](#app-service-plan) Todos os planos de hospedagem estão geralmente disponíveis (GA) em máquinas virtuais Linux e Windows.

O plano de hospedagem que escolhe dita os seguintes comportamentos:

* Como a sua aplicação de função é dimensionada.
* Os recursos disponíveis para cada instância de aplicação de função.
* Suporte para funcionalidades avançadas, como a conectividade da Rede Virtual Azure.

Tanto os planos Consumption como Premium adicionam automaticamente energia de computação quando o seu código está em funcionamento. A sua aplicação é dimensionada quando necessário para manusear a carga, e escalada quando o código deixa de funcionar. Para o plano de Consumo, também não tem de pagar vMs inativos ou capacidade de reserva com antecedência.  

O plano premium fornece funcionalidades adicionais, tais como instâncias de computação premium, a capacidade de manter as instâncias quentes indefinidamente, e a conectividade VNet.

O plano de Serviço de Aplicações permite-lhe tirar partido de infraestruturas dedicadas, que gere. A sua aplicação de função não escala com base em eventos, o que significa que nunca escala para zero. (Requer que [sempre ligado](#always-on) esteja ativado.)

Para uma comparação detalhada entre os vários planos de hospedagem (incluindo hospedagem baseada em Kubernetes), consulte a secção de [comparação dos planos de hospedagem](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Plano de consumo

Quando se utiliza o plano de consumo, os casos do anfitrião das Funções Azure são adicionados e removidos de forma dinâmica com base no número de eventos que chegam. Este plano sem servidor dimensiona automaticamente, sendo-lhe cobrados os recursos de computação apenas quando as suas funções estão em execução. Num plano de consumo, a execução de uma função excede o tempo limite após um período de tempo configurável.

A faturação baseia-se no número de execuções, no tempo de execução e na memória utilizada. A utilização é agregada em todas as funções dentro de uma aplicação de função. Para obter mais informações, consulte a página de preços do [Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

O plano de consumo é o plano de hospedagem predefinido e oferece os seguintes benefícios:

* Pague apenas quando as suas funções estiverem em execução
* Escale automaticamente, mesmo durante períodos de alta carga

As aplicações de função na mesma região podem ser atribuídas ao mesmo plano de Consumo. Não há nenhuma desvantagem ou impacto em ter várias aplicações a funcionar no mesmo plano de Consumo. Atribuir várias aplicações ao mesmo plano de Consumo não tem impacto na resiliência, escalabilidade ou fiabilidade de cada app.

Para saber mais sobre como estimar os custos ao executar um plano de consumo, consulte [os custos do plano de consumo de compreensão.](functions-consumption-costs.md)

## <a name="premium-plan"></a><a name="premium-plan"></a>Plano Premium

Quando você está usando o plano Premium, os casos do anfitrião Azure Functions são adicionados e removidos com base no número de eventos que chegam como o plano de Consumo.  O plano premium suporta as seguintes características:

* Casos perpetuamente quentes para evitar qualquer arranque a frio
* Conectividade VNet
* Duração de execução ilimitada (60 minutos garantidos)
* Tamanhos de exemplo premium (um núcleo, dois núcleos e quatro instâncias fundamentais)
* Preços mais previsíveis
* Alocação de aplicativos de alta densidade para planos com aplicações de múltiplas funções

Para saber como pode criar uma aplicação de função num plano Premium, consulte [o plano Azure Functions Premium.](functions-premium-plan.md)

Em vez de faturar por execução e memória consumida, a faturação do plano Premium baseia-se no número de segundos e memórias essenciais atribuídos em todos os casos.  Não há nenhuma acusação de execução com o plano Premium. Pelo menos uma instância deve ser atribuída a todo o momento por plano. Isto resulta num custo mínimo mensal por plano ativo, independentemente de a função estar ativa ou inativa. Tenha em mente que todas as aplicações de função de um plano Premium partilham instâncias atribuídas.

Considere o plano Azure Functions Premium nas seguintes situações:

* As aplicações de função funcionam continuamente, ou quase continuamente.
* Tem um elevado número de pequenas execuções e tem uma nota de execução alta, mas baixa nota de segunda nota de GB no plano de consumo.
* Precisa de mais OPÇÕES de CPU ou memória do que as fornecidas pelo plano de Consumo.
* O seu código tem de ser executado mais tempo do que o [tempo máximo de execução permitido](#timeout) no plano de Consumo.
* Você precisa de funcionalidades que só estão disponíveis num plano Premium, como a conectividade de rede virtual. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Plano dedicado (Serviço de Aplicações)

As suas aplicações de função também podem funcionar nos mesmos VMs dedicados que outras aplicações do Serviço de Aplicações (SKUs básicos, standard, premium e isolados).

Considere um plano de Serviço de Aplicações nas seguintes situações:

* Tem VMs existentes e subutilizados que já estão a executar outras instâncias do Serviço de Aplicações.
* Pretende fornecer uma imagem personalizada para executar as suas funções.

Você paga o mesmo por aplicações de função em um Plano de Serviço de Aplicações como você faria para outros recursos do Serviço de Aplicações, como aplicações web. Para mais detalhes sobre o funcionamento do plano do Serviço de Aplicações, consulte os planos do [Azure App Service em visão geral aprofundada.](../app-service/overview-hosting-plans.md)

Utilizando um plano de Serviço de Aplicações, pode escalar manualmente adicionando mais instâncias VM. Também pode ativar a autoescala, embora a autoescala seja mais lenta do que a escala elástica do plano Premium. Para obter mais informações, consulte [a contagem de instâncias de escala manual ou automática.](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) Também pode aumentar a escala escolhendo um plano de Serviço de Aplicações diferente. Para obter mais informações, consulte [Scale up uma aplicação em Azure](../app-service/manage-scale-up.md). 

Ao executar funções JavaScript num plano de Serviço de Aplicações, deve escolher um plano que tenha menos vCPUs. Para obter mais informações, consulte [Escolha os planos do Serviço de Aplicações de núcleo único.](functions-reference-node.md#choose-single-vcpu-app-service-plans) 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

Executar num Ambiente de Serviço de [Aplicações](../app-service/environment/intro.md) (ASE) permite-lhe isolar totalmente as suas funções e tirar partido de um maior número de casos do que um Plano de Serviço de Aplicações.

### <a name="always-on"></a><a name="always-on"></a> Sempre ligado

Se executar um plano de Serviço de Aplicações, deve ativar o **Always on** Setting para que a sua aplicação de função funcione corretamente. Num plano de Serviço de Aplicações, o tempo de funcionamento das funções fica inativo após alguns minutos de inatividade, pelo que apenas os gatilhos HTTP "acordam" as suas funções. Sempre disponível apenas num plano de Serviço de Aplicações. Num plano de Consumo, a plataforma ativa as aplicações de função automaticamente.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com Always On ativado, o tempo de execução para funções individuais é controlado pela `functionTimeout` definição no [host.jsno](functions-host-json.md#functiontimeout) ficheiro do projeto.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinar o plano de hospedagem de uma aplicação existente

Para determinar o plano de hospedagem utilizado pela sua aplicação de função, consulte o **plano de Serviço de Aplicações** no **separador Visão Geral** para a aplicação de função no portal [Azure.](https://portal.azure.com) Para ver o nível de preços, selecione o nome do Plano de Serviço de **Aplicações** e, em seguida, selecione **Propriedades** a partir do painel esquerdo.

![Ver plano de escala no portal](./media/functions-scale/function-app-overview-portal.png)

Também pode utilizar o CLI Azure para determinar o plano, da seguinte forma:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída deste comando `dynamic` é, a sua aplicação de função está no plano de Consumo. Quando a saída deste comando `ElasticPremium` é, a sua aplicação de função está no plano Premium. Todos os outros valores indicam diferentes níveis de um plano de Serviço de Aplicações.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em qualquer plano, uma aplicação de função requer uma conta geral de Armazenamento Azure, que suporta Azure Blob, Queue, Files e armazenamento de mesa. Isto porque o Azure Functions depende do Azure Storage para operações como gerir gatilhos e executar funções de registo, mas algumas contas de armazenamento não suportam filas e tabelas. Estas contas, que incluem contas de armazenamento apenas blob (incluindo armazenamento premium) e contas de armazenamento para fins gerais com replicação de armazenamento redundante de zona, são filtradas das seleções de Conta de **Armazenamento** existentes quando cria uma aplicação de função.

A mesma conta de armazenamento utilizada pela sua aplicação de função também pode ser usada pelos seus gatilhos e encadernações para armazenar os dados da sua aplicação. No entanto, para operações intensivas de armazenamento, deve utilizar uma conta de armazenamento separada.  

É possível que várias aplicações de funções partilhem a mesma conta de armazenamento sem qualquer problema. (Um bom exemplo disso é quando desenvolve várias aplicações no seu ambiente local usando o Emulador de Armazenamento Azure, que funciona como uma conta de armazenamento.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre tipos de conta de armazenamento, consulte [a introdução dos serviços de armazenamento Azure.](../storage/common/storage-introduction.md#core-storage-services)

### <a name="in-region-data-residency"></a>Na Residência de Dados da Região

Quando necessário para que todos os dados do cliente permaneçam numa única região, a conta de armazenamento associada à aplicação de função deve ser uma com [a redundância da região.](../storage/common/storage-redundancy.md)  Uma conta de armazenamento redundante na região também teria de ser utilizada com [funções duradouras de Azure](./durable/durable-functions-perf-and-scale.md#storage-account-selection) para funções duradouras.

Outros dados de clientes geridos pela plataforma só serão armazenados na região quando hospedados num Ambiente de Serviço de Aplicações do Balanceador de Carga Interna (ou ILB ASE).  Os detalhes podem ser encontrados na [redundância da zona ASE.](../app-service/environment/zone-redundancy.md#in-region-data-residency)

## <a name="how-the-consumption-and-premium-plans-work"></a>Como funcionam os planos de Consumo e Premium

Nos planos Consumo e Premium, a infraestrutura Azure Functions escala o CPU e os recursos de memória adicionando instâncias adicionais do anfitrião funções, com base no número de eventos em que as suas funções são desencadeadas. Cada instância do hospedeiro de Funções no plano de Consumo está limitada a 1,5 GB de memória e a um CPU.  Um caso do anfitrião é toda a aplicação de função, o que significa que todas as funções dentro de uma aplicação de função partilham recurso dentro de um caso e escala ao mesmo tempo. As aplicações de função que partilham o mesmo plano de Consumo são dimensionadas de forma independente.  No plano Premium, o tamanho do seu plano determinará a memória disponível e CPU para todas as aplicações nesse plano nesse caso.  

Os ficheiros de código de função são armazenados nas ações do Azure Files na conta principal de armazenamento da função. Quando elimina a conta de armazenamento principal da aplicação de função, os ficheiros de código de função são eliminados e não podem ser recuperados.

### <a name="runtime-scaling"></a>Escalagem de tempo de execução

A Azure Functions utiliza um componente chamado controlador de *escala* para monitorizar a taxa de eventos e determinar se deve escalar ou escalar. O controlador de escala utiliza heurística para cada tipo de gatilho. Por exemplo, quando se está a usar um gatilho de armazenamento da Fila Azure, ele escala com base no comprimento da fila e na idade da mensagem de fila mais antiga.

A unidade de escala para Funções Azure é a aplicação de função. Quando a aplicação de função é dimensionada, são atribuídos recursos adicionais para executar várias instâncias do anfitrião Azure Functions. Inversamente, à medida que a procura de cálculo é reduzida, o controlador de escala remove os casos de hospedeiro da função. O número de casos é eventualmente *dimensionado* para zero quando não há funções a funcionar dentro de uma aplicação de função.

![Eventos de monitorização do controlador de escala e criação de instâncias](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Início a Frio

Depois de a sua aplicação de função ter estado inativa durante alguns minutos, a plataforma pode escalar o número de casos em que a sua aplicação se reduz a zero. O próximo pedido tem a latência adicional de escala de zero para um. Esta latência é referida como um _começo a frio._ O número de dependências que devem ser carregadas pela sua aplicação de função pode afetar o tempo de arranque a frio. O arranque a frio é mais um problema para operações sincronizadas, como os gatilhos HTTP que devem devolver uma resposta. Se os arranques frios estiverem a afetar as suas funções, considere executar num plano Premium ou num plano dedicado com Always on ativado.   

### <a name="understanding-scaling-behaviors"></a>Compreender comportamentos de escala

A escala pode variar em vários fatores, e escalar de forma diferente com base no gatilho e na linguagem selecionada. Há algumas complexidades de comportamentos de escala para estar ciente de:

* Uma única aplicação de função apenas escala para um máximo de 200 instâncias. Uma única instância pode processar mais do que uma mensagem ou pedido de cada vez, por isso não há um limite definido para o número de execuções simultâneas.  Pode [especificar uma](#limit-scale-out) escala máxima mais baixa para o acelerador, conforme necessário.
* Para os gatilhos HTTP, são atribuídas novas instâncias, no máximo, uma vez por segundo.
* Para os gatilhos não-HTTP, são atribuídas novas instâncias, no máximo, uma vez a cada 30 segundos. A escala é mais rápida quando funciona num [plano Premium.](#premium-plan)
* Para os gatilhos de Service Bus, utilize _direitos de gestão_ de recursos para a escala mais eficiente. Com os direitos _de escuta,_ o escalonamento não é tão preciso porque o comprimento da fila não pode ser usado para informar decisões de escala. Para saber mais sobre a definição de direitos nas políticas de acesso a autocarros de serviço, consulte [a Política de Autorização de Acesso Partilhado.](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)
* Para os gatilhos do Event Hub, consulte a [orientação de escala](functions-bindings-event-hubs-trigger.md#scaling) no artigo de referência. 

### <a name="limit-scale-out"></a>Limite escala para fora

Pode desejar restringir o número de casos a que uma aplicação se estende.  Isto é mais comum em casos em que um componente a jusante como uma base de dados tem uma produção limitada.  Por padrão, as funções do plano de consumo irão aumentar para até 200 instâncias, e as funções de plano premium irão aumentar para até 100 instâncias.  Pode especificar um máximo inferior para uma aplicação específica modificando o `functionAppScaleLimit` valor.  O `functionAppScaleLimit` pode ser definido para 0 ou nulo para sem restrições, ou um valor válido entre 1 e o máximo da aplicação.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para apps escaláveis

Existem muitos aspetos de uma aplicação de função que impactará a sua escala, incluindo a configuração do anfitrião, a pegada de tempo de execução e a eficiência dos recursos.  Para mais informações, consulte a [secção de escalabilidade do artigo de considerações de desempenho](functions-best-practices.md#scalability-best-practices). Também deve estar ciente de como as ligações se comportam à medida que as escalas de aplicações da sua função. Para obter mais informações, consulte [Como gerir as ligações em Funções Azure](manage-connections.md).

Para obter mais informações sobre a escala em Python e Node.js, consulte [o guia de desenvolvimento python do Azure Functions Python - Escala e concurrency](functions-reference-python.md#scaling-and-performance) e [Azure Functions Node.js developer guide - Escala e conúnência](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Modelo de faturação

A faturação dos diferentes planos é descrita em detalhe na página de preços do [Azure Functions](https://azure.microsoft.com/pricing/details/functions/). A utilização é agregada ao nível da aplicação de função e conta apenas o tempo em que o código de função é executado. Seguem-se as unidades de faturação:

* **Consumo de recursos em gigabytes-segundos (GB-s)**. Computado como uma combinação de tamanho de memória e tempo de execução para todas as funções dentro de uma aplicação de função. 
* **Execuções.** Contado cada vez que uma função é executada em resposta a um gatilho do evento.

Consultas úteis e informações sobre como entender a sua conta de consumo podem ser encontradas [na faturação faQ.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Comparação de planos de alojamento

A tabela de comparação a seguir mostra todos os aspetos importantes para ajudar a decisão da app de hospedagem Azure Functions App:

### <a name="plan-summary"></a>Resumo do plano
| | |
| --- | --- |  
|**[Plano de consumo](#consumption-plan)**| Dimensione automaticamente e só pague por recursos de cálculo quando as suas funções estiverem em funcionamento. No plano de Consumo, os casos do anfitrião funções são adicionados e removidos dinamicamente com base no número de eventos que chegam.<br/> ✔ plano de hospedagem padrão.<br/>✔ Pague apenas quando as suas funções estiverem em funcionamento.<br/>✔ de escalonamento automaticamente, mesmo durante períodos de alta carga.|  
|**[Plano Premium](#premium-plan)**|Enquanto escala automaticamente com base na procura, use trabalhadores pré-aquecidos para executar aplicações sem atrasos após estar inativo, executar em casos mais poderosos e ligar-se a VNETs. Considere o plano Azure Functions Premium nas seguintes situações, além de todas as funcionalidades do plano de Serviço de Aplicações: <br/>✔ As aplicações de função funcionam continuamente, ou quase continuamente.<br/>✔ Tem um elevado número de pequenas execuções e tem uma nota de execução alta, mas baixa nota de GB segundo no plano de consumo.<br/>✔ Precisa de mais OPÇÕES de CPU ou de memória do que as fornecidas pelo plano de Consumo.<br/>✔ O seu código tem de ser executado mais tempo do que o tempo máximo de execução permitido no plano de Consumo.<br/>✔ Necessita de funcionalidades que só estão disponíveis num plano Premium, como a conectividade da rede virtual.|  
|**[Plano dedicado](#app-service-plan)**<sup>1</sup>|Executar as suas funções dentro de um plano de Serviço de Aplicações com taxas regulares de plano de serviço de aplicações. Bom ajuste para operações de longo prazo, bem como quando são necessários mais escalões preditivos e custos. Considere um plano de Serviço de Aplicações nas seguintes situações:<br/>✔ Tem VMs existentes e subutilizados que já estão a executar outras instâncias do Serviço de Aplicações.<br/>✔ Pretende fornecer uma imagem personalizada para executar as suas funções.|  
|**[ASE](#app-service-plan)**<sup>1</sup>|App Service Environment (ASE) é uma funcionalidade de Serviço de Aplicações que fornece um ambiente totalmente isolado e dedicado para executar aplicações de Serviço de Aplicações de forma segura em alta escala. AsE são adequadas para cargas de trabalho de aplicação que requerem: <br/>✔ muito alta escala.<br/>✔ isolamento total do computação e acesso seguro à rede.<br/>✔ alta utilização da memória.|  
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)** | Kubernetes fornece um ambiente totalmente isolado e dedicado que corre em cima da plataforma Kubernetes.  Kubernetes é apropriado para cargas de trabalho de aplicação que requerem: <br/>✔ requisitos de hardware personalizados.<br/>✔ isolamento e acesso seguro à rede.<br/>✔ Capacidade de funcionar em ambiente híbrido ou multi-nuvem.<br/>✔ Run ao lado das aplicações e serviços existentes da Kubernetes.|  

<sup>1</sup> Para limites específicos para as várias opções do plano de Serviço de Aplicações, consulte os limites do [plano do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

### <a name="operating-systemruntime"></a>Sistema operativo/tempo de execução

| | Linux<sup>1</sup><br/>Apenas código | Windows<sup>2</sup><br/>Apenas código | Linux<sup>1,3</sup><br/>Recipiente de estivador |
| --- | --- | --- | --- |
| **[Plano de consumo](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Sem apoio  |
| **[Plano Premium](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Plano dedicado](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)** | n/a | n/a |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux é o único sistema operativo suportado para a pilha de tempo de execução Python.  
<sup>2</sup> O Windows é o único sistema operativo suportado para a pilha de tempo de execução PowerShell.   
<sup>3</sup> Linux é o único sistema operativo suportado para contentores Docker.
<sup>4</sup> Para limites específicos para as várias opções do plano de Serviço de Aplicações, consulte os limites do [plano do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

### <a name="scale"></a>Escala

| | Aumentar horizontalmente | Max # instâncias |
| --- | --- | --- |
| **[Plano de consumo](#consumption-plan)** | Evento impulsionado. Escale automaticamente, mesmo durante períodos de alta carga. A Azure Functions escala os recursos de CPU e memória adicionando instâncias adicionais do anfitrião funções, com base no número de eventos em que as suas funções são ativadas. | 200 |
| **[Plano Premium](#premium-plan)** | Evento impulsionado. Escale automaticamente, mesmo durante períodos de alta carga. A Azure Functions escala os recursos de CPU e memória adicionando instâncias adicionais do anfitrião funções, com base no número de eventos em que as suas funções são ativadas. |100|
| **[Plano dedicado](#app-service-plan)**<sup>1</sup> | Manual/autoescala |10-20|
| **[ASE](#app-service-plan)**<sup>1</sup> | Manual/autoescala |100 |
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)**  | Autoescala orientada para eventos para clusters Kubernetes usando [KEDA](https://keda.sh). | Varia &nbsp; por &nbsp; aglomerado.&nbsp;&nbsp;|

<sup>1</sup> Para limites específicos para as várias opções do plano de Serviço de Aplicações, consulte os limites do [plano do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

### <a name="cold-start-behavior"></a>Comportamento de início a frio

|    |    | 
| -- | -- |
| **[Plano de consumo &nbsp;](#consumption-plan)** | As aplicações podem escalar para zero se estiverem inativas por um período de tempo, o que significa que alguns pedidos podem ter latência adicional no arranque.  O plano de consumo tem algumas otimizações para ajudar a diminuir o tempo de início a frio, incluindo puxar de funções de espaço reservado pré-aquecido que já têm o hospedeiro de função e os processos linguísticos em funcionamento. |
| **[Plano Premium](#premium-plan)** | Casos perpetuamente quentes para evitar qualquer arranque a frio. |
| **[Plano dedicado](#app-service-plan)**<sup>1</sup> | Ao executar um plano dedicado, o anfitrião funções pode funcionar continuamente, o que significa que o arranque a frio não é realmente um problema. |
| **[ASE](#app-service-plan)**<sup>1</sup> | Ao executar um plano dedicado, o anfitrião funções pode funcionar continuamente, o que significa que o arranque a frio não é realmente um problema. |
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)**  | Depende da configuração KEDA. As aplicações podem ser configuradas para correr sempre e nunca ter arranque a frio, ou configuradas para escala a zero, o que resulta em arranque a frio em novos eventos. 

<sup>1</sup> Para limites específicos para as várias opções do plano de Serviço de Aplicações, consulte os limites do [plano do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

### <a name="service-limits"></a>Limites do serviço

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Funcionalidades de rede

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Faturação

| | | 
| --- | --- |
| **[Plano de consumo](#consumption-plan)** | Pague apenas o tempo que as suas funções executarem. A faturação baseia-se no número de execuções, no tempo de execução e na memória utilizada. |
| **[Plano Premium](#premium-plan)** | O plano premium baseia-se no número de segundos e memórias do núcleo utilizados em instâncias necessárias e pré-aquecidas. Pelo menos um caso por plano deve ser sempre aquecido. Este plano proporciona preços mais previsíveis. |
| **[Plano dedicado](#app-service-plan)**<sup>1</sup> | Você paga o mesmo por aplicações de função em um Plano de Serviço de Aplicações como você faria para outros recursos do Serviço de Aplicações, como aplicações web.|
| **[ASE](#app-service-plan)**<sup>1</sup> | há uma taxa mensal fixa para um ASE que paga a infraestrutura e não muda com o tamanho do ASE. Além disso, há um custo por app service plano vCPU. Todas as aplicações alojadas num ASE estão na SKU de preços Isolada. |
| **[Utilizar o Kubernetes](functions-kubernetes-keda.md)**| Paga apenas os custos do seu cluster Kubernetes; nenhuma faturação adicional para funções. A sua aplicação de função funciona como uma carga de trabalho de aplicação em cima do seu cluster, tal como uma aplicação regular. |

<sup>1</sup> Para limites específicos para as várias opções do plano de Serviço de Aplicações, consulte os limites do [plano do Serviço de Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

## <a name="next-steps"></a>Passos seguintes

+ [Quickstart: Criar um projeto Azure Functions utilizando o Código do Estúdio Visual](./create-first-function-vs-code-csharp.md)
+ [Tecnologias de implantação em Funções Azure](functions-deployment-technologies.md) 
+ [Guia do programador das Funções do Azure](functions-reference.md)