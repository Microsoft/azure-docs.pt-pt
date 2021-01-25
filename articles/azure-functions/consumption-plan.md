---
title: Azure Funções Plano de Consumo
description: Saiba como o plano de consumo de funções Azure permite executar o seu código num ambiente que escala dinamicamente, mas só paga pelos recursos utilizados durante a execução.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760544"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Funções Plano de Consumo

Quando se utiliza o plano de consumo, os casos do anfitrião das Funções Azure são adicionados e removidos de forma dinâmica com base no número de eventos que chegam. O plano de consumo é a opção de hospedagem <em>totalmente sem servidor</em> para funções Azure.

## <a name="benefits"></a>Benefícios

O plano de consumo escala automaticamente, mesmo durante períodos de alta carga. Ao executar funções num plano de consumo, é cobrado por recursos de computação apenas quando as suas funções estão em funcionamento. Num plano de consumo, a execução de uma função excede o tempo limite após um período de tempo configurável.

Para comparar o plano de consumo com os outros tipos de plano e de hospedagem, consulte [a escala de função e as opções de hospedagem.](functions-scale.md)

## <a name="billing"></a>Faturação

A faturação baseia-se no número de execuções, no tempo de execução e na memória utilizada. A utilização é agregada em todas as funções dentro de uma aplicação de função. Para obter mais informações, consulte a página de preços do [Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Para saber mais sobre como estimar os custos ao executar um plano de consumo, consulte [os custos do plano de consumo de compreensão.](functions-consumption-costs.md)

## <a name="create-a-consumption-plan-function-app"></a>Criar uma app de função de plano de consumo

Quando cria uma aplicação de função no portal Azure, o plano de Consumo é o padrão. Ao utilizar APIs para criar a sua aplicação de função, não precisa primeiro de criar um plano de Serviço de Aplicações como faz com planos Premium e Dedicados.

Utilize os seguintes links para aprender a criar uma aplicação de função sem servidor num plano de Consumo, quer programáticamente, quer no portal Azure:

+ [CLI do Azure](./scripts/functions-cli-create-serverless.md)
+ [Portal do Azure](./functions-get-started.md)
+ [Modelo Azure Resource Manager](functions-create-first-function-resource-manager.md)

Também pode criar aplicações de função num plano de consumo quando publicar um projeto De Funções a partir do [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) ou do Visual [Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Várias aplicações no mesmo plano

As aplicações de função na mesma região podem ser atribuídas ao mesmo plano de Consumo. Não há nenhuma desvantagem ou impacto em ter várias aplicações a funcionar no mesmo plano de Consumo. Atribuir várias aplicações ao mesmo plano de Consumo não tem impacto na resiliência, escalabilidade ou fiabilidade de cada app.

## <a name="next-steps"></a>Próximos passos

+ [Opções de hospedagem de funções Azure Functions](functions-scale.md)
+ [Escalamento orientado para eventos em Funções Azure](event-driven-scaling.md)
