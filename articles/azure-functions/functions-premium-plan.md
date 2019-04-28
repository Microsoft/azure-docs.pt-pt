---
title: O plano Premium de funções do Azure (pré-visualização) | Documentos da Microsoft
description: Planear a detalhes e opções de configuração (VNet, não arranque a frio, a duração de execução ilimitado) para o Premium de funções do Azure.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: d327146c4a1fa61e55bb904308038c1ce717123d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031245"
---
# <a name="azure-functions-premium-plan-preview"></a>Plano Premium de funções do Azure (pré-visualização)

O plano Premium de funções do Azure é uma opção de hospedagem para aplicações de funções. O plano Premium fornece recursos como a conectividade VNet, não arranque a frio e premium hardware.  Várias aplicações de funções podem ser implementadas para o mesmo plano Premium e o plano permite-lhe configurar o tamanho da instância de computação, o tamanho do plano base e o tamanho máximo do plano.  Para uma comparação do plano Premium e outro plano e tipos de alojamento, veja [funcionar dimensionamento e as opções de hospedagem](functions-scale.md).

> [!NOTE]
> Atualmente, a pré-visualização de plano Premium suporta as funções em execução no .NET, de nó ou de Java através da infraestrutura do Windows.

## <a name="create-a-premium-plan"></a>Criar um plano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Também pode criar um plano Premium do Azure CLI

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Funcionalidades

As seguintes funcionalidades estão disponíveis para aplicações de funções implementadas num plano Premium.

### <a name="pre-warmed-instances"></a>Instâncias previamente preparadas

Se não existem eventos e execuções de hoje ocorrem no plano de consumo, a aplicação pode reduzir verticalmente para zero instâncias. Quando chegarem novos eventos, uma nova instância tem de ser especializadas com a sua aplicação em execução no mesmo.  Specializace novas instâncias podem demorar algum tempo consoante a aplicação.  Esta latência adicional na primeira chamada é frequentemente designada a frio de aplicação.

No plano Premium, pode ter a aplicação previamente começando num número de instâncias, até o tamanho mínimo de plano especificado.  Instâncias preparadas previamente também permitem-lhe pré-dimensionar uma aplicação antes de carga elevada. Como a aplicação aumenta horizontalmente, dimensiona-se em primeiro lugar para as instâncias warmed previamente. Instâncias adicionais continuam a memória intermédia de saída e quente imediatamente em preparação para a próxima operação de dimensionamento. Ao ter uma memória intermédia de instâncias preparadas previamente, pode evitar efetivamente latências de arranque a frio.  Instâncias preparadas previamente é um recurso do plano Premium e precisa ter, pelo menos, uma instância em execução e está disponível em todas as vezes que o plano ativa.

Pode configurar o número de instâncias preparadas previamente no portal do Azure, selecionando **aumentar horizontalmente** no **recursos da plataforma** separador.

![Definições de dimensionamento flexível](./media/functions-premium-plan/scale-out.png)

Também pode configurar previamente preparadas instâncias para uma aplicação com a CLI do Azure

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Conectividade de rede privada

As funções do Azure implementadas num plano Premium tira partido das [nova integração de VNet para aplicações web](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration).  Quando configurado, a aplicação pode comunicar com os recursos da sua VNet ou protegido por pontos finais de serviço.  Restrições de IP também estão disponíveis na aplicação para restringir o tráfego de entrada.

Ao atribuir uma sub-rede para a aplicação de funções num plano Premium, precisa de uma sub-rede com endereços IP suficientes para cada instância de potencial. Embora o número máximo de instâncias pode variar durante a pré-visualização, é necessário um bloco IP com, pelo menos, 100 endereços disponíveis.

Obter mais informações, consulte [integrar a sua aplicação de função com uma VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Dimensionamento elástico rápido

Instâncias de computação adicionais são adicionadas automaticamente para a sua aplicação utilizar a mesma lógica de dimensionamento rápida como o plano de consumo.  Para saber mais sobre como dimensionar funciona, veja [dimensionamento e alojamento de função](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Unbounded a duração da execução

As funções do Azure num plano de consumo estão limitadas a 10 minutos para uma única execução.  No plano Premium, a duração de execução padrão é 30 minutos para impedir a fuga de execuções. No entanto, pode [modificar a configuração de Host. JSON](./functions-host-json.md#functiontimeout) para que isso não vinculado para aplicações de plano Premium.

Em pré-visualização, a sua duração não é garantida últimos 12 minutos e terá a melhor chance de executar para além de 30 minutos, se a sua aplicação não é dimensionada para além de sua contagem mínima de trabalho.

## <a name="plan-and-sku-settings"></a>Definições de plano e SKU

Ao criar o plano, configurar duas definições: o número mínimo de instâncias (ou tamanho de plano) e o limite máximo de rajada.  As instâncias mínimas para um plano Premium é 1 e o fluxo máximo durante a pré-visualização é 20.  As instâncias mínimo são reservados e sempre em execução.

> [!IMPORTANT]
> É-lhe cobrada cada instância alocada na contagem de instâncias mínima independentemente se as funções estão em execução ou não.

Se a sua aplicação precisar de instâncias para além do tamanho do seu plano, ele pode continuar a aumentar horizontalmente até que o número de instâncias atinge o limite máximo de rajada.  É-lhe cobrada para instâncias além do tamanho do seu plano apenas enquanto eles estão em execução e alugados para.  Faremos dentro dos melhor esforços em dimensionando a sua aplicação, o seu limite máximo definido, ao passo que as instâncias de plano mínimo são garantidas para a sua aplicação.

Pode configurar o tamanho de plano e valores máximos no portal do Azure ao selecionar o **aumentar horizontalmente** opções no plano ou de uma aplicação de funções implementadas para esse plano (sob **recursos da plataforma**).

Também pode aumentar o limite máximo de rajada partir da CLI do Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKUs de instância disponível

Ao criar o seu plano de dimensionamento, pode escolher entre três tamanhos de instância.  Será ser cobrado o número total de núcleos e memória consumidas por segundo.  A aplicação automaticamente pode aumentar horizontalmente para várias instâncias conforme necessário.  

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3.5 GB|250 GB|
|EP2|2|7GB|250 GB|
|EP3|4|14GB|250 GB|

## <a name="regions"></a>Regiões

Seguem-se as regiões atualmente suportadas para a pré-visualização pública.

|Região|
|--|
|Leste da Austrália|
|Sudeste da Austrália|
|Canadá Central|
|EUA Central|
|Ásia Oriental|
|EUA Leste 2|
|França Central|
|Oeste do Japão|
|Coreia do Sul Central|
|Europa do Norte|
|EUA Centro-Sul|
|Sul da Índia|
|Sudeste Asiático|
|Reino Unido Oeste|
|Europa Ocidental|
|Oeste da Índia|
|EUA Oeste|

## <a name="known-issues"></a>Problemas Conhecidos

Pode controlar o estado dos problemas conhecidos do [pré-visualização pública no GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Compreender as funções do Azure dimensionamento e as opções de hospedagem](functions-scale.md)
