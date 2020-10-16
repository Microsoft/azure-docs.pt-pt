---
title: Plano Premium funções Azure
description: Detalhes e opções de configuração (VNet, sem arranque a frio, duração de execução ilimitada) para o plano Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
ms.openlocfilehash: a037c903a72ba79b79c7e6b011fe025aefd7b51d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578041"
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

### <a name="always-ready-instances"></a>Sempre prontos casos

Se não ocorrerem eventos e execuções hoje no plano De Consumo, a sua aplicação poderá escalar em zero casos. Quando novos eventos chegam, uma nova instância precisa de ser especializada com a sua app em execução.  A especializar novos casos pode demorar algum tempo dependendo da aplicação.  Esta latência adicional na primeira chamada é muitas vezes chamada de arranque frio da aplicação.

No plano Premium, pode ter a sua aplicação sempre pronta num determinado número de casos.  O número máximo de instâncias sempre prontas é de 20.  Quando os eventos começam a desencadear a aplicação, são encaminhados para as instâncias sempre prontas primeiro.  À medida que a função se torna ativa, casos adicionais serão aquecidos como um tampão.  Este tampão evita o arranque a frio para novas instâncias necessárias durante a escala.  Estes casos tamponados são chamados [de instâncias pré-aquecidas.](#pre-warmed-instances)  Com a combinação das instâncias sempre prontas e um tampão pré-aquecido, a sua aplicação pode efetivamente eliminar o arranque a frio.

> [!NOTE]
> Todos os planos premium terão pelo menos uma instância ativa (faturada) em todos os momentos.

Pode configurar o número de instâncias sempre prontas no portal Azure selecionando a sua **App de Função**, indo ao separador **Funcionalidades** da Plataforma e selecionando as opções **De Escala.** Na janela de edição de aplicações de função, as instâncias sempre prontas são específicas dessa aplicação.

![Regulações de escala elástica](./media/functions-premium-plan/scale-out.png)

Também pode configurar sempre instâncias prontas para uma aplicação com o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>Casos pré-aquecidos

Casos pré-aquecidos são o número de casos aquecidos como tampão durante eventos de escala e ativação.  As instâncias pré-aquecidas continuam a tampão até que o limite máximo de escala seja atingido.  A contagem de casos pré-aquecidos predefinidos é 1, e para a maioria dos cenários deve permanecer como 1.  Se uma aplicação tiver um longo aquecimento (como uma imagem personalizada do recipiente), pode desejar aumentar este tampão.  Um caso pré-aquecido só se tornará ativo depois de todas as instâncias ativas terem sido suficientemente utilizadas.

Considere este exemplo de como sempre os casos prontos e os casos pré-aquecidos funcionam em conjunto.  Uma aplicação de função premium tem cinco instâncias sempre prontas configuradas, e o padrão de um caso pré-aquecido.  Quando a aplicação estiver inativa e não houver eventos, a aplicação será aprovisionada e em execução em cinco instâncias.  Neste momento, você não será cobrado para um caso pré-aquecido, uma vez que as instâncias sempre prontas não são usadas, e nenhum caso pré-aquecido é sequer atribuído.

Assim que o primeiro gatilho chega, as cinco instâncias sempre prontas tornam-se ativas, e um caso pré-aquecido é atribuído.  A aplicação está agora em execução com seis instâncias aprovisionadas: as cinco instâncias agora ativas sempre prontas, e o sexto tampão pré-aquecido e inativo.  Se a taxa de execuções continuar a aumentar, os cinco casos ativos serão eventualmente utilizados.  Quando a plataforma decidir escalar para além de cinco instâncias, irá escalar para o caso pré-aquecido.  Quando isso acontecer, haverá agora seis instâncias ativas, e uma sétima instância será imediatamente a provisionada e preencherá o tampão pré-aquecido.  Esta sequência de escala e pré-aquecimento continuará até que a contagem máxima de instância para a aplicação seja alcançada.  Nenhuma ocorrência será pré-aquecida ou ativada para além do máximo.

Pode modificar o número de casos pré-aquecidos para uma aplicação utilizando o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>Ocorrências máximas para uma aplicação

Além da contagem máxima de instância do [plano,](#plan-and-sku-settings)pode configurar um máximo por aplicação.  O máximo da aplicação pode ser configurado usando o limite de escala de [aplicação.](./functions-scale.md#limit-scale-out)

### <a name="private-network-connectivity"></a>Conectividade da rede privada

As Funções Azure implementadas num plano Premium aproveitam a nova integração do [VNet para aplicações web.](../app-service/web-sites-integrate-with-vnet.md)  Quando configurada, a sua aplicação pode comunicar com recursos dentro do seu VNet ou protegido através de pontos finais de serviço.  As restrições IP também estão disponíveis na aplicação para restringir o tráfego de entrada.

Ao atribuir uma sub-rede à sua aplicação de função num plano Premium, necessita de uma sub-rede com endereços IP suficientes para cada instância potencial. Necessitamos de um bloco IP com pelo menos 100 endereços disponíveis.

Para obter mais informações, consulte [integrar a sua aplicação de função com um VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Escala elástica rápida

Casos adicionais de computação são automaticamente adicionados para a sua app usando a mesma lógica de escala rápida que o plano de Consumo. As aplicações no mesmo Plano de Serviço de Aplicações escalam independentemente umas das outras com base nas necessidades de uma aplicação individual. No entanto, as aplicações functions no mesmo Plano de Serviço de Aplicações partilham recursos VM para ajudar a reduzir custos, quando possível. O número de aplicações associadas a um VM depende da pegada de cada app e do tamanho do VM.

Para saber mais sobre como funciona o dimensionamento, consulte [a escala de funções e o alojamento.](./functions-scale.md#how-the-consumption-and-premium-plans-work)

### <a name="longer-run-duration"></a>Duração de maior duração

As funções Azure num plano de consumo estão limitadas a 10 minutos para uma única execução.  No plano Premium, a duração de execução é de 30 minutos para evitar execuções em fuga. No entanto, pode [modificar o host.jsna configuração](./functions-host-json.md#functiontimeout) para tornar a duração ilimitada das aplicações do plano Premium (60 minutos garantidos).

## <a name="plan-and-sku-settings"></a>Definições de Plano e SKU

Quando cria o plano, existem duas definições de tamanho do plano: o número mínimo de casos (ou tamanho do plano) e o limite máximo de explosão.

Se a sua aplicação necessitar de casos para além das instâncias sempre prontas, pode continuar a escalar até que o número de casos atinja o limite máximo de explosão.  É cobrado por casos além do tamanho do seu plano apenas enquanto eles estão a correr e alocados a si, numa base por segundo.  Faremos o melhor esforço para reduzir a sua app até ao limite máximo definido.

Pode configurar o tamanho e os máximos do plano no portal Azure selecionando as opções **Scale out** no plano ou uma aplicação de função implementada nesse plano (em **Funcionalidades da Plataforma).**

Também pode aumentar o limite máximo de explosão a partir do CLI Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

O mínimo para cada plano será pelo menos um exemplo.  O número mínimo real de ocorrências será configurado automaticamente para si com base nas instâncias sempre prontas solicitadas pelas aplicações no plano.  Por exemplo, se a aplicação A solicitar cinco instâncias sempre prontas, e a app B solicitar duas instâncias sempre prontas no mesmo plano, o tamanho mínimo do plano será calculado como cinco.  A App A estará em execução em todos os 5, e a aplicação B só estará em execução no dia 2.

> [!IMPORTANT]
> É cobrado por cada instância atribuída na contagem de instância mínima, independentemente de as funções estarem ou não a executar.

Na maioria das circunstâncias, este mínimo autocalculado deve ser suficiente.  No entanto, a escala para além do mínimo ocorre no melhor esforço.  É possível, embora improvável, que, num determinado intervalo de tempo, se atrase caso não existam casos adicionais.  Ao definir um mínimo superior ao mínimo autocalculado, reserva-se as instâncias antes da escala.

Aumentar o mínimo calculado para um plano pode ser feito usando o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKUs de instância disponível

Ao criar ou escalar o seu plano, pode escolher entre três tamanhos de instância.  Será cobrado o número total de núcleos e memória a provisionados, por segundo que cada instância lhe é atribuída.  A sua aplicação pode escalar automaticamente para várias instâncias, se necessário.  

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>Considerações de utilização da memória
Correr numa máquina com mais memória nem sempre significa que a sua aplicação de função utilizará toda a memória disponível.

Por exemplo, uma aplicação de função JavaScript é limitada pelo limite de memória padrão em Node.js. Para aumentar este limite de memória fixa, adicione a definição da aplicação `languageWorkers:node:arguments` com um valor de `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Escala Max Região Para fora

Abaixo estão os valores máximos de escala suportados atualmente para um único plano em cada região e configuração de SO. Para solicitar um aumento, por favor abra um bilhete de apoio.

Consulte aqui a disponibilidade regional completa de Funções: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Austrália Central| 100 | Não Disponível |
|Austrália Central 2| 100 | Não Disponível |
|Leste da Austrália| 100 | 20 |
|Sudeste da Austrália | 100 | 20 |
|Sul do Brasil| 100 | 20 |
|Canadá Central| 100 | 20 |
|E.U.A. Central| 100 | 20 |
|China Leste 2| 100 | 20 |
|China Norte 2| 100 | 20 |
|Ásia Leste| 100 | 20 |
|E.U.A. Leste | 100 | 20 |
|E.U.A. Leste 2| 100 | 20 |
|França Central| 100 | 20 |
|Alemanha Centro-Oeste| 100 | Não Disponível |
|Leste do Japão| 100 | 20 |
|Oeste do Japão| 100 | 20 |
|Coreia do Sul Central| 100 | 20 |
|Sul da Coreia do Sul| Não Disponível | 20 |
|E.U.A. Centro-Norte| 100 | 20 |
|Europa do Norte| 100 | 20 |
|Leste da Noruega| 100 | 20 |
|E.U.A. Centro-Sul| 100 | 20 |
|Sul da Índia | 100 | Não Disponível |
|Sudeste Asiático| 100 | 20 |
|Suíça Norte| 100 | Não Disponível |
|Suíça Oeste| 100 | Não Disponível |
|Sul do Reino Unido| 100 | 20 |
|Oeste do Reino Unido| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat Leste| 100 | Não Disponível |
|USNat Oeste| 100 | Não Disponível |
|Europa Ocidental| 100 | 20 |
|Oeste da Índia| 100 | 20 |
|E.U.A. Centro-Oeste| 100 | 20 |
|E.U.A. Oeste| 100 | 20 |
|E.U.A. Oeste 2| 100 | 20 |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Compreender a escala de funções Azure e opções de hospedagem](functions-scale.md)
