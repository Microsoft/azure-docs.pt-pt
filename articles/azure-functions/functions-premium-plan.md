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
- devx-track-azurecli
ms.openlocfilehash: 3061329ad9dcb368dab586acc2146e6fb4e23028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708718"
---
# <a name="azure-functions-premium-plan"></a>Plano Premium funções Azure

O plano Azure Functions Premium (por vezes designado como plano Elastic Premium) é uma opção de hospedagem para aplicações de função. Para outras opções de plano de hospedagem, consulte o artigo do [plano de hospedagem.](functions-scale.md)

O alojamento de plano premium proporciona os seguintes benefícios às suas funções:

* Evite o frio começa com instâncias perpetuamente quentes
* Conectividade de rede virtual.
* Duração de execução ilimitada, com 60 minutos garantidos.
* Tamanhos de exemplo premium: um núcleo, dois núcleos e quatro instâncias fundamentais.
* Preços mais previsíveis, em comparação com o plano de consumo.
* Alocação de aplicativos de alta densidade para planos com aplicações de múltiplas funções.

Quando se utiliza o plano Premium, são adicionados e removidos os casos do anfitrião das Funções Azure com base no número de eventos que chegam, tal como o [plano de Consumo.](consumption-plan.md) Aplicações de várias funções podem ser implementadas para o mesmo plano Premium, e o plano permite configurar o tamanho do caso computacional, o tamanho do plano base e o tamanho máximo do plano. 

## <a name="billing"></a>Faturação

