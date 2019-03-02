---
title: Dimensionamento de funções do Azure e alojamento | Documentos da Microsoft
description: Saiba como escolher entre o plano de consumo de funções do Azure e o plano do serviço de aplicações.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funções, funções, plano de consumo, plano do serviço de aplicações, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/28/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d385fd8c8388e3ce54b89ff2ac863cd5a1aa0df
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216140"
---
# <a name="azure-functions-scale-and-hosting"></a>Dimensionamento de funções do Azure e alojamento

As Funções do Azure são executadas em dois modos diferentes: Plano de consumo e plano de serviço de aplicações do Azure. O plano de consumo aloca automaticamente a potência de computação quando o código é executado. A aplicação é aumentada horizontalmente, quando necessário para processar a carga e reduzida verticalmente quando o código não está em execução. Não é preciso pagar para as VMs inativas ou com antecedência a capacidade de reserva.

> [!NOTE]  
> Plano de consumo para Linux está [agora em pré-visualização pública](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/).

Se não estiver familiarizado com as funções do Azure, veja a [descrição geral das funções do Azure](functions-overview.md).

Quando cria uma aplicação de funções, a escolher o plano de alojamento para as funções na aplicação. O plano, uma instância do *anfitrião de funções do Azure* executa as funções. O tipo de controles do plano:

* Como as instâncias de anfitrião são aumentadas horizontalmente.
* Os recursos que estão disponíveis para cada anfitrião.

> [!IMPORTANT]
> Tem de escolher o tipo de plano de alojamento durante a criação da aplicação de funções. Não é possível alterá-lo mais tarde.

Num plano do serviço de aplicações, pode dimensionar entre camadas para alocar diferente quantidade de recursos. O plano de consumo, as funções do Azure processa automaticamente a alocação de recursos de todos os. 

## <a name="consumption-plan"></a>Plano de consumo

Quando estiver a utilizar um plano de consumo, instâncias do anfitrião de funções do Azure dinamicamente são adicionadas e removidas com base no número de eventos de entrada. Este plano sem servidor é dimensionado automaticamente e que lhe é cobrada para recursos de computação apenas quando as suas funções estão em execução. No plano de consumo, uma execução de função exceder o tempo limite após um período de tempo configurável.

