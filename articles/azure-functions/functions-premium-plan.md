---
title: Plano Premium funções Azure
description: Detalhes e opções de configuração (VNet, sem arranque a frio, duração de execução ilimitada) para o plano Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.custom: references_regions
ms.openlocfilehash: 5ab506c57a78c67b33b888f1f50d83fe9813d0af
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506201"
---
# <a name="azure-functions-premium-plan"></a>Plano Premium funções Azure

O plano Azure Functions Premium (por vezes designado como plano Elastic Premium) é uma opção de hospedagem para aplicações de função. O plano Premium fornece funcionalidades como conectividade VNet, sem arranque a frio e hardware premium.  Aplicações de várias funções podem ser implementadas para o mesmo plano Premium, e o plano permite configurar o tamanho do caso computacional, o tamanho do plano base e o tamanho máximo do plano.  Para uma comparação do plano Premium e outros tipos de plano e hospedagem, consulte [a escala de função e as opções de hospedagem.](functions-scale.md)

## <a name="create-a-premium-plan"></a>Criar um plano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Também pode criar um plano Premium utilizando [o plano az functionapp criado](/cli/azure/functionapp/plan#az-functionapp-plan-create) no Azure CLI. O exemplo a seguir cria um plano _elástico premium 1:_

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Neste exemplo, `<RESOURCE_GROUP>` substitua-o pelo seu grupo de recursos e `<PLAN_NAME>` por um nome para o seu plano único no grupo de recursos. Especificar um [suporte suportado `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Para criar um plano Premium que suporte o Linux, inclua a `--is-linux` opção.

Com o plano criado, pode utilizar [a az functionapp criar](/cli/azure/functionapp#az-functionapp-create) para criar a sua aplicação de função. No portal, tanto o plano como a app são criados ao mesmo tempo. Para um exemplo de um script completo do Azure CLI, consulte [Criar uma aplicação de função num plano Premium.](scripts/functions-cli-create-premium-plan.md)

## <a name="features"></a>Funcionalidades

As seguintes funcionalidades estão disponíveis para funcionar aplicações implementadas num plano Premium.

### <a name="pre-warmed-instances"></a>Casos pré-aquecidos

Se não ocorrerem eventos e execuções hoje no plano De Consumo, a sua aplicação poderá escalar em zero casos. Quando novos eventos chegam, uma nova instância precisa de ser especializada com a sua app em execução.  A especializar novos casos pode demorar algum tempo dependendo da aplicação.  Esta latência adicional na primeira chamada é muitas vezes chamada de arranque frio da aplicação.

No plano Premium, pode ter a sua aplicação pré-aquecida num determinado número de casos, até ao tamanho mínimo do seu plano.  As instâncias pré-aquecidas também permitem pré-escalar uma aplicação antes de uma carga elevada. À medida que a aplicação se escala, ele escala pela primeira vez para os casos pré-aquecidos. Casos adicionais continuam a tamponar e aquecer imediatamente em preparação para a próxima operação à escala. Ao ter um tampão de casos pré-aquecidos, pode efetivamente evitar latências de arranque a frio.  As instâncias pré-aquecidas são uma característica do plano Premium, e você precisa manter pelo menos um exemplo em execução e disponível em todos os momentos o plano está ativo.

Pode configurar o número de casos pré-aquecidos no portal Azure selecionando a sua **App de Função**, indo ao separador **Funcionalidades** da Plataforma e selecionando as opções **Scale out.** Na janela de edição de aplicações de função, as instâncias pré-aquecidas são específicas dessa aplicação, mas as instâncias mínimas e máximas aplicam-se a todo o seu plano.

![Regulações de escala elástica](./media/functions-premium-plan/scale-out.png)

Também pode configurar casos pré-aquecidos para uma aplicação com o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Conectividade da rede privada

As Funções Azure implementadas num plano Premium aproveitam a nova integração do [VNet para aplicações web.](../app-service/web-sites-integrate-with-vnet.md)  Quando configurada, a sua aplicação pode comunicar com recursos dentro do seu VNet ou protegido através de pontos finais de serviço.  As restrições IP também estão disponíveis na aplicação para restringir o tráfego de entrada.

Ao atribuir uma sub-rede à sua aplicação de função num plano Premium, necessita de uma sub-rede com endereços IP suficientes para cada instância potencial. Necessitamos de um bloco IP com pelo menos 100 endereços disponíveis.

Para obter mais informações, consulte [integrar a sua aplicação de função com um VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Escala elástica rápida

Casos adicionais de computação são automaticamente adicionados para a sua app usando a mesma lógica de escala rápida que o plano de Consumo. As aplicações no mesmo Plano de Serviço de Aplicações escalam independentemente umas das outras com base nas necessidades de uma aplicação individual. No entanto, as aplicações functions no mesmo Plano de Serviço de Aplicações partilham recursos VM para ajudar a reduzir custos, quando possível. O número de aplicações associadas a um VM depende da pegada de cada app e do tamanho do VM.

Para saber mais sobre como funciona o dimensionamento, consulte [a escala de funções e o alojamento.](./functions-scale.md#how-the-consumption-and-premium-plans-work)

### <a name="longer-run-duration"></a>Duração de maior duração

As funções Azure num plano de consumo estão limitadas a 10 minutos para uma única execução.  No plano Premium, a duração de execução é de 30 minutos para evitar execuções em fuga. No entanto, pode [modificar o host.jsna configuração](./functions-host-json.md#functiontimeout) para tornar isto ilimitado para aplicações de plano Premium (60 minutos garantidos).

## <a name="plan-and-sku-settings"></a>Definições de Plano e SKU

Ao criar o plano, configura duas definições: o número mínimo de casos (ou tamanho do plano) e o limite máximo de explosão.  As instâncias mínimas são reservadas e estão sempre a funcionar.

> [!IMPORTANT]
> É cobrado por cada instância atribuída na contagem de instância mínima, independentemente de as funções estarem ou não a executar.

Se a sua aplicação necessitar de casos para além do tamanho do seu plano, pode continuar a escalar até que o número de casos atinja o limite máximo de explosão.  Você é cobrado por casos além do seu tamanho de plano apenas enquanto eles estão correndo e alugados para você.  Faremos o melhor esforço para reduzir a sua app até ao limite máximo definido, enquanto as instâncias de plano mínimo são garantidas para a sua aplicação.

Pode configurar o tamanho e os máximos do plano no portal Azure selecionando as opções **Scale out** no plano ou uma aplicação de função implementada nesse plano (em **Funcionalidades da Plataforma).**

Também pode aumentar o limite máximo de explosão a partir do CLI Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKUs de instância disponível

Ao criar ou escalar o seu plano, pode escolher entre três tamanhos de instância.  Será cobrado pelo número total de núcleos e memória consumidos por segundo.  A sua aplicação pode escalar automaticamente para várias instâncias, se necessário.  

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>Considerações de utilização da memória
Correr numa máquina com mais memória nem sempre significa que a sua aplicação de função utilizará toda a memória disponível.

Por exemplo, uma aplicação de função JavaScript é limitada pelo limite de memória padrão em Node.js. Para aumentar este limite de memória fixa, adicione a definição da aplicação `languageWorkers:node:arguments` com um valor de `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Escala Max Região Para fora

Abaixo estão os valores de escala máxima atualmente suportados para um único plano em cada região e configuração de SO. Para solicitar um aumento, abra um bilhete de apoio.

Consulte aqui a disponibilidade regional completa de Funções: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Região| Windows | Linux |
|--| -- | -- |
|Austrália Central| 20 | Não Disponível |
|Austrália Central 2| 20 | Não Disponível |
|Leste da Austrália| 100 | 20 |
|Sudeste da Austrália | 100 | 20 |
|Sul do Brasil| 60 | 20 |
|Canadá Central| 100 | 20 |
|E.U.A. Central| 100 | 20 |
|Ásia Leste| 100 | 20 |
|E.U.A. Leste | 100 | 20 |
|E.U.A. Leste 2| 100 | 20 |
|França Central| 100 | 20 |
|Alemanha Centro-Oeste| 100 | Não Disponível |
|Leste do Japão| 100 | 20 |
|Oeste do Japão| 100 | 20 |
|Coreia do Sul Central| 100 | 20 |
|E.U.A. Centro-Norte| 100 | 20 |
|Europa do Norte| 100 | 20 |
|Leste da Noruega| 20 | 20 |
|E.U.A. Centro-Sul| 100 | 20 |
|Sul da Índia | 100 | Não Disponível |
|Sudeste Asiático| 100 | 20 |
|Sul do Reino Unido| 100 | 20 |
|Oeste do Reino Unido| 100 | 20 |
|Europa Ocidental| 100 | 20 |
|Oeste da Índia| 100 | 20 |
|E.U.A. Centro-Oeste| 20 | 20 |
|E.U.A. Oeste| 100 | 20 |
|E.U.A. Oeste 2| 100 | 20 |

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Compreender a escala de funções Azure e opções de hospedagem](functions-scale.md)
