---
title: Dimensionamento de funções do Azure e alojamento | Documentos da Microsoft
description: Saiba como escolher entre o plano de consumo de funções do Azure e o plano Premium.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funções, funções, plano de consumo, o plano premium, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3253cc7e379ae63880d533f14bc76e7af5a4425a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050543"
---
# <a name="azure-functions-scale-and-hosting"></a>Dimensionamento de funções do Azure e alojamento

Quando cria uma aplicação de funções no Azure, tem de escolher um plano de alojamento para a sua aplicação. Existem três planos de alojamento disponíveis para as funções do Azure: [Plano de consumo](#consumption-plan), [plano Premium](#premium-plan), e [plano do App Service](#app-service-plan).

O plano de alojamento que escolher determina os comportamentos seguintes:

* Como a sua aplicação function app é dimensionada.
* Os recursos disponíveis para cada instância de aplicação de função.
* Suporte para recursos avançados, como conectividade VNET.

Planos de consumo e o Premium adicionam automaticamente o poder de computação quando o código é executado. A aplicação é aumentada horizontalmente, quando necessário para processar a carga e reduzida verticalmente quando o código deixa de ser executada. Para o plano de consumo, também não é necessário que pagar as VMs inativas ou com antecedência a capacidade de reserva.  

O plano premium fornece recursos adicionais, como premium instâncias de computação, a capacidade de manter instâncias quente indefinidamente e conectividade VNet.

Plano do App Service permite-lhe tirar partido da infraestrutura dedicada, que gere. A aplicação de funções não dimensiona com base em eventos, que significa que nunca é dimensionado para zero. (Requer que [sempre no](#always-on) está ativada.)

> [!NOTE]
> Pode alternar entre planos de consumo e Premium, alterando a propriedade do plano do recurso de aplicação de função.

## <a name="hosting-plan-support"></a>Suporte do plano de alojamento

Suporte de funcionalidades enquadra-se as seguintes duas categorias:

* _Disponibilidade geral (GA)_ : totalmente suportado e aprovada para utilização em produção.
* _Pré-visualização_: ainda não foram totalmente suportado e aprovada para utilização em produção.

A tabela seguinte indica o nível atual de suporte para os três planos de alojamento, quando em execução no Windows ou Linux:

| | Plano de consumo | Plano Premium | Plano dedicado |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | pré-visualização | GA |
| Linux | pré-visualização | N/A | GA |

## <a name="consumption-plan"></a>Plano de consumo

Quando estiver a utilizar o plano de consumo, instâncias do anfitrião de funções do Azure dinamicamente são adicionadas e removidas com base no número de eventos de entrada. Este plano sem servidor é dimensionado automaticamente e que lhe é cobrada para recursos de computação apenas quando as suas funções estão em execução. No plano de consumo, uma execução de função exceder o tempo limite após um período de tempo configurável.

A faturação baseia-se no número de execuções, tempo de execução e a memória utilizada. A faturação é agregada entre todas as funções dentro de uma aplicação de funções. Para obter mais informações, consulte a [funções do Azure, página de preços](https://azure.microsoft.com/pricing/details/functions/).

O plano de consumo é o padrão de plano de alojamento e oferece as seguintes vantagens:

* Pague apenas quando as suas funções estão em execução
* Aumentar horizontalmente, automaticamente, mesmo durante os períodos de alto de carga

Aplicações de funções na mesma região podem ser atribuídas para o mesmo plano de consumo. Não existe nenhuma desvantagem ou impacto para ter várias aplicações em execução no mesmo plano de consumo. A atribuição de várias aplicações para o mesmo plano de consumo não tem qualquer impacto na fiabilidade de cada aplicação, escalabilidade ou resiliência.

## <a name="premium-plan"></a>Plano premium (pré-visualização)

Quando estiver a utilizar o plano Premium, as instâncias do anfitrião de funções do Azure são adicionadas e removidas com base no número de eventos de entrada como o plano de consumo.  Plano premium suporta as seguintes funcionalidades:

* Instâncias perpetuamente quente para evitar qualquer arranque a frio
* Conectividade VNet
* Duração de execução ilimitado
* Tamanhos de instâncias de Premium (um núcleo, dois núcleos e quatro instâncias de núcleos)
* Preços mais previsível
* Atribuição de aplicações de alta densidade para planos com várias aplicações de funções

Informações sobre como pode configurar estas opções podem ser encontradas no [documento de plano as funções do Azure premium](functions-premium-plan.md).

Em vez de faturação por execução e de memória consumida, a faturação para o plano Premium baseia-se no número de núcleos por segundo, o tempo de execução e memória utilizada pelas instâncias necessárias e reservadas.  Pelo menos uma instância tem de ser quente at a todas as vezes. Isso significa que existe um custo fixo mensal por plano do Active Directory, independentemente do número de execuções.

Considere o plano premium de funções do Azure nas seguintes situações:

* As suas aplicações de função executam continuamente, ou quase contínua.
* Precisa de mais opções de CPU ou memória que o que é fornecido pelo plano de consumo.
* O seu código precisa para ser executada mais do que o [tempo de execução máximo permitido](#timeout) no plano de consumo.
* Precisa de funcionalidades que só estão disponíveis num plano Premium, como conectividade VPN da VNET /.

> [!NOTE]
> A pré-visualização de plano premium atualmente suporta apenas as funções do Azure em Windows.

Ao executar as funções JavaScript num plano Premium, deve escolher uma instância que tem menos de vCPUs. Para obter mais informações, consulte a [escolha planos Premium de núcleo único](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedicado (plano do App Service)

As suas aplicações de função também podem executar nas VMs dedicadas mesmo como outras aplicações de serviço de aplicações (básico, Standard, Premium e os SKUs isolados).

Considere um plano do serviço de aplicações nas seguintes situações:

* Tem as VMs existentes, subutilizadas que já estejam a executar outras instâncias de serviço de aplicações.
* Deseja fornecer uma imagem personalizada para executar as suas funções.

Paga o mesmo para aplicações de funções num plano do serviço de aplicações tal como faria para outros recursos de serviço de aplicações, como aplicações web. Para obter detalhes sobre como funciona o plano do serviço de aplicações, consulte a [descrição geral aprofundada dos planos do App Service do Azure](../app-service/overview-hosting-plans.md).

Com um plano do serviço de aplicações, manualmente pode aumentar horizontalmente ao adicionar mais instâncias VM. Também pode ativar o dimensionamento automático. Para obter mais informações, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Também pode aumentar verticalmente ao escolher um plano de serviço de aplicações diferente. Para obter mais informações, consulte [aumentar verticalmente uma aplicação no Azure](../app-service/web-sites-scale.md). 

Ao executar as funções JavaScript num plano do serviço de aplicações, deve escolher um plano que tem menos de vCPUs. Para obter mais informações, consulte [escolha planos de serviço de aplicações de núcleo único](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Se executar num plano do serviço de aplicações, deve ativar os **sempre no** definição para que a aplicação function app é executada corretamente. Num plano do serviço de aplicações, o runtime das funções fica inativo após alguns minutos de inatividade, para que os acionadores HTTP só serão "despertar" as suas funções. Sempre está disponível apenas num plano do serviço de aplicações. No plano de consumo, a plataforma ativa aplicações function App automaticamente.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Mesmo com Always On ativado, o tempo de limite de execução de funções individuais é controlado pelos `functionTimeout` definição [Host. JSON](functions-host-json.md#functiontimeout) arquivo de projeto.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determinar o plano de alojamento da aplicação existente

Para determinar o plano de alojamento utilizado pela sua aplicação de função, veja **plano do serviço de aplicações / escalão de preço** no **descrição geral** separador para a aplicação de funções no [portal do Azure](https://portal.azure.com). Para os planos de serviço de aplicações, o escalão de preço também é indicado.

![Ver o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também pode utilizar a CLI do Azure para determinar o plano, da seguinte forma:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando o resultado deste comando é `dynamic`, a sua aplicação de função está no plano de consumo. Quando o resultado deste comando é `ElasticPremium`, a aplicação de função está no plano Premium. Todos os outros valores indicam as diferentes camadas de um plano de serviço de aplicações.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em qualquer plano, uma aplicação de funções requer uma conta de armazenamento do Azure geral, que suporta o armazenamento de Blobs do Azure, filas, ficheiros e tabela. Isto acontece porque as funções dependem de armazenamento do Azure para operações como a gestão de acionadores e execuções de função de registo, mas algumas contas de armazenamento não suportam filas e tabelas. Estas contas, que incluem as contas de armazenamento apenas de BLOBs (incluindo o armazenamento premium) e contas de armazenamento para fins gerais com replicação de armazenamento com redundância de zona, são filtrados-out do seu existente **conta de armazenamento** seleções ao criar uma aplicação de funções.

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, veja [apresentação dos serviços do armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Como funcionam os planos de consumo e premium

No consumo e planos premium, a infraestrutura de funções do Azure dimensiona os recursos de CPU e memória adicionando instâncias adicionais do anfitrião de funções, com base no número de eventos que suas funções são acionadas no. Cada instância do host de funções no plano de consumo está limitada a 1,5 GB de memória e de uma CPU.  Uma instância do host é a aplicação de funções toda, que significa que todas as funções dentro de um recurso de compartilhamento de aplicação de função dentro de uma instância e o dimensionamento ao mesmo tempo. Aplicações de funções que partilham o mesmo plano de consumo são dimensionadas de forma independente.  No plano premium, o tamanho do seu plano irá determinar a CPU e memória disponível para todas as aplicações nesse plano nessa instância.  

Arquivos de código de função são armazenados em partilhas de ficheiros do Azure na conta de armazenamento principal da função. Ao eliminar a conta de armazenamento principal da aplicação de função, os arquivos de código de função são eliminados e não podem ser recuperados.

> [!NOTE]
> Quando estiver a utilizar um acionador de blob num plano de consumo, pode haver até um atraso de 10 minutos no processamento novos blobs. Este atraso ocorre quando uma aplicação de funções tornou-se inativo. Depois da aplicação de função está em execução, os blobs são processadas imediatamente. Para evitar este atraso de arranque a frio, utilize o plano Premium ou utilize o [acionador do Event Grid](functions-bindings-event-grid.md). Para obter mais informações, consulte [o artigo de referência de ligação de Acionador de blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Dimensionamento do tempo de execução

As funções do Azure utiliza um componente chamado de *controlador de escala* para monitorizar a taxa de eventos e determinar se deve ampliar ou reduzir horizontalmente. O controlador de dimensionamento utiliza a heurística para cada tipo de Acionador. Por exemplo, quando estiver a utilizar um acionador do armazenamento de filas do Azure, dimensiona com base no comprimento da fila e a idade da mensagem de fila mais antiga.

A unidade de escala para as funções do Azure é a aplicação de funções. Quando a aplicação de funções é aumentada horizontalmente, recursos adicionais são alocados para executar várias instâncias do anfitrião de funções do Azure. Por outro lado, como computação a pedido é reduzida, o controlador de escala remove instâncias de anfitrião de função. O número de instâncias é eventualmente reduzido verticalmente para zero quando não existem funções estão em execução dentro de uma aplicação de funções.

![Controlador de dimensionamento, eventos de monitorização e criação de instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Comportamentos de dimensionamento de compreensão

Dimensionamento pode variar em vários fatores e o dimensionamento de forma diferente, consoante o acionador e o idioma selecionado. Há algumas complicações dos comportamentos de dimensionamento para ter em consideração:

* Uma aplicação de funções individual só é aumentada verticalmente para um máximo de 200 instâncias. Uma única instância pode processar mais do que uma mensagem ou pedido de cada vez, então não é um conjunto de limite no número de execuções simultâneas.
* Para acionadores de HTTP, novas instâncias serão apenas alocadas no máximo uma vez a cada 1 segundo.
* Para acionadores de não-HTTP, novas instâncias serão apenas alocadas no máximo uma vez a cada 30 segundos.

Acionadores diferentes também podem ter diferentes limites de dimensionamento, bem como documentado abaixo:

* [Hub de Eventos](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicações escaláveis

Há muitos aspectos de uma aplicação de funções que afetam a eficiência com que ele dimensionará, incluindo configuração do anfitrião, requisitos de espaço de tempo de execução e a eficiência de recursos.  Para obter mais informações, consulte a [secção de escalabilidade do artigo de considerações de desempenho](functions-best-practices.md#scalability-best-practices). Também deve estar ciente de como ligações se comportar consoante o dimensionamento da aplicação de função. Para obter mais informações, consulte [como gerir ligações nas funções do Azure](manage-connections.md).

### <a name="billing-model"></a>Modelo de faturação

A faturação para os diferentes planos é descrita detalhadamente no [funções do Azure, página de preços](https://azure.microsoft.com/pricing/details/functions/). Utilização é agregada ao nível da aplicação de função e contagens de apenas o tempo que o código de função é executado. Seguem-se as unidades de faturação:

* **Consumo de recursos em segundos de gigabytes (GB-s)** . Calculada como uma combinação de tamanho de memória e tempo de execução para todas as funções dentro de uma aplicação de funções. 
* **Execuções**. Contabilizadas sempre que uma função é executada em resposta a um disparador de eventos.

Consultas úteis e informações sobre como compreender a sua fatura de consumo podem ser encontradas [nas FAQ do faturação](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites do serviço

A tabela seguinte indica os limites que se aplicam às aplicações de função quando em execução nos vários planos de alojamento:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