> [!NOTE]
> O tempo limite predefinido para funções num plano de consumo é de 5 minutos. O valor pode ser aumentado para a aplicação de funções até um máximo de 10 minutos, alterando a propriedade `functionTimeout` no [Host. JSON](functions-host-json.md#functiontimeout) arquivo de projeto.

A faturação baseia-se no número de execuções, tempo de execução e a memória utilizada. A faturação é agregada entre todas as funções dentro de uma aplicação de funções. Para obter mais informações, consulte a [funções do Azure, página de preços].

O plano de consumo é o padrão de plano de alojamento e oferece as seguintes vantagens:

* Paga apenas quando as suas funções estão em execução.
* Aumentar horizontalmente, automaticamente, mesmo durante os períodos de alto de carga.

## <a name="app-service-plan"></a>Plano do App Service

No plano do serviço de aplicações dedicado, as suas aplicações de função executam em VMs dedicadas no básico, Standard, Premium e os SKUs isolados, que é o mesmo que outras aplicações de serviço de aplicações. VMs dedicadas são alocadas à sua aplicação de função, o que significa que o anfitrião de funções pode ser [sempre em execução](#always-on). Planos de serviço de aplicações suportam o Linux.

Considere um plano do serviço de aplicações nos seguintes casos:

* Tem as VMs existentes, subutilizadas que já estejam a executar outras instâncias de serviço de aplicações.
* As suas aplicações de função executam continuamente, ou quase contínua. Neste caso, um plano do serviço de aplicações pode ser mais económico.
* Precisa de mais opções de CPU ou memória que o que é fornecido no plano de consumo.
* O código precisa ser executado mais do que o tempo de execução máximo permitido no plano de consumo, o que é até 10 minutos.
* Precisa de funcionalidades que só estão disponíveis num plano do serviço de aplicações, como o suporte para o ambiente de serviço de aplicações, conectividade VPN da VNET/e tamanhos de VM maiores.
* Pretende executar a aplicação de funções no Linux ou pretender fornecer uma imagem personalizada para executar as suas funções.

Uma VM dissocia o custo do número de execuções, tempo de execução e a memória utilizada. Como resultado, não paga mais do que o custo da instância VM que aloca. Para obter detalhes sobre como funciona o plano do serviço de aplicações, consulte a [descrição geral aprofundada dos planos do App Service do Azure](../app-service/overview-hosting-plans.md). 

Com um plano do serviço de aplicações, manualmente pode aumentar horizontalmente ao adicionar mais instâncias VM ou, pode ativar o dimensionamento automático. Para obter mais informações, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Também pode aumentar verticalmente ao escolher um plano de serviço de aplicações diferente. Para obter mais informações, consulte [aumentar verticalmente uma aplicação no Azure](../app-service/web-sites-scale.md). 

Ao executar as funções JavaScript num plano do serviço de aplicações, deve escolher um plano que tem menos de vCPUs. Para obter mais informações, consulte [escolha planos de serviço de aplicações de núcleo único](functions-reference-node.md#choose-single-vcpu-app-service-plans).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

###<a name="always-on"></a> Always On

Se executar num plano do serviço de aplicações, deve ativar os **sempre no** definição para que a aplicação function app é executada corretamente. Num plano do serviço de aplicações, o runtime das funções fica inativo após alguns minutos de inatividade, para que os acionadores HTTP só serão "despertar" as suas funções. Sempre está disponível apenas num plano do serviço de aplicações. No plano de consumo, a plataforma ativa aplicações function App automaticamente.

## <a name="what-is-my-hosting-plan"></a>O que é o meu plano de alojamento

Para determinar o plano de alojamento utilizado pela sua aplicação de função, veja **plano do serviço de aplicações / escalão de preço** no **descrição geral** separador para a aplicação de funções no [portal do Azure](https://portal.azure.com). Para os planos de serviço de aplicações, o escalão de preço também é indicado. 

![Ver o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também pode utilizar a CLI do Azure para determinar o plano, da seguinte forma:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando o resultado deste comando é `dynamic`, a sua aplicação de função está no plano de consumo. Todos os outros valores indicam as camadas de um plano de serviço de aplicações.

Mesmo com Always On ativado, o tempo de limite de execução de funções individuais é controlado pelos `functionTimeout` definição [Host. JSON](functions-host-json.md#functiontimeout) arquivo de projeto.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

No plano de consumo ou um plano do serviço de aplicações, uma aplicação de funções requer uma conta de armazenamento do Azure geral, que suporta o armazenamento de Blobs do Azure, filas, ficheiros e tabela. Isto acontece porque as funções baseia-se no armazenamento do Azure para operações como a gestão de acionadores e execuções de função de registo, mas algumas contas de armazenamento não suportam filas e tabelas. Estas contas, que incluem as contas de armazenamento apenas de BLOBs (incluindo o armazenamento premium) e contas de armazenamento para fins gerais com replicação de armazenamento com redundância de zona, são filtrados-out do seu existente **conta de armazenamento** seleções ao criar uma aplicação de funções.

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, veja [apresentação dos serviços do armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Como funciona o plano de consumo

O plano de consumo, o controlador de escala dimensiona automaticamente recursos de CPU e memória adicionando instâncias adicionais do anfitrião de funções, com base no número de eventos que suas funções são acionadas no. Cada instância do host de funções está limitada a 1,5 GB de memória.  Uma instância do host é a aplicação de funções, que significa que todas as funções dentro de um recurso de compartilhamento de aplicação de função dentro de uma instância e o dimensionamento ao mesmo tempo. Aplicações de funções que partilham o mesmo plano de consumo são dimensionadas de forma independente.  

Quando utiliza o plano de alojamento de consumo, os arquivos de código de função são armazenados em partilhas de ficheiros do Azure na conta de armazenamento principal da função. Ao eliminar a conta de armazenamento principal da aplicação de função, os arquivos de código de função são eliminados e não podem ser recuperados.

> [!NOTE]
> Quando estiver a utilizar um acionador de blob num plano de consumo, pode haver até um atraso de 10 minutos no processamento novos blobs. Este atraso ocorre quando uma aplicação de funções tornou-se inativo. Depois da aplicação de função está em execução, os blobs são processadas imediatamente. Para evitar este atraso de arranque a frio, utilize um plano do serviço de aplicações com **Always On** ativada ou utilizar o acionador do Event Grid. Para obter mais informações, consulte [o artigo de referência de ligação de Acionador de blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Dimensionamento do tempo de execução

As funções do Azure utiliza um componente chamado de *controlador de escala* para monitorizar a taxa de eventos e determinar se deve ampliar ou reduzir horizontalmente. O controlador de dimensionamento utiliza a heurística para cada tipo de Acionador. Por exemplo, quando estiver a utilizar um acionador do armazenamento de filas do Azure, dimensiona com base no comprimento da fila e a idade da mensagem de fila mais antiga.

A unidade de escala é a aplicação de funções. Quando a aplicação de funções é aumentada horizontalmente, recursos adicionais são alocados para executar várias instâncias do anfitrião de funções do Azure. Por outro lado, como computação a pedido é reduzida, o controlador de escala remove instâncias de anfitrião de função. O número de instâncias é eventualmente reduzido verticalmente para zero quando não existem funções estão em execução dentro de uma aplicação de funções.

![Controlador de dimensionamento, eventos de monitorização e criação de instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Comportamentos de dimensionamento de compreensão

Dimensionamento pode variar em vários fatores e o dimensionamento de forma diferente, consoante o acionador e o idioma selecionado. No entanto, há alguns aspetos do dimensionamento que existem atualmente no sistema:

* Uma aplicação de funções individual só é aumentada verticalmente para um máximo de 200 instâncias. Uma única instância pode processar mais do que uma mensagem ou pedido de cada vez, então não é um conjunto de limite no número de execuções simultâneas.
* Para acionadores de HTTP, novas instâncias serão apenas alocadas no máximo uma vez a cada 1 segundo.
* Para acionadores de não-HTTP, novas instâncias serão apenas alocadas no máximo uma vez a cada 30 segundos.

Acionadores diferentes também podem ter diferentes limites de dimensionamento, bem como documentado abaixo:

* [Hub de Eventos](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicações escaláveis

Há muitos aspectos de uma aplicação de funções que afetam a eficiência com que ele dimensionará, incluindo configuração do anfitrião, requisitos de espaço de tempo de execução e a eficiência de recursos.  Para obter mais informações, consulte a [secção de escalabilidade do artigo de considerações de desempenho](functions-best-practices.md#scalability-best-practices). Também deve estar ciente de como ligações se comportar consoante o dimensionamento da aplicação de função. Para obter mais informações, consulte [como gerir ligações nas funções do Azure](manage-connections.md).

### <a name="billing-model"></a>Modelo de faturação

Faturação para o plano de consumo é descrito detalhadamente sobre o [funções do Azure, página de preços]. Utilização é agregada ao nível da aplicação de função e contagens de apenas o tempo que o código de função é executado. Seguem-se as unidades de faturação:

* **Consumo de recursos em segundos de gigabytes (GB-s)**. Calculada como uma combinação de tamanho de memória e tempo de execução para todas as funções dentro de uma aplicação de funções. 
* **Execuções**. Contabilizadas sempre que uma função é executada em resposta a um disparador de eventos.

[Funções do Azure, página de preços]: https://azure.microsoft.com/pricing/details/functions