A faturação do plano Premium baseia-se no número de segundos e memórias do núcleo atribuídos em todos os casos. Esta faturação difere do plano de Consumo, que é faturado por execução e memória consumida. Não há nenhuma acusação de execução com o plano Premium. Pelo menos uma instância deve ser atribuída a todo o momento por plano. Esta faturação resulta num custo mensal mínimo por plano ativo, independentemente de a função estar ativa ou inativa. Tenha em mente que todas as aplicações de função de um plano Premium partilham instâncias atribuídas. Para saber mais, consulte a página de preços do [Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-premium-plan"></a>Criar um plano Premium

Quando cria uma aplicação de função no portal Azure, o plano de Consumo é o padrão. Para criar uma aplicação de função que funcione num plano Premium, tem de criar explicitamente um plano de Serviço de Aplicações utilizando um dos SKUs _Premium Elásticos._ A aplicação de função que cria é então hospedada neste plano. O portal Azure facilita a criação tanto do plano Premium como da aplicação de função ao mesmo tempo. Pode executar mais do que uma aplicação de função no mesmo plano Premium, mas ambos funcionam no mesmo sistema operativo (Windows ou Linux). 

Os seguintes artigos mostram-lhe como criar uma aplicação de função com um plano Premium, quer programáticamente, quer no portal Azure:

+ [Portal do Azure](create-premium-plan-function-app-portal.md)
+ [CLI do Azure](scripts/functions-cli-create-premium-plan.md)
+ [Modelo Azure Resource Manager](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>Eliminar arranques frios

Quando eventos ou execuções não ocorrem no plano de Consumo, a sua aplicação pode escalar para zero casos. Quando surgirem novos eventos, uma nova instância com a sua app em execução deve ser especializada. A especializar novos casos pode demorar algum tempo dependendo da aplicação. Esta latência adicional na primeira chamada é muitas vezes chamada de _arranque a frio da_ aplicação .

O plano premium fornece duas funcionalidades que trabalham em conjunto para eliminar eficazmente o frio nas suas funções: _instâncias sempre prontas_ e _instâncias pré-aquecidas_. 

### <a name="always-ready-instances"></a>Sempre prontos casos

No plano Premium, pode ter a sua aplicação sempre pronta num determinado número de casos. O número máximo de instâncias sempre prontas é de 20. Quando os eventos começam a desencadear a aplicação, são encaminhados para as instâncias sempre prontas. À medida que a função se torna ativa, casos adicionais serão aquecidos como um tampão. Este tampão evita o arranque a frio para novas instâncias necessárias durante a escala. Estes casos tamponados são chamados [de instâncias pré-aquecidas.](#pre-warmed-instances) Com a combinação das instâncias sempre prontas e um tampão pré-aquecido, a sua aplicação pode efetivamente eliminar o arranque a frio.

> [!NOTE]
> Todos os planos premium têm pelo menos uma instância ativa (faturada) em todos os momentos.

# <a name="portal"></a>[Portal](#tab/portal)

Pode configurar o número de instâncias sempre prontas no portal Azure selecionando a sua **App de Função**, indo ao separador **Funcionalidades** da Plataforma e selecionando as opções **De Escala.** Na janela de edição de aplicações de função, as instâncias sempre prontas são específicas dessa aplicação.

![Regulações de escala elástica](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

Também pode configurar sempre instâncias prontas para uma aplicação com o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Casos pré-aquecidos

Casos pré-aquecidos são casos aquecidos como tampão durante eventos de escala e ativação. As instâncias pré-aquecidas continuam a tampão até que o limite máximo de escala seja atingido. A contagem de casos pré-aquecidos predefinidos é 1, e para a maioria dos cenários este valor deve permanecer como 1.

Quando uma aplicação tem um longo aquecimento (como uma imagem personalizada do recipiente), poderá ter de aumentar este tampão. Um caso pré-aquecido só se torna ativo depois de todas as instâncias ativas terem sido suficientemente utilizadas.

Considere este exemplo de como os casos sempre prontos e os casos pré-aquecidos funcionam em conjunto. Uma aplicação de função premium tem cinco instâncias sempre prontas configuradas, e o padrão de um caso pré-aquecido. Quando a aplicação está inativa e não há eventos a desencadear, a aplicação é aprovisionada e a funcionar com cinco instâncias. Neste momento, não é cobrado para um caso pré-aquecido, uma vez que as instâncias sempre prontas não são usadas, e nenhum caso pré-aquecido é atribuído.

Assim que o primeiro gatilho chega, as cinco instâncias sempre prontas tornam-se ativas, e um caso pré-aquecido é atribuído. A aplicação está agora em execução com seis instâncias aprovisionadas: as cinco instâncias agora ativas sempre prontas, e o sexto tampão pré-aquecido e inativo. Se a taxa de execuções continuar a aumentar, os cinco casos ativos são eventualmente utilizados. Quando a plataforma decide escalar para além de cinco instâncias, ele escala para o caso pré-aquecido. Quando isso acontece, existem agora seis instâncias ativas, e uma sétima instância é imediatamente a provisionada e preenche o tampão pré-aquecido. Esta sequência de escala e pré-aquecimento continua até que a contagem máxima de instância para a aplicação seja alcançada. Nenhuma ocorrência é pré-aquecida ou ativada para além do máximo.

Pode modificar o número de casos pré-aquecidos para uma aplicação utilizando o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Instâncias de aplicação de função máxima

Além da contagem máxima de instância do [plano,](#plan-and-sku-settings)pode configurar um máximo por aplicação. O máximo da aplicação pode ser configurado usando o limite de escala de [aplicação.](./event-driven-scaling.md#limit-scale-out)

## <a name="private-network-connectivity"></a>Conectividade da rede privada

As aplicações de função implementadas num plano Premium podem tirar partido da integração do [VNet para aplicações web.](../app-service/web-sites-integrate-with-vnet.md) Quando configurada, a sua aplicação pode comunicar com recursos dentro do seu VNet ou protegido através de pontos finais de serviço. As restrições IP também estão disponíveis na aplicação para restringir o tráfego de entrada.

Ao atribuir uma sub-rede à sua aplicação de função num plano Premium, necessita de uma sub-rede com endereços IP suficientes para cada instância potencial. Necessitamos de um bloco IP com pelo menos 100 endereços disponíveis.

Para obter mais informações, consulte [integrar a sua aplicação de função com um VNet](functions-create-vnet.md).

## <a name="rapid-elastic-scale"></a>Escala elástica rápida

Casos adicionais de computação são automaticamente adicionados para a sua app usando a mesma lógica de escala rápida que o plano de Consumo. As aplicações no mesmo Plano de Serviço de Aplicações escalam independentemente umas das outras com base nas necessidades de uma aplicação individual. No entanto, as aplicações functions no mesmo Plano de Serviço de Aplicações partilham recursos VM para ajudar a reduzir custos, quando possível. O número de aplicações associadas a um VM depende da pegada de cada app e do tamanho do VM.

Para saber mais sobre como funciona o dimensionamento, consulte [o dimensionamento orientado para o evento em Funções Azure.](event-driven-scaling.md)

## <a name="longer-run-duration"></a>Duração de maior duração

As funções Azure num plano de consumo estão limitadas a 10 minutos para uma única execução. No plano Premium, a duração de execução é de 30 minutos para evitar execuções em fuga. No entanto, é [possível modificar o host.jsna configuração](./functions-host-json.md#functiontimeout) para tornar a duração ilimitada das aplicações do plano Premium. Quando definido para uma duração não limitada, a sua aplicação de função é garantida para funcionar durante pelo menos 60 minutos. 

## <a name="plan-and-sku-settings"></a>Definições de Plano e SKU

Quando cria o plano, existem duas definições de tamanho do plano: o número mínimo de casos (ou tamanho do plano) e o limite máximo de explosão.

Se a sua aplicação necessitar de casos para além das instâncias sempre prontas, pode continuar a escalar até que o número de casos atinja o limite máximo de explosão. Só é cobrado por casos além do tamanho do seu plano enquanto eles estão a correr e a atribuir-lhe, numa base por segundo. A plataforma faz o seu melhor esforço para escalar a sua app para fora do limite máximo definido.

Pode configurar o tamanho e os máximos do plano no portal Azure selecionando as opções **Scale out** no plano ou uma aplicação de função implementada nesse plano (em **Funcionalidades da Plataforma).**

Também pode aumentar o limite máximo de explosão a partir do CLI Azure:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

O mínimo para cada plano será pelo menos um exemplo. O número mínimo real de ocorrências será configurado automaticamente para si com base nas instâncias sempre prontas solicitadas pelas aplicações no plano. Por exemplo, se a aplicação A solicitar cinco instâncias sempre prontas, e a app B solicitar duas instâncias sempre prontas no mesmo plano, o tamanho mínimo do plano será calculado como cinco. A App A estará em execução em todos os 5, e a aplicação B só estará em execução no dia 2.

> [!IMPORTANT]
> É cobrado por cada instância atribuída na contagem de instância mínima, independentemente de as funções estarem ou não a executar.

Na maioria das circunstâncias, este mínimo autocalculado é suficiente. No entanto, a escala para além do mínimo ocorre no melhor esforço. É possível, embora improvável, que a uma escala de tempo específica possa ser adiada se caso casos adicionais não estejam disponíveis. Ao definir um mínimo superior ao mínimo autocalculado, reserva-se as instâncias antes da escala.

Aumentar o mínimo calculado para um plano pode ser feito usando o Azure CLI.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>SKUs de instância disponível

Ao criar ou escalar o seu plano, pode escolher entre três tamanhos de instância. Será cobrado o número total de núcleos e memória a provisionados, por segundo que cada instância lhe é atribuída. A sua aplicação pode escalar automaticamente para várias instâncias, se necessário.

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-usage-considerations"></a>Considerações de utilização da memória

Correr numa máquina com mais memória nem sempre significa que a sua aplicação de função utiliza toda a memória disponível.

Por exemplo, uma aplicação de função JavaScript é limitada pelo limite de memória padrão em Node.js. Para aumentar este limite de memória fixa, adicione a definição da aplicação `languageWorkers:node:arguments` com um valor de `--max-old-space-size=<max memory in MB>` .

E para planos com memória superior a 4GB, certifique-se de que a Definição da Plataforma bitness está definida `64 Bit` em [Definições Gerais](../app-service/configure-common.md#configure-general-settings).

## <a name="region-max-scale-out"></a>Escala Max Região Para fora

Abaixo estão os valores máximos de escala suportados atualmente para um único plano em cada região e configuração de SO. Para solicitar um aumento, você pode abrir um bilhete de apoio.

Consulte a disponibilidade regional completa de Funções no site da [Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Austrália Central| 100 | Não Disponível |
|Austrália Central 2| 100 | Não Disponível |
|Leste da Austrália| 100 | 20 |
|Austrália Sudeste | 100 | 20 |
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
> [Compreenda as opções de hospedagem de Funções Azure](functions-scale.md)